### Что такое шаблонизатор

Шаблонизатор разделяет:
- **Данные** (JSON, структуры) — логика программы
- **Представление** (HTML, конфиги, код) — текстовые шаблоны

Это обеспечивает:
- Читаемость и поддерживаемость
- Повторное использование шаблонов
- Потенциальное auto-escape (защита от XSS)

### inja — Jinja2 для C++

```cpp
#include <inja/inja.hpp>
#include <nlohmann/json.hpp>
using json = nlohmann::json;
using namespace inja;

Environment env;

// Простая подстановка:
json data = {{"name", "World"}};
std::string r = env.render("Hello, {{ name }}!", data);
// "Hello, World!"

// Условие:
json d = {{"admin", true}};
env.render("{% if admin %}Welcome, admin!{% else %}Hello, user!{% endif %}", d);

// Цикл:
json list = {{"items", {"apple", "banana", "cherry"}}};
env.render("{% for item in items %}- {{ item }}\n{% endfor %}", list);

// Вложенные объекты:
json task = {{"task", {{"title", "Buy milk"}, {"done", false}}}};
env.render("{{ task.title }}: {% if task.done %}✓{% else %}✗{% endif %}", task);
```

### Загрузка шаблонов из файлов

```cpp
env.set_search_paths({"templates/"});
json data = ...;
std::string html = env.render_file("index.html", data);
```

### Пример: генерация HTML страницы

```
// templates/tasks.html:
<!DOCTYPE html><html><body>
<h1>Tasks</h1>
<ul>
{% for task in tasks %}
  <li {% if task.done %}class="done"{% endif %}>
    {{ loop.index }}. {{ task.title }}
  </li>
{% endfor %}
</ul>
<p>Total: {{ tasks | length }}</p>
</body></html>
```

```cpp
json data = {{"tasks", {
    {{"title", "Finish project"}, {"done", false}},
    {{"title", "Write tests"}, {"done", true}}
}}};
std::string html = env.render_file("tasks.html", data);
```

### Генерация конфигов

Шаблон `nginx.conf.tmpl`:
```
server {
    listen {{ port }};
    server_name {{ domain }};
    {% for location in locations %}
    location {{ location.path }} { proxy_pass {{ location.upstream }}; }
    {% endfor %}
}
```

### Кодогенерация

Генерация C++ структур из JSON-схемы — common pattern в enterprise:
```
// schema.json: [{name: "Task", fields: [{name: "id", type: "int32"}, ...]}]
// struct.cpp.tmpl: struct {{ struct.name }} { {% for f in struct.fields %} {{ f.type }} {{ f.name }}; {% endfor %} };
```
