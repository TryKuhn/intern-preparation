### Rule of 0/3/5

| Правило | Условие | Что определить |
|---|---|---|
| Rule of 0 | Не управляешь ресурсами | Ничего |
| Rule of 3 | Определён деструктор | + copy constructor + copy assignment |
| Rule of 5 | Rule of 3 | + move constructor + move assignment |

```cpp
// Rule of 0: используй RAII-типы, ничего не определяй
class Good {
    std::unique_ptr<Data> data_;  // unique_ptr сам управляет памятью
    std::string name_;
    // Деструктор, копирование, перемещение — не нужны
};

// Rule of 5: управляешь ресурсом вручную
class Buffer {
    char* data_;
    size_t size_;
public:
    Buffer(size_t n) : data_(new char[n]), size_(n) {}
    ~Buffer() { delete[] data_; }

    // Copy
    Buffer(const Buffer& o) : data_(new char[o.size_]), size_(o.size_) {
        std::copy(o.data_, o.data_ + o.size_, data_);
    }
    Buffer& operator=(const Buffer& o) {
        if (this != &o) {
            delete[] data_;
            data_ = new char[o.size_]; size_ = o.size_;
            std::copy(o.data_, o.data_ + o.size_, data_);
        }
        return *this;
    }

    // Move (noexcept!)
    Buffer(Buffer&& o) noexcept : data_(o.data_), size_(o.size_) {
        o.data_ = nullptr; o.size_ = 0;
    }
    Buffer& operator=(Buffer&& o) noexcept {
        if (this != &o) {
            delete[] data_;
            data_ = o.data_; size_ = o.size_;
            o.data_ = nullptr; o.size_ = 0;
        }
        return *this;
    }
};
```

### std::move — просто каст

```cpp
// std::move реализован примерно так:
template<typename T>
typename std::remove_reference<T>::type&&
move(T&& t) noexcept {
    return static_cast<typename std::remove_reference<T>::type&&>(t);
}
```

`std::move(x)` не перемещает ничего — это `static_cast<T&&>(x)`. Перемещение происходит в перемещающем конструкторе или операторе=, которые вызываются на rvalue.

**Moved-from state**: объект в «валидном, но неопределённом» состоянии. Его можно уничтожить или присвоить ему новое значение. Читать нельзя.

### Copy Elision и RVO/NRVO

```cpp
std::string make_string() {
    std::string s = "hello";
    return s;        // NRVO: компилятор создаёт s прямо в месте назначения
}

auto s = make_string();  // Без копирования и перемещения!
```

С C++17 mandatory copy elision для prvalue: `return std::string("hello")` — гарантированно никаких копий.

**Антипаттерн**: `return std::move(local);` — это блокирует NRVO, принудительно вызывает перемещение вместо элизии!

### Forwarding references и perfect forwarding

```cpp
// T&& — forwarding reference (не rvalue-ссылка!) когда T выводится
template<typename T>
void forward_to(T&& arg) {
    target(std::forward<T>(arg));  // forward сохраняет категорию значения
}
```

Правила свёртки ссылок:
- `T& &` → `T&`
- `T& &&` → `T&`
- `T&& &` → `T&`
- `T&& &&` → `T&&`

Если `forward_to` вызывается с lvalue (`int x; forward_to(x)`): T = `int&`, arg тип `int&`.
Если с rvalue (`forward_to(5)`): T = `int`, arg тип `int&&`.
`std::forward<T>(arg)` возвращает `T&&` → после свёртки: lvalue остаётся lvalue, rvalue остаётся rvalue.

### noexcept и std::vector

```cpp
class MyType {
public:
    MyType(MyType&& other) noexcept { /* ... */ }  // noexcept — ВАЖНО!
};

std::vector<MyType> v;
v.push_back(MyType{});  // при реаллокации:
                         // с noexcept — перемещает (O(n) без копий)
                         // без noexcept — копирует (дороже)
```

`std::vector` проверяет `noexcept` через `std::is_nothrow_move_constructible`. Всегда помечай move-конструктор и move-оператор= как `noexcept` если они действительно не бросают.
