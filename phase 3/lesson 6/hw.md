### A. Kafka Producer/Consumer (около 60 мин)
1. Запусти Kafka через docker-compose из materials.md.
2. Напиши C++ producer (librdkafka) отправляющий 100 JSON сообщений `{"id": N, "data": "..."}` в топик "test".
3. Напиши C++ consumer читающий из "test" и выводящий каждое сообщение.
4. Убедись что consumer с `auto.offset.reset=earliest` видит все сообщения.

### B. Consumer Groups (около 30 мин)
1. Запусти двух consumers с одинаковым `group.id` — убедись что сообщения делятся между ними.
2. Запусти двух consumers с разными `group.id` — убедись что оба получают ВСЕ сообщения.
3. Объясни в `selfcheck.md` почему второй сценарий называется "replay".

### C. At-least-once и at-most-once (около 30 мин)
1. Продемонстрируй at-least-once: consumer читает сообщение, симулирует сбой (return без commit), перезапускает — видит то же сообщение снова.
2. Сделай processing idempotent: храни set обработанных ID, пропускай дубли.

### D. Сравнение с HTTP (около 20 мин, письменно)
Напиши сравнение: для каждого сценария объясни HTTP vs Kafka:
1. Пользователь регистрируется — нужно послать welcome email.
2. Банковский перевод — нужна гарантия доставки.
3. Аналитика кликов — миллионы событий в секунду.

### Самопроверка (около 20 мин, письменно в `selfcheck.md`)
1. Что такое топик, партиция, offset?
2. Зачем нужны consumer groups?
3. В чём разница at-least-once и at-most-once?
4. Почему Kafka хранит сообщения после чтения?
5. Когда выбрать RabbitMQ вместо Kafka?

### Критерий "сделано"
Producer и consumer работают, consumer groups продемонстрированы, at-least-once с дублями и idempotent processing реализованы.
