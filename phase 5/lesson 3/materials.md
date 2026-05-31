### Concepts C++20

```cpp
#include <concepts>

// Определение концепта:
template<typename T>
concept Addable = requires(T a, T b) { a + b; };

template<typename T>
concept Container = requires(T c) {
    { c.begin() } -> std::input_or_output_iterator;
    { c.end() }   -> std::input_or_output_iterator;
    { c.size() }  -> std::convertible_to<size_t>;
};

// Использование — несколько синтаксических вариантов:
template<std::integral T>       // terse syntax
T abs_val(T x) { return x < 0 ? -x : x; }

template<typename T>
requires std::integral<T>       // requires-clause
T abs_val2(T x) { return x < 0 ? -x : x; }

template<typename T>
T abs_val3(T x) requires std::integral<T> { ... }  // trailing requires

auto abs_val4(std::integral auto x) { ... }  // abbreviated function template

// Составные концепты:
template<typename T>
concept SignedIntegral = std::integral<T> && std::is_signed_v<T>;

// Стандартные концепты из <concepts>:
// Arithmetic: std::integral, std::floating_point, std::arithmetic
// Object:     std::regular, std::semiregular
// Callable:   std::invocable, std::predicate
// Comparison: std::equality_comparable, std::totally_ordered
// Iterator:   std::input_iterator, std::random_access_iterator
```

### CRTP — статический полиморфизм

```cpp
// Mixin через CRTP:
template<typename Derived>
class Printable {
public:
    void print() const {
        static_cast<const Derived*>(this)->print_impl();
    }
};

template<typename Derived>
class Comparable {
public:
    bool operator<(const Derived& o) const {
        return static_cast<const Derived*>(this)->less(o);
    }
    bool operator>(const Derived& o) const { return o < static_cast<const Derived&>(*this); }
    bool operator==(const Derived& o) const { return !(*this < o) && !(o < *this); }
};

class Point : public Comparable<Point>, public Printable<Point> {
    int x_, y_;
public:
    bool less(const Point& o) const { return x_ != o.x_ ? x_ < o.x_ : y_ < o.y_; }
    void print_impl() const { std::cout << "(" << x_ << "," << y_ << ")"; }
};
```

**Преимущества CRTP над виртуальными функциями**: нет vtable overhead, inline-friendly, compile-time dispatch.

### Dependent names и two-phase lookup

```cpp
template<typename T>
class MyBase { public: void greet() {} };

template<typename T>
class Child : public MyBase<T> {
public:
    void run() {
        greet();          // ОШИБКА: фаза 1 — greet не найдена (зависит от T)
        this->greet();    // OK: зависимое имя — ищется в фазе 2
        MyBase<T>::greet(); // OK: квалифицированный dependent name
    }
};
```

`typename` и `template` дизамбигуаторы:
```cpp
template<typename T>
void f() {
    typename T::value_type x;   // typename: value_type — тип (не статическое поле)
    T::template helper<int>();  // template: helper — шаблон (не переменная)
}
```

### Модель инстанцирования

Шаблон инстанцируется (генерируется код) в точке использования. Компилятор должен видеть полное определение шаблона → шаблоны живут в заголовках.

```cpp
// myclass.h — ПРАВИЛЬНО: полное определение
template<typename T>
class MyClass {
    void method() { /* ... */ }  // определение в заголовке
};

// Explicit instantiation: сгенерировать код для конкретного типа в .cpp
// myclass.cpp:
template class MyClass<int>;    // explicit instantiation definition
// myclass.h:
extern template class MyClass<int>; // explicit instantiation declaration
// → уменьшает дублирование кода при сборке нескольких .cpp
```

**Code bloat**: каждое инстанцирование генерирует отдельный код. `vector<int>` и `vector<double>` — разные функции. Explicit instantiation помогает вынести в один .cpp.
