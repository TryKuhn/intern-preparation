0–5 мин. Зачем это всё.
Обсуди: всегда использовать `shared_ptr` — плохо. У него атомарный счётчик ссылок (стоит денег), он провоцирует циклы (утечки), скрывает семантику владения. Идиома современного C++: `unique_ptr` для владения, сырой указатель для наблюдения. `shared_ptr` — только когда нужно разделённое владение.

5–25 мин. RAII — идиома.
Покажи проблему без RAII:
```cpp
void process() {
    FILE* f = fopen("data.txt", "r");
    do_work(f);           // а если здесь исключение?
    fclose(f);            // может не выполниться!
}
```
RAII: ресурс захватывается в конструкторе, освобождается в деструкторе. Деструктор вызывается всегда при выходе из области видимости — даже при исключении.
```cpp
struct FileGuard {
    FILE* f;
    FileGuard(const char* name) : f(fopen(name, "r")) {}
    ~FileGuard() { if (f) fclose(f); }
};
```
Покажи scope guard: `std::unique_ptr` с кастомным делитером как RAII-обёртка.

25–50 мин. unique_ptr.
```cpp
auto p = std::make_unique<int>(42);
// auto q = p;  // ОШИБКА: нельзя копировать
auto q = std::move(p);  // передача владения
// p теперь nullptr
*q == 42;
```
- `unique_ptr` — единственный владелец. Деструктор автоматически вызывает `delete`.
- `make_unique` — предпочтительнее `new` (exception-safe).
- Кастомный делитер: `unique_ptr<FILE, decltype(&fclose)> f(fopen("f","r"), fclose);`
- Сырой указатель из `unique_ptr`: `p.get()` — невладеющий, не освобождать!

50–70 мин. shared_ptr и weak_ptr.
```cpp
auto sp1 = std::make_shared<int>(42);  // один аллок для объекта + control block
auto sp2 = sp1;  // счётчик = 2
sp1.reset();     // счётчик = 1
// sp2 уничтожается — счётчик = 0 — объект освобождается
```
Control block: хранит счётчик strong refs и счётчик weak refs. Атомарный — потокобезопасен, но не бесплатен.

Цикл `shared_ptr`:
```cpp
struct Node { std::shared_ptr<Node> next; };  // цикл — утечка!
struct Node { std::weak_ptr<Node> next; };    // weak_ptr рвёт цикл
```
`weak_ptr`: не удерживает объект живым. Чтобы использовать — `lock()`, возвращает `shared_ptr` или пустой.

`make_shared` vs `new`: `make_shared` делает одну аллокацию (объект + control block вместе). Экономично. Но: если есть `weak_ptr`, control block живёт до последнего weak_ptr.

70–80 мин. enable_shared_from_this.
```cpp
class Foo : public std::enable_shared_from_this<Foo> {
    std::shared_ptr<Foo> get_self() { return shared_from_this(); }
};
// Использовать только если объект уже управляется shared_ptr!
```

80–90 мин. Выдача ДЗ. Правило большого пальца: сырой указатель = невладеющий наблюдатель. Всегда.
