### const: верхний и низкоуровневый

**Верхний const (top-level)** — константность самого объекта/указателя:
```cpp
int* const p = &x;     // p нельзя переназначить, *p изменяем
const int ci = 5;      // ci нельзя изменить
```

**Низкоуровневый const (low-level)** — константность того, на что указывают:
```cpp
const int* p = &x;     // *p нельзя изменить через p, но p можно переназначить
```

Таблица:
```cpp
int x = 5, y = 10;

int* p1 = &x;           // изменяем и указатель, и объект
const int* p2 = &x;     // нельзя *p2 = ..., можно p2 = &y
int* const p3 = &x;     // можно *p3 = ..., нельзя p3 = &y
const int* const p4 = &x; // нельзя ни то, ни другое
```

При передаче по значению top-level const не влияет на сигнатуру:
```cpp
void f(const int x);  // то же что void f(int x) для перегрузки
```

### const в классах

```cpp
class Counter {
    int count_;
    mutable int access_log_;  // изменяется даже в const-методах
public:
    int get() const {           // const-метод
        ++access_log_;          // OK: mutable
        return count_;          // OK: только читаем
    }
    void increment() {          // не-const метод
        ++count_;
    }
};
```

**Логическая vs битовая константность:**
- **Битовая**: метод не меняет ни одного бита объекта (что проверяет компилятор через `const`).
- **Логическая**: метод не меняет наблюдаемое состояние (что должен гарантировать разработчик). `mutable` позволяет нарушить битовую, сохраняя логическую.

### constexpr, consteval, constinit (C++20)

```cpp
constexpr double pi = 3.14159265358979;  // вычислено compile-time

constexpr int power(int base, int exp) {
    int result = 1;
    for (int i = 0; i < exp; ++i) result *= base;
    return result;
}
constexpr int p = power(2, 10);  // 1024 — compile-time
int r = power(2, n);             // runtime: n неизвестно компилятору

consteval int square(int x) { return x * x; }  // C++20
// square(5) — OK (compile-time literal)
// int n = 5; square(n) — ОШИБКА: n не constexpr

constinit int initialized = power(2, 8);  // C++20: static initialization, не const
```

| Ключевое слово | Compile-time | Runtime | Изменяемый |
|---|---|---|---|
| `const` | Нет | Да | Нет (если инициализирован константой) |
| `constexpr` | Да | Да | Нет |
| `consteval` | Только | Нет | Нет |
| `constinit` | Инициализация статически | Да (чтение) | Да |

### volatile — что делает и что не делает

```cpp
volatile int hardware_reg = 0;  // читать из памяти, не из регистра
while (hardware_reg == 0) {}    // компилятор не оптимизирует в бесконечный цикл

// volatile НЕ для многопоточности:
volatile int shared = 0;
// thread1: shared = 1;
// thread2: if (shared == 1) ...  // гонка данных — UB! volatile не помогает
// Нужен std::atomic<int> shared = 0;
```

`volatile` запрещает компилятору кэшировать значение в регистрах, но не создаёт memory fence и не является атомарным. В современном C++ используется редко — почти всегда нужен `std::atomic`.
