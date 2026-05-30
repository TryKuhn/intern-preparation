### RAII — идиома управления ресурсами

**RAII** (Resource Acquisition Is Initialization): ресурс захватывается в конструкторе, освобождается в деструкторе. Деструктор вызывается детерминированно при выходе из scope — включая путь через исключение.

```cpp
class FileGuard {
    FILE* f_;
public:
    explicit FileGuard(const char* name) : f_(fopen(name, "r")) {
        if (!f_) throw std::runtime_error("cannot open file");
    }
    ~FileGuard() { fclose(f_); }
    FILE* get() { return f_; }
    // Запрещаем копирование:
    FileGuard(const FileGuard&) = delete;
    FileGuard& operator=(const FileGuard&) = delete;
};
```

### unique_ptr — эксклюзивное владение

```cpp
#include <memory>

auto p = std::make_unique<int>(42);     // создать
auto arr = std::make_unique<int[]>(10); // массив

*p = 100;                   // разыменовать
p.get();                    // сырой указатель (не освобождать!)
p.reset();                  // освободить и обнулить
p.release();                // забрать сырой указатель (p становится nullptr)

auto q = std::move(p);      // передать владение, p = nullptr
// auto r = p;              // ОШИБКА: unique_ptr нельзя копировать

// Кастомный делитер:
auto f = std::unique_ptr<FILE, decltype(&fclose)>(fopen("data.txt","r"), fclose);
```

**Правило**: функции принимают `unique_ptr` по значению или `&&` если берут владение, иначе — `T*` или `T&` (наблюдение).

### shared_ptr — разделённое владение

```cpp
auto sp1 = std::make_shared<MyClass>(args);  // 1 аллокация: объект + control block
auto sp2 = sp1;   // копия: счётчик = 2
sp1.reset();      // счётчик = 1
// когда счётчик = 0 — вызовется деструктор и память освободится

sp1.use_count();  // текущее значение счётчика (для отладки)
```

Под капотом: control block хранит `strong_count` (количество `shared_ptr`) и `weak_count` (количество `weak_ptr`). Инкремент/декремент — **атомарные операции** — это накладные расходы по сравнению с `unique_ptr`.

**`make_shared` vs `new`:**
```cpp
// make_shared: 1 аллокация (объект + control block вместе)
auto sp = std::make_shared<MyClass>();

// new: 2 аллокации
auto sp2 = std::shared_ptr<MyClass>(new MyClass());
```

Минус `make_shared`: если живут `weak_ptr`, control block держится до последнего `weak_ptr` — объект разрушен, но память блока не освобождена.

### Циклические зависимости и weak_ptr

```cpp
struct Node {
    int val;
    std::shared_ptr<Node> next;  // ПЛОХО: цикл = утечка памяти
};

struct SafeNode {
    int val;
    std::weak_ptr<SafeNode> next;  // ХОРОШО: weak_ptr рвёт цикл
};

// Использование weak_ptr:
std::weak_ptr<int> wp = sp;
if (auto locked = wp.lock()) {  // lock() вернёт shared_ptr или пустой
    *locked = 100;
}
```

`weak_ptr` не удерживает объект живым. Объект может быть уничтожен пока есть `weak_ptr`.

### enable_shared_from_this

```cpp
class Worker : public std::enable_shared_from_this<Worker> {
public:
    void schedule() {
        // Нужно передать shared_ptr на себя в callback:
        auto self = shared_from_this();  // правильно
        // auto bad = shared_ptr<Worker>(this);  // ПЛОХО: создаст второй control block
    }
};

// ВАЖНО: shared_from_this() работает только если объект уже управляется shared_ptr
auto w = std::make_shared<Worker>();
w->schedule();  // OK
```

### Правила выбора

| Сценарий | Тип |
|---|---|
| Единственный владелец | `unique_ptr` |
| Разделённое владение | `shared_ptr` |
| Наблюдение без владения | сырой `T*` или `T&` |
| Наблюдение за `shared_ptr` без удержания | `weak_ptr` |
| Не нужна динамическая аллокация | значение (не указатель) |
