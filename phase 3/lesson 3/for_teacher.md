0–5 мин. Авторизация в stateless REST.
JWT: клиент посылает токен в каждом запросе. Сервер проверяет подпись — не нужно хранить сессии. Или API-ключ в заголовке. Stateless = масштабируемость: любой экземпляр сервера может обработать запрос.

5–25 мин. TCP/IP и HTTP lifecycle.
Нарисуй стек: Application (HTTP) → Transport (TCP) → Network (IP) → Data Link.
HTTP/1.1: текстовый протокол. HTTP/2: бинарный, мультиплексирование. HTTP/3: поверх QUIC (UDP).
Жизненный цикл HTTP-запроса: DNS lookup → TCP connect (3-way handshake) → TLS handshake (если HTTPS) → HTTP request → response.
Идемпотентность: GET, PUT, DELETE — идемпотентны. POST — нет. Важно для retry-логики.

25–50 мин. Protocol Buffers.
```protobuf
// person.proto
syntax = "proto3";
message Person {
  string name = 1;
  int32 age = 2;
  repeated string emails = 3;
}
```
```bash
protoc --cpp_out=. person.proto
```
```cpp
Person p; p.set_name("Alice"); p.set_age(30);
std::string bytes; p.SerializeToString(&bytes);
Person p2; p2.ParseFromString(bytes);
```
Varint кодирование: маленькие числа → меньше байт. Эволюция схемы: никогда не переиспользовать field numbers (удалённое поле).

50–70 мин. gRPC.
```protobuf
service UserService {
  rpc GetUser(GetUserRequest) returns (User);
  rpc ListUsers(ListUsersRequest) returns (stream User);
}
```
4 типа: unary, server streaming, client streaming, bidirectional streaming.
HTTP/2 под капотом. Дедлайны: `context.WithTimeout`. Метаданные: как HTTP заголовки.

70–82 мин. Живая практика с curl. Выдача ДЗ.
