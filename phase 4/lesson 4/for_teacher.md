0–10 мин. Порядок разработки.
Рекомендуемый подход: 1) Схема БД (основа данных), 2) API контракт (что возвращаем), 3) Реализация (логика), 4) Docker (упаковка), 5) CI (автоматизация). На практике Docker можно делать параллельно с реализацией — помогает тестировать.

10–70 мин. Совместная разработка мини-бэкенда.
Строим вместе поэтапно — каждый шаг проверяем:

**Шаг 1: Схема БД**
```sql
CREATE TABLE tasks (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  done BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);
```

**Шаг 2: REST API через Crow**
```cpp
#include "crow.h"
#include <pqxx/pqxx>
#include <nlohmann/json.hpp>
using json = nlohmann::json;

int main() {
    pqxx::connection db("dbname=appdb user=postgres password=secret host=postgres");
    crow::SimpleApp app;

    CROW_ROUTE(app, "/tasks").methods("GET"_method)([&db]{
        pqxx::work txn(db);
        auto rows = txn.exec("SELECT id, title, done FROM tasks ORDER BY created_at");
        json result = json::array();
        for (const auto& row : rows)
            result.push_back({{"id", row[0].as<int>()},
                              {"title", row[1].as<std::string>()},
                              {"done", row[2].as<bool>()}});
        return crow::response(result.dump());
    });

    CROW_ROUTE(app, "/tasks").methods("POST"_method)([&db](const crow::request& req){
        auto body = json::parse(req.body);
        pqxx::work txn(db);
        auto res = txn.exec_params(
            "INSERT INTO tasks(title) VALUES($1) RETURNING id",
            body["title"].get<std::string>());
        txn.commit();
        return crow::response(201, json{{"id", res[0][0].as<int>()}}.dump());
    });

    CROW_ROUTE(app, "/tasks/<int>").methods("DELETE"_method)([&db](int id){
        pqxx::work txn(db);
        txn.exec_params("DELETE FROM tasks WHERE id=$1", id);
        txn.commit();
        return crow::response(204);
    });

    app.port(8080).run();
}
```

**Шаг 3: CMakeLists.txt** с Crow, libpqxx, nlohmann/json.

**Шаг 4: Multi-stage Dockerfile** + docker-compose.yml.

**Шаг 5: GitHub Actions** — build + docker build.

70–90 мин. Тестирование через curl. Выдача финального ДЗ.
