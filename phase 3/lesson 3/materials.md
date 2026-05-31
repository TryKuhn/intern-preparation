### HTTP методы и семантика

| Метод | Действие | Идемпотентный? | Тело запроса |
|---|---|---|---|
| GET | Получить ресурс | Да | Нет |
| POST | Создать ресурс | Нет | Да |
| PUT | Заменить ресурс | Да | Да |
| PATCH | Частично обновить | Нет (обычно) | Да |
| DELETE | Удалить | Да | Нет |

**Статус-коды:**
- 200 OK, 201 Created, 204 No Content
- 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 422 Unprocessable
- 500 Internal Server Error, 503 Service Unavailable

### REST ресурсы

```
GET    /users           — список пользователей
POST   /users           — создать пользователя
GET    /users/{id}      — конкретный пользователь
PUT    /users/{id}      — обновить полностью
PATCH  /users/{id}      — обновить частично
DELETE /users/{id}      — удалить
```

### Protocol Buffers

```protobuf
syntax = "proto3";
package myapp;

message Task {
  int32 id = 1;        // field number — НИКОГДА не переиспользовать
  string title = 2;
  bool done = 3;
  repeated string tags = 4;
}

message CreateTaskRequest { string title = 1; }
message CreateTaskResponse { Task task = 1; }
```

```bash
protoc --cpp_out=. task.proto
# Генерирует task.pb.h и task.pb.cc
```

```cpp
#include "task.pb.h"
Task t; t.set_id(1); t.set_title("fix bug"); t.set_done(false);
std::string bytes; t.SerializeToString(&bytes);
// bytes << меньше JSON, бинарный, быстрый парсинг

Task t2; t2.ParseFromString(bytes);
```

**JSON vs Protobuf:** JSON — читаемый, медленный. Protobuf — компактный (~3-10x), быстрый (~5-100x), требует схему.

### gRPC

```protobuf
service TaskService {
  rpc CreateTask(CreateTaskRequest) returns (CreateTaskResponse);
  rpc ListTasks(ListTasksRequest) returns (stream Task);  // server streaming
}
```

```cpp
// Клиент:
auto channel = grpc::CreateChannel("localhost:50051", grpc::InsecureChannelCredentials());
auto stub = TaskService::NewStub(channel);

grpc::ClientContext ctx;
ctx.set_deadline(std::chrono::system_clock::now() + std::chrono::seconds(5));
CreateTaskRequest req; req.set_title("hello");
CreateTaskResponse resp;
auto status = stub->CreateTask(&ctx, req, &resp);
```

**REST vs gRPC:**
- REST: простой, HTTP, JSON, читаем браузером
- gRPC: быстрый, бинарный, типизированный, streaming, сложнее отладка
