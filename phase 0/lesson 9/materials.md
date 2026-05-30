### class vs struct

```cpp
class Foo {      // члены private по умолчанию
    int x;       // private
public:
    int y;       // public
};

struct Bar {     // члены public по умолчанию
    int x;       // public
private:
    int y;       // private
};
```

Единственное различие: `class` → `private` по умолчанию, `struct` → `public`. В остальном — полностью эквивалентны.

### Список инициализации членов

```cpp
class Point {
    const int x_;        // const — ОБЯЗАТЕЛЕН список инициализации
    int& ref_;           // ссылка — тоже обязателен
    std::string name_;
public:
    Point(int x, int& r, std::string name)
        : x_(x)               // порядок = порядок объявления, НЕ порядка в списке
        , ref_(r)
        , name_(std::move(name))  // move эффективнее copy для строк
    {}
};
```

**Ключевое**: члены инициализируются в порядке их **объявления** в классе, независимо от порядка в списке инициализации. GCC предупреждает при несоответствии (`-Wreorder`).

### explicit и неявные преобразования

```cpp
class MyInt {
public:
    MyInt(int x) {}           // неявный конструктор
};

class MyString {
public:
    explicit MyString(int size) {}  // явный — только прямая инициализация
};

MyInt a = 5;        // OK: неявная конверсия
MyString b = 5;     // ОШИБКА: explicit запрещает это
MyString c(5);      // OK: прямая инициализация
MyString d{5};      // OK: direct-list-initialization
```

**Правило**: одноаргументные конструкторы делай `explicit`, если неявная конверсия не имеет смысла.

### {} инициализация и narrowing

```cpp
int a{5};           // OK
int b{3.14};        // ОШИБКА: narrowing conversion (double→int) в {}
int c(3.14);        // OK: приведение без ошибки (!)
int d = 3.14;       // OK: тоже без ошибки

std::vector<int> v1(3);   // 3 элемента: {0, 0, 0}
std::vector<int> v2{3};   // 1 элемент: {3}
std::vector<int> v3{1,2,3}; // 3 элемента: {1, 2, 3}

auto x = {1, 2, 3};       // std::initializer_list<int>
```

### Делегирующие конструкторы (C++11)

```cpp
class Config {
    std::string path_;
    bool debug_;
public:
    Config() : Config("default.cfg", false) {}      // делегирует
    Config(std::string path) : Config(path, false) {} // делегирует
    Config(std::string path, bool debug)            // реальный конструктор
        : path_(std::move(path)), debug_(debug) {}
};
```

Наследуемые конструкторы:
```cpp
class Derived : public Base {
    using Base::Base;  // унаследовать все конструкторы Base
};
```

### Magic statics — потокобезопасный Singleton (C++11)

```cpp
class Logger {
public:
    static Logger& instance() {
        static Logger log;  // инициализируется один раз, потокобезопасно с C++11
        return log;
    }
private:
    Logger() { /* ... */ }
};
```

### SIOF — Static Initialization Order Fiasco

```cpp
// a.cpp
int count = 0;          // глобальная переменная

// b.cpp
extern int count;
int doubled = count * 2; // UB: count может быть ещё не инициализирован!
```

Порядок инициализации глобальных переменных в разных `.cpp` — **не определён**. Решение: заворачивать в функцию (magic static).

### Most Vexing Parse

```cpp
Widget w();         // объявление функции w(), а не объект!
Widget w{};         // OK: конструктор по умолчанию
Widget w = Widget(); // тоже OK

// Ещё хуже:
Widget w(int());    // объявление функции с параметром "указатель на функцию int()"
Widget w{int{}};    // OK: создаёт Widget с параметром 0
```
