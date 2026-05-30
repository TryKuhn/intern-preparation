### A. const и указатели (около 30 мин)

Для каждой строки определи: компилируется или нет? Если нет — почему?
```cpp
int x = 5;
const int* p1 = &x;
int* const p2 = &x;
const int* const p3 = &x;

*p1 = 10;    // ?
p1 = nullptr; // ?
*p2 = 10;    // ?
p2 = nullptr; // ?
*p3 = 10;    // ?
p3 = nullptr; // ?
```
Проверь каждое через компилятор. Напиши объяснения в `selfcheck.md`.

### B. const-методы и mutable (около 40 мин)

1. Реализуй класс `LazyString`:
   - Хранит `std::string text_`.
   - `size_t length() const` — возвращает длину строки, **кэширует** результат в `mutable size_t cached_length_` и `mutable bool valid_`.
   - `void set(std::string s)` — меняет строку, инвалидирует кэш.
   - Убедись что первый вызов `length()` вычисляет, последующие — берут из кэша.

2. Напиши функцию `void print(const std::string& s)`. Попробуй вызвать её с: `std::string s; print(s);` и `print("hello");`. Почему второй вариант работает? (const& к temporary)

3. Напиши класс `ThreadSafeCounter` с `mutable std::mutex` — `increment()` и `get() const` (const-метод который всё равно блокирует мьютекс для чтения).

### C. constexpr (около 30 мин)

1. Напиши `constexpr int fibonacci(int n)`. Вычисли `fibonacci(30)` на этапе компиляции через `constexpr int f30 = fibonacci(30);`. Убедись через `static_assert(f30 == ...)`.
2. Напиши `constexpr std::array<int, 10> make_squares()` которая возвращает массив квадратов чисел 0..9. Используй в compile-time контексте.
3. Попробуй вызвать обычную (не constexpr) функцию в constexpr-контексте — покажи ошибку.

### D. volatile (около 15 мин)

1. Напиши программу с `volatile int x = 0;` и циклом `while(x == 0) {}`. Сравни asm-вывод (`g++ -O2 -S`) с и без `volatile`. Найди разницу.
2. Объясни письменно почему `volatile` не заменяет `std::atomic` для потоков.

### Самопроверка (около 20 мин, письменно в `selfcheck.md`)

1. Что означает `const int* p` и `int* const p`? В чём разница?
2. Может ли const-метод изменять состояние объекта? Когда это допустимо?
3. Чем `constexpr` отличается от `const`?
4. Что делает `consteval` (C++20)?
5. Почему `volatile` не используется для синхронизации потоков?
6. Что такое «логическая константность»?
7. Верхний const при передаче параметра по значению — имеет ли значение?

### Критерий "сделано"
LazyString работает корректно с кэшем, constexpr fibonacci вычислен на этапе компиляции (проверено через static_assert), volatile vs atomic объяснено письменно.
