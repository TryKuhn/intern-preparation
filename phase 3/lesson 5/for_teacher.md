0–5 мин. Проблема потерянного обновления.
Транзакции с уровнем изоляции SERIALIZABLE или использование `SELECT FOR UPDATE` блокирует строку — второй пользователь ждёт. После коммита первого, второй видит обновлённые данные.

5–30 мин. SQL: CRUD и JOIN.
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email TEXT UNIQUE
);
CREATE TABLE tasks (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
  title TEXT,
  done BOOLEAN DEFAULT FALSE
);

INSERT INTO users (name, email) VALUES ('Alice', 'alice@mail.com');
SELECT u.name, t.title FROM users u JOIN tasks t ON t.user_id = u.id WHERE NOT t.done;
UPDATE tasks SET done = TRUE WHERE id = 5;
DELETE FROM users WHERE id = 10;
```
Объясни JOIN: INNER (пересечение), LEFT (все из left + совпавшие из right), RIGHT.
Индексы B-дерево: `CREATE INDEX idx_tasks_user ON tasks(user_id);` — ускоряет поиск по foreign key.

30–55 мин. ACID и уровни изоляции.
- **A**tomicity: всё или ничего.
- **C**onsistency: транзакция не нарушает ограничения.
- **I**solation: транзакции изолированы друг от друга.
- **D**urability: после COMMIT данные сохранены.

Уровни изоляции (PostgreSQL): READ COMMITTED (default), REPEATABLE READ, SERIALIZABLE.
Проблемы: dirty read, non-repeatable read, phantom read.

55–75 мин. libpqxx — C++ клиент.
```cpp
#include <pqxx/pqxx>
try {
    pqxx::connection conn("dbname=practice user=student password=pass host=localhost");
    pqxx::work txn(conn);
    auto res = txn.exec("SELECT id, name FROM users WHERE id > 0");
    for (const auto& row : res)
        std::cout << row["id"].as<int>() << " " << row["name"].as<std::string>() << "\n";
    txn.exec_params("INSERT INTO tasks(user_id, title) VALUES($1, $2)", 1, "New task");
    txn.commit();
} catch (const pqxx::sql_error& e) {
    std::cerr << e.query() << ": " << e.what();
}
```

75–90 мин. Нормализация кратко. Выдача ДЗ.
