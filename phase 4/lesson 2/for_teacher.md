0–10 мин. Контейнер vs ВМ.
ВМ: полная ОС, гипервизор, гигабайты накладных расходов. Контейнер: изолированный процесс через namespaces + cgroups. Разделяют ядро хоста. Изолированы: filesystem (через overlayfs), сеть (netns), процессы (pidns), пользователи (userns). Контейнер стартует за секунды, ВМ — минуты.

10–40 мин. Dockerfile и слои.
```dockerfile
FROM ubuntu:22.04          # базовый образ — слой 1
RUN apt-get update && apt-get install -y g++ cmake  # слой 2
WORKDIR /app               # метаданные
COPY CMakeLists.txt .      # слой 3
COPY src/ src/             # слой 4 (меняется чаще)
RUN cmake -B build && cmake --build build  # слой 5
CMD ["./build/myapp"]      # дефолтная команда
```
Ключевой момент: порядок инструкций влияет на кэш. Если сначала COPY всего, то любое изменение кода инвалидирует все слои. Правильно: сначала CMakeLists.txt (меняется редко), потом src/ (меняется часто). Кэш инвалидируется только начиная с изменённого слоя.

CMD vs ENTRYPOINT:
```dockerfile
ENTRYPOINT ["./myapp"]     # базовая команда, не заменяется при run
CMD ["--help"]             # дефолтные аргументы, заменяются при run
# docker run img --port 8080 → ./myapp --port 8080
```

40–65 мин. Multi-stage build.
```dockerfile
# Stage 1: сборка (1GB+ с компилятором)
FROM ubuntu:22.04 AS builder
RUN apt-get update && apt-get install -y g++ cmake
WORKDIR /build
COPY . .
RUN cmake -B build -DCMAKE_BUILD_TYPE=Release && cmake --build build

# Stage 2: runtime (20MB без компилятора)
FROM ubuntu:22.04 AS runtime
WORKDIR /app
COPY --from=builder /build/build/myapp .
EXPOSE 8080
ENTRYPOINT ["./myapp"]
```
Покажи разницу размеров: `docker images`.

65–80 мин. Volumes, port mapping, env vars.
```bash
docker run -p 8080:8080 -v /data:/app/data -e DB_HOST=localhost myapp
```

80–90 мин. .dockerignore. Выдача ДЗ.
