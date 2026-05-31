### docker-compose.yml — структура

```yaml
version: '3.8'
services:
  имя_сервиса:
    image: ubuntu:22.04             # готовый образ
    build: ./path                   # или собрать из Dockerfile
    build:
      context: .
      dockerfile: Dockerfile.prod
    ports:
      - "8080:8080"                 # host:container
    environment:
      - DB_HOST=postgres            # переменная окружения
      DB_PORT: 5432                 # альтернативный синтаксис
    env_file:
      - .env                        # из файла
    volumes:
      - mydata:/app/data            # именованный volume
      - ./config:/app/config:ro     # bind mount (только чтение)
    networks:
      - mynet
    depends_on:
      postgres:
        condition: service_healthy  # ждать healthcheck
    healthcheck:
      test: ["CMD-SHELL", "curl -f http://localhost:8080/health || exit 1"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s            # grace period перед первой проверкой
    restart: unless-stopped        # перезапускать при падении

volumes:
  mydata:                           # именованный volume (персистентный)

networks:
  mynet:                            # кастомная сеть (DNS по имени сервиса)
```

### Полный стек C++ + Postgres + Kafka

```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes: [pgdata:/var/lib/postgresql/data]
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
      interval: 5s; retries: 10

  zookeeper:
    image: confluentinc/cp-zookeeper:7.5.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    depends_on: [zookeeper]
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "true"
    healthcheck:
      test: ["CMD", "kafka-broker-api-versions", "--bootstrap-server", "localhost:9092"]
      interval: 10s; retries: 15

  app:
    build: .
    ports: ["8080:8080"]
    environment:
      DB_HOST: postgres
      DB_PORT: "5432"
      KAFKA_BROKERS: kafka:9092
    depends_on:
      postgres: {condition: service_healthy}
      kafka: {condition: service_healthy}

volumes:
  pgdata:
```

```bash
docker compose up --build          # собрать и запустить
docker compose up -d               # в фоне
docker compose logs -f app         # следить за логами
docker compose down                # остановить и удалить контейнеры
docker compose down -v             # + удалить volumes
```

### GitHub Actions CI

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push: {branches: [main, develop]}
  pull_request: {branches: [main]}

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: |
          sudo apt-get update -y
          sudo apt-get install -y cmake g++-12 libpqxx-dev

      - name: Configure CMake
        run: cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER=g++-12

      - name: Build
        run: cmake --build build -j$(nproc)

      - name: Test
        run: cd build && ctest --output-on-failure

  docker:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
```
