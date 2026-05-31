0–10 мин. Зачем знать SFINAE.
Ответ: legacy code. 99% существующего C++ кода написан до C++20. На стажировке скорее всего встретишь enable_if, void_t, tag dispatch. Concepts — для новых проектов. Важно читать оба.

10–40 мин. Concepts C++20.
```cpp
// Определение концепта:
template<typename T>
concept Integral = std::is_integral_v<T>;

template<typename T>
concept Printable = requires(T x) {
    { std::cout << x } -> std::same_as<std::ostream&>;
};

template<typename T>
concept Sortable = requires(T& c) {
    { c.begin() } -> std::input_iterator;
    { c.end() } -> std::input_iterator;
    requires std::totally_ordered<typename T::value_type>;
};

// Использование:
template<Integral T>
void process(T x) { std::cout << "integral: " << x; }

// Или через requires-clause:
template<typename T>
requires std::is_integral_v<T>
void process(T x) { ... }

// Стандартные концепты из <concepts>:
// std::same_as, std::convertible_to, std::integral, std::floating_point
// std::equality_comparable, std::totally_ordered
// std::invocable, std::regular
```
Ошибки с concepts: компилятор показывает какой концепт не выполнен — читаемо! SFINAE — эзотерические сообщения.

40–60 мин. CRTP — статический полиморфизм.
```cpp
template<typename Derived>
class Comparable {
    bool operator<(const Derived& other) const {
        return static_cast<const Derived*>(this)->compare(other) < 0;
    }
    bool operator>(const Derived& other) const { return other < *this; }
    bool operator==(const Derived& other) const {
        return !(*this < other) && !(other < *this);
    }
};

class MyClass : public Comparable<MyClass> {
public:
    int compare(const MyClass& other) const { return value_ - other.value_; }
private:
    int value_;
};
```
CRTP: база знает тип наследника на этапе компиляции → статический полиморфизм без vtable. Применение: mixins, CRTP-Policy classes, enable_shared_from_this.

60–75 мин. Dependent names, two-phase lookup, модель инстанцирования.
```cpp
template<typename T>
struct Base { void foo() {} };

template<typename T>
struct Derived : Base<T> {
    void bar() {
        foo();         // ОШИБКА: foo — dependent name, не найдена в фазе 1
        this->foo();   // OK: явно зависимое выражение
        Base<T>::foo();// OK: квалифицированный поиск
    }
};
```

Почему шаблоны в заголовках: код генерируется при инстанцировании. Инстанцирование требует полного определения — только .h/.hpp файлы видны в нужном месте.

75–90 мин. Code bloat и explicit instantiation. Итог фазы 5. Выдача ДЗ.
