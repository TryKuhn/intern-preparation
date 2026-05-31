0–10 мин. CAP и Spanner.
CAP говорит: при network partition (P) нужно выбрать C или A. Spanner использует TrueTime API (атомарные часы + GPS) чтобы минимизировать время partitions. На практике partitions случаются редко → Spanner почти всегда CP, но при partition деградирует gracefully. CAP — это не «выбрать навсегда», а «что делать при partition».

10–35 мин. Шардирование и репликация.
Шардирование: делим данные на части. Shard key — ключ разбиения.
- Range sharding: shard 1 = id 0-1M, shard 2 = id 1M-2M. Проблема: hotspot.
- Hash sharding: shard = hash(id) % N. Равномерно, нет range queries.
- Consistent hashing: при добавлении нового шарда перераспределяется минимум данных.

Репликация: primary + N secondary. Primary — все записи. Secondary — чтения (eventually consistent) или failover.

35–60 мин. Map-Reduce интуиция.
```
Входные данные: миллиарды записей (access logs)
Задача: посчитать количество запросов по каждому URL

Map функция:
  Вход: строка лога
  Выход: (URL, 1) пары

Shuffle/Sort: группируем все пары по ключу (URL)
  URL_A → [1,1,1,1,1]
  URL_B → [1,1,1]

Reduce функция:
  Вход: (URL, [1,1,1,...])
  Выход: (URL, count)
```
Реализуй локально: `map_phase(records)` + `reduce_phase(mapped)` в C++ с std::map.

60–75 мин. Дополнительные паттерны: CQRS, Event Sourcing, saga. Выдача ДЗ.
