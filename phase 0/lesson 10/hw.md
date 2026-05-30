### A. Rule of 5 реализация (около 60 мин)

Реализуй класс `UniqueArray<T>` — умный массив с эксклюзивным владением:
1. Конструктор `UniqueArray(size_t n)` — выделяет массив.
2. Деструктор — освобождает.
3. Copy constructor — глубокое копирование.
4. Copy assignment — глубокое копирование (с самоприсваиванием).
5. Move constructor — передача владения (noexcept).
6. Move assignment — передача владения (noexcept).
7. `operator[](size_t i)` — доступ к элементам.
8. `size() const`.

Добавь статический счётчик: `static int copy_count; static int move_count;` — увеличивай в каждом copy/move-конструкторе.
Тест: создай вектор `std::vector<UniqueArray<int>>`, добавь 3 элемента, затем зарезервируй (`reserve`) больше места. Выведи copy_count и move_count до и после reserve. Сравни с вариантом без `noexcept` на move.

### B. std::move и moved-from state (около 30 мин)

1. Создай `std::string s = "hello"`. Выполни `std::string t = std::move(s)`. Что теперь в `s`? Можно ли её использовать?
2. Напиши функцию `process(std::string data)` принимающую строку по значению. Вызови как `process(str)` и как `process(std::move(str))` — выведи адрес строки до и внутри функции чтобы видеть copy/move.
3. Что произойдёт если вызвать `std::move` на `const` объект? Попробуй: `const std::string cs = "x"; auto t = std::move(cs);` — скопировался или переместился? Почему?

### C. RVO/NRVO и copy elision (около 20 мин)

1. Напиши функцию `make_buffer()` возвращающую `Buffer` по значению. Добавь вывод в конструктор копирования и перемещающий конструктор. Убедись что ни один не вызывается (NRVO).
2. Добавь `return std::move(local_buf)` — что изменится? Зачем это антипаттерн?
3. Включи `-fno-elide-constructors` в g++ — какой конструктор теперь вызывается?

### D. Perfect forwarding (около 25 мин)

1. Напиши `template<typename T> void log_and_call(T&& arg)` которая выводит `"lvalue"` или `"rvalue"` в зависимости от категории, затем вызывает `process(std::forward<T>(arg))`.
2. Напиши `make<T>(Args&&... args)` — аналог `std::make_unique`: создаёт `T` с perfect forwarding аргументов.
3. Без `std::forward` — что изменится? Покажи что именованный параметр всегда lvalue.

### Самопроверка (около 20 мин, письменно в `selfcheck.md`)

1. Что делает `std::move`? Почему это не «перемещение»?
2. В каком состоянии объект после того, как из него переместили?
3. Что такое Rule of 5? Почему нельзя определить только деструктор?
4. Почему `noexcept` на move-конструкторе важен для `std::vector`?
5. Что такое NRVO? Почему `return std::move(local)` — антипаттерн?
6. Чем `T&&` forwarding reference отличается от rvalue-ссылки?
7. Что делает `std::forward`?

### Критерий "сделано"
UniqueArray работает без утечек, счётчик copy/move показывает что vector при reserve использует move (не copy) с noexcept, perfect forwarding работает корректно.
