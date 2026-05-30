### Виртуальные функции и vtable

```cpp
struct Animal {
    virtual void speak() const { std::cout << "...\n"; }
    virtual ~Animal() = default;  // ВСЕГДА виртуальный деструктор!
};
struct Dog : Animal {
    void speak() const override { std::cout << "Woof!\n"; }
};
struct Cat : Animal {
    void speak() const override { std::cout << "Meow!\n"; }
};

Animal* a = new Dog();
a->speak();  // "Woof!" — виртуальный вызов по vtable
delete a;    // ~Dog() затем ~Animal() — правильно, т.к. деструктор виртуальный
```

**vtable**: для каждого класса с виртуальными функциями компилятор создаёт таблицу указателей на функции. Каждый объект хранит скрытый `vptr`, указывающий на vtable своего класса.

Стоимость: один лишний указатель в объекте (обычно 8 байт), один уровень косвенности при вызове. Не бесплатно, но дёшево.

### override и final

```cpp
struct Base {
    virtual void process(int x);
    virtual void compute(double d) const;
};

struct Derived : Base {
    void process(int x) override;           // OK
    // void process(int x);                 // работает, но опасно — без проверки
    // void process(float x) override;      // ОШИБКА: нет такой функции в Base
    void compute(double d) const override;  // OK
    // void compute(double d) override;     // ОШИБКА: потеряли const

    void local_method() final;  // нельзя переопределить в наследниках
};

struct Final final : Base { /* ... */ };  // нельзя наследоваться от Final
```

**Правило**: всегда писать `override` при переопределении виртуальных функций.

### Slicing — срезка объекта

```cpp
struct Base { int x = 1; };
struct Derived : Base { int y = 2; };

Derived d;
Base b = d;      // СРЕЗКА: b — копия только Base-части, y потеряно
Base& rb = d;    // НЕТ срезки: ссылка на Derived
Base* pb = &d;   // НЕТ срезки: указатель на Derived

// Через виртуальные функции — полиморфизм работает только через указатели/ссылки
```

### Виртуальный деструктор

Без виртуального деструктора `delete` через указатель на базовый класс вызывает только деструктор базового класса → утечка ресурсов производного.

**Правило**: если в классе есть хотя бы одна виртуальная функция — деструктор тоже должен быть виртуальным.

```cpp
struct Base {
    virtual ~Base() = default;   // виртуальный, сгенерированный компилятором
};
```

### Вызов виртуальной из конструктора/деструктора

```cpp
struct Base {
    Base() {
        foo();  // ВЫЗЫВАЕТ Base::foo, не переопределённую версию!
    }
    virtual void foo() { std::cout << "Base::foo\n"; }
};
```

В конструкторе `Base`: объект Derived ещё не сконструирован, `vptr` указывает на `vtable_Base`. Виртуальный вызов резолвируется на текущий класс.

### Аргументы по умолчанию — статическая привязка

```cpp
struct Base {
    virtual void show(int x = 10) { std::cout << x; }
};
struct Derived : Base {
    void show(int x = 20) override { std::cout << x; }
};

Base* p = new Derived();
p->show();   // выведет 10 — аргумент из статического типа (Base), не динамического (Derived)
```

Аргументы по умолчанию определяются по **статическому** типу указателя/ссылки.

### Ромбовидное наследование

```cpp
// Без virtual — два экземпляра A:
struct A { int x; };
struct B : A {};
struct C : A {};
struct D : B, C { };
// D::B::x и D::C::x — разные поля

// С virtual — один экземпляр A:
struct B : virtual A {};
struct C : virtual A {};
struct D : B, C {};
// D::x — единственное
```
