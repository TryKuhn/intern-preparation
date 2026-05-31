0–10 мин. Вывод T в forwarding reference.
При передаче lvalue `int x`: T выводится как `int&`. Тогда `T&&` = `int& &&` → по свёртке = `int&`. При передаче rvalue `42`: T = `int`, `T&&` = `int&&`. Это правило свёртки ссылок — фундамент perfect forwarding.

10–40 мин. Три режима вывода аргументов.
```cpp
template<typename T>
void f1(T x);    // T: отбрасывает ссылки и const. f1(x) → T=int; f1(cx) → T=int

template<typename T>
void f2(T& x);   // T: сохраняет const. f2(x) → T=int; f2(cx) → T=const int

template<typename T>
void f3(T&& x);  // forwarding ref: T=int& (lvalue), T=int (rvalue)
```
Демонстрация через `static_assert(std::is_same_v<T, expected_type>)` и `typeid`.

`decltype(auto)`:
```cpp
int x = 5; int& rx = x;
decltype(auto) a = x;   // int (не ссылка)
decltype(auto) b = rx;  // int& (ссылка сохранена)
decltype(auto) c = std::move(x); // int&&
```

40–65 мин. Специализация и variadic.
```cpp
// Частичная специализация — только для классов:
template<typename T> struct IsPointer { static constexpr bool value = false; };
template<typename T> struct IsPointer<T*> { static constexpr bool value = true; };

// Функции нельзя частично специализировать — перегружать:
template<typename T> void process(T val) { /* общий */ }
template<typename T> void process(std::vector<T>& v) { /* для vector */ }

// Variadic шаблоны + fold expressions:
template<typename... Args>
auto sum(Args... args) { return (... + args); }  // fold expression

template<typename... Args>
void print(Args&&... args) {
    ((std::cout << args << ' '), ...);  // comma fold
}
```

65–82 мин. NTTP. Выдача ДЗ.
