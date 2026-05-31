Цель пре-ридинга — понять зачем нужна очередь сообщений. Около 20 минут.

1. Установить Docker: `docker --version`. Запустить Kafka:
   ```bash
   # docker-compose.yml будет на занятии
   docker run -d --name zookeeper -p 2181:2181 confluentinc/cp-zookeeper:latest
   docker run -d --name kafka -p 9092:9092 --link zookeeper \
     -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
     -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
     confluentinc/cp-kafka:latest
   ```
2. Прочитать по диагонали:
    - Message broker: посредник между сервисами. Асинхронная коммуникация.
    - Producer: отправляет сообщения. Consumer: читает.
3. Подумать над вопросом: чем очередь сообщений отличается от HTTP-запроса? В каком случае лучше использовать очередь?
