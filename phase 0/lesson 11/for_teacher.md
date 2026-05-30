0–5 мин. Зачем это всё.
Обсуди вопрос. Покажи код:
```cpp
struct Base { int x; ~Base() { std::cout << "~Base\n"; } };
struct Derived : Base { int y; ~Derived() { std::cout << "~Derived\n"; } };
Base* p = new Derived();
delete p;  // вызывается только ~Base — утечка ресурсов Derived!
```
С `virtual ~Base()` — оба деструктора вызываются в правильном порядке.

5–30 мин. vtable и vptr.
Когда класс имеет виртуальные функции, компилятор добавляет скрытый указатель `vptr` в каждый объект. `vptr` указывает на таблицу виртуальных функций (`vtable`) класса — массив указателей на функции.
```
объект Base:    [vptr] → vtable_Base:   [&Base::foo, &Base::bar]
объект Derived: [vptr] → vtable_Derived:[&Derived::foo, &Base::bar]
```
Виртуальный вызов: загрузить vptr → загрузить адрес функции из vtable → вызвать. Небольшие накладные расходы.
Покажи через `sizeof`: `sizeof(int)` = 4, `sizeof(struct{int x; virtual void f(){};})` = 16 (или 8+4 с паддингом).

30–55 мин. override, final, slicing.
```cpp
struct Base {
    virtual void foo(int x) const;
    virtual ~Base() = default;
};
struct Derived : Base {
    void foo(int x) const override;  // override проверяет что функция есть в базе
    // void foo(int x) override;     // ОШИБКА: нет const — не переопределяет, а прячет!
};
```
`override` спасает от опечаток и изменений сигнатуры в базе.
`final`: запретить наследование (`struct Derived final`) или переопределение.

**Slicing**:
```cpp
Derived d; d.y = 42;
Base b = d;      // срезка: b — Base, поле y потеряно
Base* p = &d;    // НЕТ срезки: p указывает на Derived
```
Полиморфизм работает только через указатели и ссылки.

55–70 мин. Вызов виртуальной в конструкторе/деструкторе.
```cpp
struct Base {
    Base() { foo(); }      // вызывает Base::foo(), НЕ Derived::foo()!
    virtual void foo() { std::cout << "Base::foo\n"; }
};
struct Derived : Base {
    void foo() override { std::cout << "Derived::foo\n"; }
};
Derived d;  // конструктор Base вызовет Base::foo, не Derived::foo
```
В момент вызова конструктора Base, объект Derived ещё не полностью сконструирован. vptr указывает на vtable_Base.

Аргументы по умолчанию у виртуальных — статическая привязка:
```cpp
struct Base { virtual void f(int x = 1) { std::cout << x; } };
struct Derived : Base { void f(int x = 2) override { std::cout << x; } };
Base* p = new Derived();
p->f();  // выведет 1 — аргумент по умолчанию из Base (статически)!
```

70–82 мин. Ромб и виртуальное наследование.
```cpp
struct A { int x; };
struct B : A {};
struct C : A {};
struct D : B, C {};  // D имеет два A: D::B::A и D::C::A
D d;
d.x = 5;  // ОШИБКА: неоднозначно
d.B::x = 5;  // OK

struct A { int x; };
struct B : virtual A {};  // виртуальное наследование
struct C : virtual A {};
struct D : B, C {};  // один A
D d; d.x = 5;  // OK
```

82–90 мин. Выдача ДЗ.
