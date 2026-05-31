0–10 мин. depends_on vs healthcheck.
depends_on контролирует порядок запуска, но Kafka/Postgres могут быть «запущены» как контейнер, но ещё не готовы принимать соединения. Healthcheck позволяет задать критерий готовности. `depends_on: condition: service_healthy` ждёт именно healthcheck.

10–40 мин. docker-compose.yml — стек «C++ сервис + Postgres + Kafka».
```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: myapp
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
      interval: 5s
      timeout: 5s
      retries: 10

  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      zookeeper:
        condition: service_started
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
    healthcheck:
      test: ["CMD", "kafka-topics", "--bootstrap-server", "localhost:9092", "--list"]
      interval: 10s
      retries: 10

  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      DB_HOST: postgres
      KAFKA_BROKERS: kafka:9092
    depends_on:
      postgres:
        condition: service_healthy
      kafka:
        condition: service_healthy
    networks:
      - backend

volumes:
  pgdata:

networks:
  backend:
```
Ключевые моменты:
- Сервисы общаются по DNS-имени сервиса (postgres, kafka) внутри docker сети
- Volume pgdata персистентен
- app зависит от healthy postgres и kafka

40–65 мин. GitHub Actions CI.
```yaml
# .github/workflows/ci.yml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: sudo apt-get update && sudo apt-get install -y cmake g++ libpqxx-dev
      - name: Build
        run: cmake -B build && cmake --build build -j4
      - name: Run tests
        run: cd build && ctest --output-on-failure
```
Объясни: trigger (on push/PR), runner (ubuntu-latest), steps. CD — доставка в prod (Continuous Deployment). CI — только сборка и тесты.

65–82 мин. Частые вопросы: зачем compose вместо docker run, DNS между контейнерами, что происходит при `docker compose up`. Выдача ДЗ.
