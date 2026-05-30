0–5 мин. Зачем это всё.
Обсуди: `std::move` — это просто каст к `T&&`. Никакого перемещения в момент вызова не происходит. Перемещение — это контракт: перемещающий конструктор берёт ресурсы объекта-источника и оставляет тот в «валидном, но неопределённом» состоянии. Это избегает копирования, например строк.

5–30 мин. Rule of 0/3/5 и специальные методы.
```cpp
class MyClass {
public:
    MyClass();                                // конструктор по умолчанию
    ~MyClass();                               // деструктор
    MyClass(const MyClass& other);            // копирующий конструктор
    MyClass& operator=(const MyClass& other); // копирующий оператор =
    MyClass(MyClass&& other) noexcept;        // перемещающий конструктор
    MyClass& operator=(MyClass&& other) noexcept; // перемещающий оператор =
};
```
Rule of 0: если не управляешь ресурсами — не определяй ни одного специального метода.
Rule of 3: если определил деструктор — определи копирующий конструктор и оператор=.
Rule of 5: если определил деструктор — определи все 5 (добавь перемещающие).
`= default`: попросить компилятор сгенерировать стандартную версию.
`= delete`: запретить операцию.

30–55 мин. Move semantics.
```cpp
class Buffer {
    size_t size_;
    char* data_;
public:
    Buffer(size_t n) : size_(n), data_(new char[n]) {}
    ~Buffer() { delete[] data_; }
    // Перемещающий конструктор:
    Buffer(Buffer&& other) noexcept
        : size_(other.size_), data_(other.data_) {
        other.size_ = 0;
        other.data_ = nullptr;  // источник в валидном состоянии
    }
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data_;
            data_ = other.data_;  size_ = other.size_;
            other.data_ = nullptr; other.size_ = 0;
        }
        return *this;
    }
};
```
`std::move(x)` — это `static_cast<Buffer&&>(x)`. Не перемещает, разрешает перемещение.
Moved-from state: объект должен быть в «валидном, но неопределённом» состоянии. Можно уничтожить, нельзя читать.

55–70 мин. RVO/NRVO и copy elision.
```cpp
std::string make_string() {
    std::string s = "hello";
    return s;  // NRVO: компилятор создаёт строку сразу в месте назначения
}
```
RVO (Named Return Value Optimization): компилятор создаёт объект прямо в возвращаемой памяти. Обязателен с C++17 для prvalue return.
Правило: не писать `return std::move(s)` — это блокирует NRVO!

70–82 мин. Forwarding references и noexcept.
```cpp
template<typename T>
void wrapper(T&& arg) { use(std::forward<T>(arg)); }  // perfect forwarding
```
`T&&` — forwarding/universal reference (не rvalue-reference!) — выводится по правилам свёртки.
`noexcept` на move-конструкторе критически важен для `std::vector`:
```cpp
// vector при реаллокации:
// Если move-конструктор noexcept — перемещает элементы (O(n) без копий)
// Если нет — копирует (безопасно при исключении)
```

82–90 мин. Выдача ДЗ.
