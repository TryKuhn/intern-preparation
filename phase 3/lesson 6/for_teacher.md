0–10 мин. Очередь vs HTTP.
HTTP: синхронный, вызывающий ждёт ответа, tight coupling (получатель должен быть доступен). Очередь: асинхронный, loose coupling, получатель может быть недоступен — сообщение хранится. Применение: обработка платежей (не блокировать пользователя), email-рассылки, события аналитики.

10–40 мин. Kafka концепции.
- **Topic**: именованный канал. Сообщения в логе.
- **Partition**: тема разбита на партиции. Партиция = упорядоченный лог. Позволяет параллельную обработку.
- **Consumer Group**: группа потребителей. Каждая партиция читается одним consumer'ом в группе. Несколько групп = replay сообщений.
- **Offset**: позиция в партиции. Consumer коммитит offset после обработки → при рестарте продолжает с той же позиции.
- **Retention**: сообщения хранятся заданное время (не удаляются при чтении — в отличие от очереди).
At-least-once: commit offset после обработки → при сбое может обработать дважды.
At-most-once: commit offset до обработки → при сбое может потерять.
Exactly-once: сложно, требует idempotent producer и transactional API.

40–70 мин. librdkafka Producer и Consumer.
```cpp
// Producer:
RdKafka::Conf* conf = RdKafka::Conf::create(RdKafka::Conf::CONF_GLOBAL);
conf->set("bootstrap.servers", "localhost:9092", errstr);
RdKafka::Producer* producer = RdKafka::Producer::create(conf, errstr);
RdKafka::Topic* topic = RdKafka::Topic::create(producer, "test-topic", nullptr, errstr);
producer->produce(topic, 0, RdKafka::Producer::RK_MSG_COPY,
                  const_cast<char*>("hello"), 5, nullptr, nullptr);
producer->flush(10000);

// Consumer:
conf->set("group.id", "my-group", errstr);
conf->set("auto.offset.reset", "earliest", errstr);
RdKafka::KafkaConsumer* consumer = RdKafka::KafkaConsumer::create(conf, errstr);
consumer->subscribe({"test-topic"});
while (running) {
    auto msg = consumer->consume(1000);
    if (msg->err() == RdKafka::ERR_NO_ERROR) {
        std::cout << (char*)msg->payload();
        // Manual commit: consumer->commitSync(msg);
    }
    delete msg;
}
```

70–82 мин. RabbitMQ сравнение. Выдача ДЗ.
