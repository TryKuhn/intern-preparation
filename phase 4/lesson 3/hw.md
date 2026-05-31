### A. docker-compose стек (около 60 мин)
1. Напиши `docker-compose.yml` со стеком: postgres + zookeeper + kafka + твоё C++ приложение.
2. Добавь healthcheck для postgres (pg_isready) и kafka.
3. Убедись что `app` стартует только когда postgres и kafka готовы (condition: service_healthy).
4. Запусти `docker compose up --build`. Проверь через `docker compose ps` что все сервисы healthy.
5. Подключись к postgres внутри compose-сети: `docker compose exec postgres psql -U postgres`.

### B. Сети и DNS (около 20 мин)
1. Добавь в compose две сети: `backend` (app + postgres + kafka) и `monitoring` (app + prometheus).
2. Убедись что из контейнера `app` ping до `postgres` работает по имени: `docker compose exec app ping postgres`.
3. Объясни почему приложение подключается к `DB_HOST=postgres`, а не `localhost`.

### C. GitHub Actions (около 40 мин)
1. Создай в проекте `.github/workflows/ci.yml` — сборка + тесты на push в main.
2. Добавь матрицу сборки:
   ```yaml
   strategy:
     matrix:
       compiler: [g++, clang++]
       build_type: [Debug, Release]
   ```
3. Добавь шаг кэширования зависимостей через `actions/cache`.
4. Добавь шаг запуска тестов через docker-compose:
   ```yaml
   - run: docker compose up -d postgres kafka
   - run: sleep 30 && ctest --test-dir build
   - run: docker compose down
   ```

### Самопроверка (около 15 мин, письменно в `selfcheck.md`)
1. Зачем compose вместо нескольких `docker run`?
2. Как контейнеры общаются друг с другом в compose? По какому имени?
3. Чем `depends_on: service_started` отличается от `service_healthy`?
4. Что такое CI? Чем отличается от CD?
5. Что происходит при `docker compose up --build`?

### Критерий "сделано"
Стек из 4 сервисов запускается одной командой, все healthy, GitHub Actions workflow запускается при push.
