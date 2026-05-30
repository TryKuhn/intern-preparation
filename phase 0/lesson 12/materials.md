### Шаблоны функций

```cpp
template<typename T>
T max_val(T a, T b) { return a > b ? a : b; }

// Вывод аргументов:
max_val(3, 5);         // T = int
max_val(3.0, 5.0);     // T = double
max_val(3, 5.0);       // ОШИБКА: T неоднозначен (int или double?)
max_val<double>(3, 5.0); // OK: явно указан тип

// Полная специализация:
template<>
const char* max_val<const char*>(const char* a, const char* b) {
    return std::strcmp(a, b) > 0 ? a : b;
}
```

### Шаблоны классов

```cpp
template<typename T, size_t N = 10>  // с параметром по умолчанию
class FixedStack {
    T data_[N];
    size_t top_ = 0;
public:
    void push(const T& val) { data_[top_++] = val; }
    T& top() { return data_[top_ - 1]; }
    bool empty() const { return top_ == 0; }
    size_t size() const { return top_; }
};

FixedStack<int> s;       // N = 10 по умолчанию
FixedStack<double, 5> s2; // явно N = 5
```

### Специализация

```cpp
// Частичная специализация класса (для указателей):
template<typename T>
class FixedStack<T*> {
    // другая реализация для типов-указателей
};

// Функции нельзя частично специализировать — только перегружать:
template<typename T>
void process(std::vector<T>& v) { /* специальная версия для vector */ }
```

### if constexpr (C++17)

```cpp
template<typename T>
std::string to_string_safe(T val) {
    if constexpr (std::is_same_v<T, std::string>) {
        return val;
    } else if constexpr (std::is_arithmetic_v<T>) {
        return std::to_string(val);
    } else {
        return "[unknown]";
    }
}
```

`if constexpr` — ветки, которые не проходят проверку, не компилируются (в отличие от обычного `if`).

### UB vs Unspecified vs Implementation-defined

| Категория | Что обязан делать компилятор | Пример |
|---|---|---|
| **Undefined Behavior (UB)** | Ничего — любое поведение допустимо | Знаковое переполнение |
| **Unspecified behavior** | Выбрать одно из допустимых | Порядок вычисления аргументов |
| **Implementation-defined** | Выбрать одно из допустимых И документировать | Размер `int` |

```cpp
// UB: знаковое переполнение
int x = INT_MAX;
x++;  // UB — компилятор может оптимизировать с предположением что этого нет

// UB: null pointer dereference
int* p = nullptr;
*p = 5;  // UB — обычно segfault, но не гарантирован

// Unspecified: порядок вычисления аргументов
int a = 0;
foo(a++, a++);  // порядок вычисления a++ не определён (в C++17 — для перегрузок ок)

// UB: i = i++ — модификация дважды между sequence points (до C++17 — UB)
int i = 0;
i = i++;  // результат неопределён
```

### Strict aliasing

```cpp
float f = 3.14f;
int* p = reinterpret_cast<int*>(&f);
*p = 42;  // UB: нарушение правила strict aliasing

// Компилятор предполагает что int* и float* никогда не указывают на одну память
// (кроме char* и unsigned char* — те могут)

// Правильный способ переинтерпретировать биты:
int bits;
std::memcpy(&bits, &f, sizeof(int));  // OK: memcpy — исключение
```

### Четыре каста

```cpp
// static_cast: явные, проверяемые преобразования
int i = static_cast<int>(3.7);          // 3
Base* p = static_cast<Base*>(derived);  // upcast

// dynamic_cast: downcast с RTTI (требует виртуальный метод)
Derived* d = dynamic_cast<Derived*>(base_ptr);  // nullptr если не Derived
Derived& d = dynamic_cast<Derived&>(base_ref);  // std::bad_cast если не Derived

// const_cast: убрать/добавить const
const int* cp = &x;
int* p = const_cast<int*>(cp);  // UB если оригинал const!

// reinterpret_cast: переинтерпретация битов — почти всегда опасно
uintptr_t addr = reinterpret_cast<uintptr_t>(ptr);
```

**Правило**: предпочитай `static_cast`. `dynamic_cast` — только для полиморфных типов. `const_cast` — только для снятия const с не-const оригинала. `reinterpret_cast` — максимально редко.
