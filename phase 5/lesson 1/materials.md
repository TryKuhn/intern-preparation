### Три режима вывода аргументов

```cpp
template<typename T> void by_value(T x);   // отбрасывает ref и top-level const
template<typename T> void by_lref(T& x);   // сохраняет const; rvalue не принимает
template<typename T> void by_fwd(T&& x);   // forwarding reference; всё принимает

int i = 5;
const int ci = 5;
int& ri = i;

by_value(i);    // T = int
by_value(ci);   // T = int (const отброшен)
by_lref(i);     // T = int
by_lref(ci);    // T = const int (const сохранён)
by_fwd(i);      // T = int& (lvalue → T = lvalue ref)
by_fwd(42);     // T = int (rvalue → T = value type)
by_fwd(ci);     // T = const int& (const lvalue)
```

### Правила свёртки ссылок

Применяются при T = ссылочный тип в `T&&`:

| T | T&& |
|---|---|
| `int` | `int&&` |
| `int&` | `int&` (& && → &) |
| `int&&` | `int&&` (&& && → &&) |

### decltype(auto) — точное сохранение типа

```cpp
int x = 5; int& rx = x; const int cx = 5;

auto a = rx;           // int — ссылка отброшена
decltype(auto) b = rx; // int& — ссылка сохранена

// Полезно для возврата из обёрток:
template<typename Func, typename... Args>
decltype(auto) invoke(Func f, Args&&... args) {
    return f(std::forward<Args>(args)...);
}
```

### Частичная специализация классов

```cpp
// Общий случай:
template<typename T>
struct TypeInfo { static const char* name() { return "unknown"; } };

// Специализация для указателей:
template<typename T>
struct TypeInfo<T*> { static const char* name() { return "pointer"; } };

// Специализация для vector:
template<typename T>
struct TypeInfo<std::vector<T>> { static const char* name() { return "vector"; } };

// Функции нельзя частично специализировать — используем перегрузку:
template<typename T> void process(T val);
template<typename T> void process(T* ptr);        // перегрузка для указателей
template<typename T> void process(std::vector<T>); // перегрузка для vector
```

### Variadic шаблоны и fold expressions (C++17)

```cpp
// Рекурсия (до C++17):
template<typename T>
void print(T last) { std::cout << last << '\n'; }

template<typename T, typename... Rest>
void print(T first, Rest... rest) {
    std::cout << first << ' ';
    print(rest...);
}

// Fold expressions (C++17) — намного чище:
template<typename... Args>
void print(Args&&... args) {
    ((std::cout << args << ' '), ...);  // unary right fold: op1, op2, op3, ...
    std::cout << '\n';
}

template<typename... Args>
auto sum(Args... args) { return (args + ...); }  // unary right fold: a + (b + c)

template<typename... Args>
bool all_positive(Args... args) { return ((args > 0) && ...); }
```

### Non-Type Template Parameters (NTTP)

```cpp
template<int N>
std::array<int, N> make_zeros() { return {}; }

template<typename T, size_t N>
constexpr size_t size_of_array(T (&)[N]) { return N; }

// C++20: float, string, user-defined literal как NTTP
template<auto Value>  // C++17: любой integral или pointer
void show() { std::cout << Value; }
show<42>(); show<'A'>(); show<nullptr>();
```
