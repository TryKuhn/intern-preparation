0–5 мин. Шаблонизатор vs конкатенация.
Конкатенация: смешивает логику и представление, сложно читать, легко создать XSS. Шаблонизатор: чёткое разделение, поддерживаемость, возможность auto-escape HTML.

5–30 мин. Jinja2 синтаксис.
Три типа тегов:
- `{{ variable }}` — вывод значения
- `{% for / if / block %}` — управляющие конструкции
- `{# comment #}` — комментарий

Фильтры: `{{ name | upper }}`, `{{ date | format }}`.
Наследование шаблонов: `{% extends "base.html" %}` + `{% block content %}`.

30–65 мин. inja в C++.
```cpp
#include <inja/inja.hpp>
#include <nlohmann/json.hpp>
using json = nlohmann::json;

inja::Environment env;

// Базовый рендеринг:
json data = {{"name", "Alice"}, {"age", 30}};
std::string result = env.render("Hello, {{ name }}! You are {{ age }} years old.", data);

// Из файла:
json tasks = {{"tasks", json::array({
    {{"id", 1}, {"title", "Fix bug"}, {"done", false}},
    {{"id", 2}, {"title", "Write tests"}, {"done", true}}
})}};
std::string html = env.render_file("template.html", tasks);

// Шаблон template.html:
// {% for task in tasks %}
//   <li class="{% if task.done %}done{% endif %}">{{ task.title }}</li>
// {% endfor %}
```

65–82 мин. Генерация кода через шаблоны (кодоген).
Шаблон C++ структуры из JSON-схемы. Практическое применение в реальных проектах.
Другие движки: mustache (логика-free), mstch (C++ mustache). Выдача ДЗ.
