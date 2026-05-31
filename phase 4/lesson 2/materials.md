### Образ, контейнер, реестр

- **Image**: неизменяемый шаблон (слои). Хранится локально или в registry (Docker Hub, ECR, GCR).
- **Container**: запущенный экземпляр image. Имеет writeable слой поверх image.
- **Registry**: хранилище images. `docker pull ubuntu:22.04` скачивает из Docker Hub.

```bash
docker pull ubuntu:22.04        # скачать image
docker images                   # список локальных images
docker run ubuntu:22.04 ls /   # запустить и выполнить команду
docker ps                       # запущенные контейнеры
docker ps -a                    # все (включая остановленные)
docker stop <id>                # остановить
docker rm <id>                  # удалить контейнер
docker rmi <image>              # удалить image
```

### Dockerfile

```dockerfile
FROM ubuntu:22.04                           # базовый образ
RUN apt-get update -y && \                  # объединять RUN для меньшего числа слоёв
    apt-get install -y g++ cmake libpqxx-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/*  # очистить кэш apt

WORKDIR /app                                # рабочая директория

# Сначала файлы, меняющиеся РЕДКО:
COPY CMakeLists.txt .
RUN cmake -B build                          # кэшируется если CMakeLists не менялся

# Потом файлы, меняющиеся ЧАСТО:
COPY src/ src/
RUN cmake --build build --config Release

EXPOSE 8080                                 # документация (не открывает порт)
ENV LOG_LEVEL=info                          # переменная окружения
CMD ["./build/myapp"]                       # дефолтная команда
```

### CMD vs ENTRYPOINT

```dockerfile
# Только CMD — можно полностью заменить:
CMD ["./myapp", "--port", "8080"]
# docker run img bash → запустит bash вместо myapp

# Только ENTRYPOINT — нельзя заменить без --entrypoint:
ENTRYPOINT ["./myapp"]
CMD ["--port", "8080"]              # дефолтные аргументы
# docker run img --port 9090 → ./myapp --port 9090
# docker run img → ./myapp --port 8080
```

### Multi-stage build

```dockerfile
FROM ubuntu:22.04 AS builder
RUN apt-get update && apt-get install -y g++ cmake
WORKDIR /build
COPY . .
RUN cmake -B build -DCMAKE_BUILD_TYPE=Release && \
    cmake --build build -j$(nproc)

FROM ubuntu:22.04
RUN apt-get update && apt-get install -y libpq5 && apt-get clean
WORKDIR /app
COPY --from=builder /build/build/myapp ./myapp
ENTRYPOINT ["./myapp"]
```

Builder image: ~1 GB. Runtime image: ~70 MB. Только бинарник и runtime-зависимости.

### .dockerignore

```
build/
.git/
*.o
*.d
.DS_Store
```

### Volumes и сеть

```bash
docker run \
  -p 8080:8080 \               # host:container порт
  -v /host/data:/app/data \    # volume mount
  -e DB_HOST=localhost \        # переменная окружения
  --name mycontainer \
  myapp:latest
```

Volume нужен для персистентных данных (БД) — иначе данные теряются при удалении контейнера.
