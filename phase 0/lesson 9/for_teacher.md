0–5 мин. Зачем это всё.
Обсуди вопрос про порядок инициализации. Покажи конкретный баг:
```cpp
struct Bad {
    int b;
    int a;
    Bad(int x) : a(x), b(a * 2) {}  // БАГ: b инициализируется ПЕРВЫМ (порядок объявления),
};                                    // но a ещё не инициализирован в этот момент!
```
GCC/Clang предупреждают `-Wreorder`. Включить и показать.

5–30 мин. class/struct, доступ, инкапсуляция.
- `class` — по умолчанию `private`. `struct` — по умолчанию `public`. Это единственное различие.
- Инкапсуляция: `private` — детали реализации. `public` — интерфейс. `protected` — для наследников.
- Список инициализации обязателен для: `const` членов, ссылок (`int&`), членов без конструктора по умолчанию, баз классов. Эффективнее присваивания в теле.
```cpp
class Person {
    std::string name_;     // private по умолчанию в class
    int age_;
public:
    Person(std::string name, int age)
        : name_(std::move(name))  // move, не копия
        , age_(age)
    {}
};
```

30–50 мин. explicit, {} инициализация, ловушки.
```cpp
class Num {
public:
    Num(int x) {}             // неявное преобразование: Num n = 5; OK
    explicit Num2(int x) {}   // explicit: только Num2 n(5); OK
};
Num n = 5;    // неявная конверсия — может быть неожиданной
Num2 n = 5;   // ОШИБКА: нет неявного конструктора
```
`{}` инициализация: запрещает сужающие преобразования.
```cpp
int i{3.14};  // ОШИБКА: narrowing
```
Ловушка:
```cpp
std::vector<int> v1(3);    // 3 нулевых элемента
std::vector<int> v2{3};    // один элемент со значением 3
auto x = {1, 2, 3};        // std::initializer_list<int>, не vector!
```

50–65 мин. Делегирующие конструкторы, magic statics, SIOF.
```cpp
class Config {
public:
    Config() : Config("default.cfg") {}          // делегирующий конструктор
    Config(const std::string& file) { load(file); }
};
```
Наследуемые конструкторы: `using Base::Base;` в наследнике.
Magic statics (C++11):
```cpp
Singleton& get_instance() {
    static Singleton instance;  // потокобезопасная инициализация с C++11
    return instance;
}
```
SIOF (Static Initialization Order Fiasco): глобальные объекты инициализируются в неопределённом порядке между единицами трансляции. Решение: lazy-инициализация через функции (magic static).

65–80 мин. Most Vexing Parse.
```cpp
Widget w();          // ОШИБКА: объявление функции, а не конструктор!
Widget w{};          // OK: вызов конструктора по умолчанию
Widget w = Widget(); // тоже OK
```
Most vexing parse: `Widget w(Widget())` — объявление функции с параметром. Решение: `{}` или `auto`.

80–90 мин. Выдача ДЗ.
