### Архитектура мини-бэкенда

```
┌─────────────────────────────────────────┐
│          docker-compose стек            │
│                                         │
│  ┌──────────────┐  ┌────────────────┐   │
│  │  app:8080    │  │  postgres:5432 │   │
│  │  C++ + Crow  │──│  tasks table   │   │
│  └──────────────┘  └────────────────┘   │
└─────────────────────────────────────────┘
         ↑
    curl/HTTP клиент
```

### REST API контракт

```
GET    /tasks        → 200 JSON array
POST   /tasks        → 201 JSON {id}       Body: {"title": "..."}
GET    /tasks/{id}   → 200 JSON task | 404
PATCH  /tasks/{id}   → 200 JSON task       Body: {"done": true}
DELETE /tasks/{id}   → 204 No Content
GET    /health       → 200 {"status": "ok"}
```

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.20)
project(TaskAPI CXX)
set(CMAKE_CXX_STANDARD 17)

find_package(Crow REQUIRED)
find_package(PostgreSQL REQUIRED)
find_package(nlohmann_json REQUIRED)

add_executable(taskapi main.cpp)
target_link_libraries(taskapi PRIVATE
  Crow::Crow
  pqxx
  nlohmann_json::nlohmann_json
)
```

### Multi-stage Dockerfile

```dockerfile
FROM ubuntu:22.04 AS builder
RUN apt-get update -y && apt-get install -y \
    cmake g++ libpqxx-dev nlohmann-json3-dev \
    libboost-all-dev libssl-dev && apt-get clean
WORKDIR /build
COPY CMakeLists.txt .
# Crow header-only — скопировать
COPY crow/ crow/
RUN cmake -B cmake_build
COPY src/ src/
RUN cmake --build cmake_build -j$(nproc)

FROM ubuntu:22.04
RUN apt-get update -y && apt-get install -y libpq5 && apt-get clean
WORKDIR /app
COPY --from=builder /build/cmake_build/taskapi ./taskapi
EXPOSE 8080
ENTRYPOINT ["./taskapi"]
```

### docker-compose.yml

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

  app:
    build: .
    ports: ["8080:8080"]
    environment:
      DB_CONN: "dbname=appdb user=postgres password=secret host=postgres"
    depends_on:
      postgres: {condition: service_healthy}

volumes:
  pgdata:
```

### Тестирование через curl

```bash
# Создать задачу:
curl -X POST http://localhost:8080/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy milk"}'

# Получить список:
curl http://localhost:8080/tasks

# Отметить выполненной:
curl -X PATCH http://localhost:8080/tasks/1 \
  -d '{"done": true}'

# Удалить:
curl -X DELETE http://localhost:8080/tasks/1
```
