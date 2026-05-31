### A. HTTP и curl (около 30 мин)
1. Используй `curl` для запросов к публичному API (jsonplaceholder.typicode.com):
   - GET /posts (список постов)
   - GET /posts/1 (один пост)
   - POST /posts с JSON телом
   - DELETE /posts/1
2. Используй `-v` флаг — изучи заголовки запроса и ответа.
3. Напиши bash-скрипт делающий все 4 типа запросов и выводящий статус-коды.

### B. Protocol Buffers (около 50 мин)
1. Напиши `.proto` для простой задачи: `Task { id, title, done, tags[] }` и `User { id, name, email }`.
2. Скомпилируй через `protoc --cpp_out=. task.proto`.
3. Напиши C++ программу сериализующую и десериализующую 100 задач.
4. Сравни размер JSON-представления тех же данных с protobuf. Выведи коэффициент сжатия.
5. Удали поле из .proto (например tags), добавь новое с новым номером. Убедись что старые бинарные данные всё ещё читаются.

### C. gRPC (около 50 мин)
1. Напиши .proto с unary RPC `GetTask(GetTaskRequest) returns (Task)`.
2. Реализуй сервер (реализует TaskService::Service).
3. Реализуй клиент вызывающий GetTask.
4. Добавь server-streaming RPC `ListTasks(Empty) returns (stream Task)`.
5. Добавь таймаут на клиенте через `context.set_deadline`.

### Самопроверка (около 20 мин, письменно в `selfcheck.md`)
1. Чем REST отличается от gRPC?
2. Почему нельзя переиспользовать field numbers в protobuf?
3. Что такое idempotency? Зачем важна для retry?
4. Как JWT решает проблему stateless авторизации?
5. Чем HTTP/2 отличается от HTTP/1.1?

### Критерий "сделано"
Protobuf сериализация/десериализация работает, gRPC сервер отвечает на запросы, сравнение JSON vs protobuf проведено.
