### Капстоун: мини task-manager бэкенд

Это финальный проект фазы 4. Результат — работающий REST API в Docker-контейнере с CI.

### Шаг 1: БД и API контракт (около 30 мин)
1. Создай схему: таблица `tasks` (id, title, done, created_at).
2. Напиши SQL миграцию в файле `schema.sql` — будет выполняться при первом запуске.
3. Задокументируй API контракт в `API.md`.

### Шаг 2: C++ реализация (около 90 мин)
Реализуй все 5 эндпоинтов:
1. `GET /tasks` — вернуть все задачи.
2. `POST /tasks` — создать задачу. Body: `{"title": "..."}`. Ответ: 201 + `{"id": N}`.
3. `GET /tasks/{id}` — вернуть одну задачу или 404.
4. `PATCH /tasks/{id}` — обновить `done`. Ответ: 200 + обновлённая задача.
5. `DELETE /tasks/{id}` — удалить. Ответ: 204.
6. `GET /health` — `{"status": "ok"}` для healthcheck.

Требования:
- Параметризованные запросы (защита от SQL injection).
- Корректные HTTP статус-коды.
- JSON Content-Type заголовок в ответах.

### Шаг 3: CMake и сборка (около 20 мин)
1. Напиши `CMakeLists.txt` со всеми зависимостями.
2. Убедись что сборка работает локально: `cmake -B build && cmake --build build`.

### Шаг 4: Docker (около 30 мин)
1. Напиши multi-stage Dockerfile.
2. Напиши `docker-compose.yml` с postgres + app.
3. Добавь init.sql через volume mount для создания таблицы при первом запуске:
   ```yaml
   postgres:
     volumes:
       - pgdata:/var/lib/postgresql/data
       - ./schema.sql:/docker-entrypoint-initdb.d/schema.sql
   ```
4. `docker compose up --build` → всё работает.

### Шаг 5: GitHub Actions CI (около 20 мин)
1. Создай `.github/workflows/ci.yml`.
2. Jobs: build (cmake), docker-build.
3. Пуш в GitHub — убедись что CI зелёный.

### Финальная проверка
```bash
docker compose up --build -d
curl -X POST http://localhost:8080/tasks -d '{"title":"test"}'
curl http://localhost:8080/tasks
curl -X PATCH http://localhost:8080/tasks/1 -d '{"done":true}'
curl -X DELETE http://localhost:8080/tasks/1
curl http://localhost:8080/tasks
docker compose down
```

### Критерий "сделано"
Все 5 эндпоинтов работают, данные персистентны между рестартами (через volume), GitHub Actions зелёный, multi-stage образ < 200 МБ.
