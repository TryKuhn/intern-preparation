### CAP теорема

При network partition нужно выбрать одно из:
- **CP** (Consistent + Partition tolerant): операции блокируются до восстановления связи. Пример: HBase, ZooKeeper.
- **AP** (Available + Partition tolerant): возвращают возможно устаревшие данные. Пример: Cassandra, DynamoDB, Kafka.
- **CA** (Consistent + Available): недостижимо в реальных распределённых системах.

**Реальный мир**: CAP описывает worst case (partition). Обычно системы балансируют C и A в нормальной работе.

| Система | Модель | Комментарий |
|---|---|---|
| PostgreSQL | CA (single node) | Реплики — eventual consistency |
| Kafka | AP | Может отставать от leader |
| Redis | CP (по умолчанию) | При partition блокирует запись |
| Cassandra | AP | Tunable consistency |

### Шардирование

```
Hash sharding: shard = hash(user_id) % num_shards
  user_id 100 → hash(100) % 3 = shard 1
  user_id 201 → hash(201) % 3 = shard 0
```

**Consistent hashing**: серверы и ключи на кольце. При добавлении нового сервера только ~K/N ключей переходят к нему (K = ключи, N = серверов).

### Репликация

```
Leader (primary) — принимает записи, реплицирует на followers
Follower 1, 2, 3 — принимают чтения, отстают на некоторое время

Failover: если leader падёт, один из follower становится новым leader
```

### Map-Reduce (локальная симуляция на C++)

```cpp
#include <map>
#include <vector>
#include <string>

// Map: одна запись → список (ключ, значение)
std::vector<std::pair<std::string, int>>
map_fn(const std::string& log_line) {
    // Извлечь URL из строки лога
    std::string url = extract_url(log_line);
    return {{url, 1}};
}

// Reduce: список значений для ключа → итог
int reduce_fn(const std::string& url, const std::vector<int>& counts) {
    return std::accumulate(counts.begin(), counts.end(), 0);
}

// Framework:
std::map<std::string, std::vector<int>> shuffle;
for (const auto& line : logs) {
    for (auto& [key, val] : map_fn(line))
        shuffle[key].push_back(val);
}
std::map<std::string, int> result;
for (auto& [key, vals] : shuffle)
    result[key] = reduce_fn(key, vals);
```

### Паттерны

**CQRS** (Command Query Responsibility Segregation): разные модели для чтения и записи.

**Event Sourcing**: хранить события, а не состояние. Состояние = применение всех событий.

**Saga**: распределённые транзакции через серию локальных транзакций с компенсирующими действиями.
