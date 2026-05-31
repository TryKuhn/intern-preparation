### A. Concepts (около 50 мин)

1. Перепиши функцию `to_string(x)` из урока 5.2 используя Concepts вместо enable_if. Сравни читаемость.
2. Напиши концепт `Serializable` — тип имеет метод `.serialize() -> std::string`.
3. Напиши концепт `Container` с требованиями: begin/end итераторы, size(), value_type.
4. Реализуй функцию `my_sort(Container auto& c)` через концепт.
5. Напиши функцию с несколькими концептами: `join(Container auto&& c, std::string_view delim)` — конкатенирует элементы с разделителем (элементы должны быть Printable).

### B. CRTP (около 50 мин)

1. Реализуй CRTP mixin `Comparable<Derived>` генерирующий `<, >, <=, >=, ==, !=` из одного метода `compare(const Derived&) -> int`.
2. Реализуй CRTP mixin `Cloneable<Derived>` с `clone() -> std::unique_ptr<Derived>`.
3. Реализуй CRTP `Singleton<Derived>` с `instance() -> Derived&`.
4. Сравни производительность: CRTP mixin vs virtual functions для вызова 10^8 раз. Покажи разницу.

### C. Dependent names и инстанцирование (около 30 мин)

1. Воспроизведи ошибку «not found in first phase» для dependent name. Исправь через `this->` или `Base<T>::`.
2. Воспроизведи ошибку «typename needed» для зависимого типа. Исправь.
3. Напиши класс шаблона в .h, используй из двух .cpp. Добавь explicit instantiation declaration в .h и definition в отдельный .cpp. Убедись что compile time уменьшился (или просто работает).
4. Замерь размер бинарника для `vector<int>`, `vector<double>`, `vector<std::string>` — покажи code bloat.

### D. Итоговое сравнение (около 20 мин, письменно в `selfcheck.md`)

Для каждого инструмента объясни: когда использовать, преимущества, недостатки:
- `enable_if` + SFINAE
- `if constexpr`
- Tag dispatch
- Concepts (C++20)

### Самопроверка (около 15 мин, письменно в `selfcheck.md`)

1. Чем Concepts лучше SFINAE? В каких случаях SFINAE всё равно нужен?
2. Что такое CRTP? В чём преимущество над virtual?
3. Почему шаблоны живут в заголовках?
4. Что такое two-phase lookup?
5. Зачем `typename` перед зависимым именем типа?
6. Что такое explicit instantiation и зачем?

### Критерий "сделано"
Concepts версия to_string читается значительно лучше enable_if версии, CRTP Comparable работает для 3 разных классов, бенчмарк CRTP vs virtual проведён и задокументирован.
