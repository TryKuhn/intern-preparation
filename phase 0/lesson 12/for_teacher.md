0–5 мин. Зачем это всё.
Обсуди `i = i++`. Запусти с разными компиляторами и оптимизациями — результаты могут отличаться. Это UB: компилятор вправе сделать что угодно. Именно поэтому понимание UB — необходимый навык для C++ разработчика.

5–30 мин. Шаблоны — рабочий минимум.
```cpp
template<typename T>
T max_val(T a, T b) { return a > b ? a : b; }

max_val(3, 5);          // T = int (вывод)
max_val(3.0, 5.0);      // T = double
max_val<std::string>("a", "b");  // явное указание

template<typename T>
class Stack {
    std::vector<T> data_;
public:
    void push(const T& val) { data_.push_back(val); }
    T pop() { T v = data_.back(); data_.pop_back(); return v; }
};

Stack<int> s; s.push(1); s.push(2);
```
Вывод аргументов: компилятор выводит T из аргументов. Не может выводить если T только в возвращаемом типе.

30–50 мин. Специализация и if constexpr.
```cpp
// Полная специализация функции:
template<> std::string max_val<std::string>(std::string a, std::string b) {
    return a.length() > b.length() ? a : b;
}

// Частичная специализация — только для классов:
template<typename T>
class Stack<T*> { /* версия для указателей */ };

// if constexpr (C++17) — ветвление на этапе компиляции:
template<typename T>
void print_type(T val) {
    if constexpr (std::is_integral_v<T>) {
        std::cout << "int: " << val;
    } else {
        std::cout << "other: " << val;
    }
}
```

50–70 мин. UB-тур.
Три категории неопределённости:
- **UB (Undefined Behavior)**: всё что угодно, включая «работает сегодня, ломается завтра».
- **Unspecified behavior**: реализация может делать что-то, но не обязана документировать.
- **Implementation-defined**: реализация должна документировать поведение.

```cpp
// UB: порядок вычисления аргументов — unspecified
foo(a++, a++);  // порядок вычисления a++ — unspecified, две модификации — UB

// UB: знаковое переполнение
int x = INT_MAX + 1;  // UB

// UB: null pointer dereference
int* p = nullptr; *p = 5;  // UB — segfault не гарантирован!

// UB: strict aliasing
float f = 3.14;
int* p = reinterpret_cast<int*>(&f);  // нарушение strict aliasing
*p = 42;  // UB — компилятор может кэшировать f в регистре
```

70–82 мин. Четыре каста.
```cpp
// static_cast: «разумные» преобразования, проверяются компилятором
double d = 3.7; int i = static_cast<int>(d);  // 3
Base* p = static_cast<Base*>(derived_ptr);  // upcast

// dynamic_cast: безопасный downcast с проверкой в runtime (RTTI)
Derived* dp = dynamic_cast<Derived*>(base_ptr);  // nullptr если не тот тип

// const_cast: убрать/добавить const
const int* cp = &x;
int* p = const_cast<int*>(cp);  // UB если x реально const!

// reinterpret_cast: переинтерпретация битов (опасно)
int* p = reinterpret_cast<int*>(0xDEADBEEF);  // очень опасно
```

82–90 мин. Выдача ДЗ. Итог фазы 0 — в следующий раз контрольная.
