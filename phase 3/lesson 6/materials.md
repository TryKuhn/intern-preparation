### Kafka — ключевые концепции

```
Topic "orders":
  Partition 0: [msg0, msg1, msg4, msg7, ...]  offset: 0,1,2,...
  Partition 1: [msg2, msg5, msg8, ...]
  Partition 2: [msg3, msg6, msg9, ...]

Consumer Group "payments":
  Consumer A → Partition 0
  Consumer B → Partition 1
  Consumer C → Partition 2

Consumer Group "analytics":
  Consumer X → Partition 0, 1, 2  (один consumer, читает все)
```

**Retention**: сообщения хранятся по времени (7 дней по умолчанию) или размеру, независимо от того прочитаны ли они.

### Docker Compose для Kafka

```yaml
# docker-compose.yml
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on: [zookeeper]
    ports: ["9092:9092"]
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

### librdkafka Producer

```cpp
#include <librdkafka/rdkafkacpp.h>

std::string errstr;
auto* conf = RdKafka::Conf::create(RdKafka::Conf::CONF_GLOBAL);
conf->set("bootstrap.servers", "localhost:9092", errstr);
conf->set("acks", "all", errstr);

auto* producer = RdKafka::Producer::create(conf, errstr);
std::string msg = R"({"event":"user_signup","user_id":42})";
producer->produce("events", RdKafka::Topic::PARTITION_UA,
                  RdKafka::Producer::RK_MSG_COPY,
                  (void*)msg.data(), msg.size(), nullptr, nullptr);
producer->flush(10'000);
delete producer; delete conf;
```

### librdkafka Consumer (manual commit)

```cpp
conf->set("group.id", "my-consumer-group", errstr);
conf->set("auto.offset.reset", "earliest", errstr);
conf->set("enable.auto.commit", "false", errstr);  // manual commit!

auto* consumer = RdKafka::KafkaConsumer::create(conf, errstr);
consumer->subscribe({"events"});

while (true) {
    auto* msg = consumer->consume(1000);
    if (!msg->err()) {
        std::string payload(static_cast<char*>(msg->payload()), msg->len());
        process(payload);
        consumer->commitSync(msg);  // commit после успешной обработки
    }
    delete msg;
}
```

### Kafka vs RabbitMQ

| | Kafka | RabbitMQ |
|---|---|---|
| Модель | Лог (сообщения хранятся) | Очередь (сообщение удаляется после ACK) |
| Потребители | Consumer groups, replay | Конкурирующие consumers |
| Throughput | Очень высокий | Высокий |
| Latency | Выше (батчи) | Ниже |
| Использование | Event streaming, аналитика | Task queues, RPC |

### At-least/at-most/exactly-once

- **At-most-once**: commit before process → потери при сбое
- **At-least-once**: commit after process → дубли при сбое (рекомендуется + idempotent processing)
- **Exactly-once**: transactional API → сложно, дорого
