### A. Вывод аргументов и свёртка (около 40 мин)

Для каждого вызова определи тип T и тип параметра. Проверь через `static_assert(std::is_same_v<...>)`:
```cpp
template<typename T> void f(T x);
template<typename T> void g(T& x);
template<typename T> void h(T&& x);

int i = 5; const int ci = 5; int& ri = i;
f(i);  f(ci);  f(42);  f(ri);
g(i);  g(ci);  // g(42) — ошибка
h(i);  h(ci);  h(42);  h(std::move(i));
```

### B. Variadic шаблоны (около 50 мин)

1. Реализуй `print(args...)` через fold expression — выводит все аргументы через пробел.
2. Реализуй `sum(args...)` для произвольного числа аргументов любых типов.
3. Реализуй `all_of(pred, args...)` — возвращает true если предикат верен для всех.
4. Реализуй `tuple_like<T...>` — хранит значения через рекурсию: `Head` + `Tail...`. Реализуй `get<N>()`.
5. Реализуй `make_array(args...)` — создаёт `std::array` с выведенным типом.

### C. Специализация (около 30 мин)

1. Напиши `template<typename T> struct Serialize` с методом `to_string()`. Сделай частичные специализации для: `T*`, `std::vector<T>`, `std::optional<T>`.
2. Покажи что функции нельзя частично специализировать — создай шаблон функции и попробуй частичную специализацию. Переделай через перегрузку.
3. NTTP: `template<int N> class FixedArray` — массив фиксированного размера N.

### D. decltype(auto) (около 20 мин)

1. Напиши `perfect_invoke(f, args...)` возвращающую `decltype(auto)` — вызывает f с perfect forwarding и возвращает результат с правильной категорией.
2. Покажи разницу: если f возвращает `int&`, то `auto` обрезает ссылку, `decltype(auto)` — нет.

### Самопроверка (около 15 мин, письменно в `selfcheck.md`)

1. Почему `T&&` в шаблоне — forwarding reference, а не rvalue-ссылка?
2. Правила свёртки: `int& &&` → ?
3. Почему функции нельзя частично специализировать?
4. Fold expression `(args + ...)` vs `(... + args)` — в чём разница?
5. Зачем `decltype(auto)` вместо `auto`?

### Критерий "сделано"
Все типы в части A угаданы правильно (static_assert проходит), tuple_like работает для 3 элементов, perfect_invoke сохраняет lvalue-ссылки.
