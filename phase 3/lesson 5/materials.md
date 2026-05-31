### SQL: основные операции

```sql
-- DDL:
CREATE TABLE tasks (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  done BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);
CREATE INDEX idx_tasks_user_id ON tasks(user_id);

-- DML:
INSERT INTO tasks (user_id, title) VALUES (1, 'Fix bug') RETURNING id;
SELECT * FROM tasks WHERE NOT done ORDER BY created_at DESC LIMIT 10;
UPDATE tasks SET done = TRUE WHERE id = $1;
DELETE FROM tasks WHERE user_id = $1;

-- JOIN:
SELECT u.name, COUNT(t.id) as task_count
FROM users u
LEFT JOIN tasks t ON t.user_id = u.id
GROUP BY u.id, u.name
ORDER BY task_count DESC;
```

### ACID

| Свойство | Что означает |
|---|---|
| Atomicity | Транзакция выполняется целиком или не выполняется вообще |
| Consistency | После транзакции БД в консистентном состоянии (ограничения выполнены) |
| Isolation | Промежуточное состояние транзакции невидимо другим |
| Durability | После COMMIT данные сохранены даже при сбое |

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
-- Если ошибка:
ROLLBACK;
-- Всё ок:
COMMIT;
```

### Уровни изоляции

```sql
BEGIN ISOLATION LEVEL SERIALIZABLE;
-- Защищает от dirty read, non-repeatable read, phantom read
```

### libpqxx

```cpp
#include <pqxx/pqxx>

pqxx::connection conn("dbname=practice user=student password=pass");

// Транзакция (commit на выходе из scope):
{
    pqxx::work txn(conn);
    
    // Параметризованный запрос (защита от SQL injection):
    txn.exec_params(
        "INSERT INTO tasks(user_id, title) VALUES($1, $2)",
        user_id, title
    );
    
    // Запрос с результатом:
    auto rows = txn.exec_params(
        "SELECT id, title FROM tasks WHERE user_id = $1",
        user_id
    );
    for (const auto& row : rows) {
        int id = row[0].as<int>();
        std::string title = row[1].as<std::string>();
    }
    
    txn.commit();
}  // если без commit → ROLLBACK при уничтожении
```

### Индексы

B-дерево: O(log n) поиск по индексированному столбцу. Ускоряет WHERE, JOIN, ORDER BY.
```sql
EXPLAIN ANALYZE SELECT * FROM tasks WHERE user_id = 5;
-- Показывает план запроса: Seq Scan (без индекса) vs Index Scan (с индексом)
```
