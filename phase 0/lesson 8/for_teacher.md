0–5 мин. Зачем это всё.
Обсуди: `const`-корректность — способ задокументировать инварианты прямо в типах. Компилятор проверяет их. `mutable` — легальный способ кэшировать что-то в const-объекте (например, ленивое вычисление или мьютекс). Пример: кэширование хэша в const строке.

5–25 мин. const слева и справа.
Самая путаная часть: `const int*` vs `int* const`.
Правило читать справа налево: «указатель на const int» vs «const указатель на int».
```cpp
const int* p = &x;    // указатель на const int — *p нельзя изменить
int* const p = &x;    // const указатель — p нельзя переназначить
const int* const p = &x;  // ни то, ни другое
```
Покажи примеры кода, пусть сама угадывает что компилируется.
Верхний const (top-level) при передаче по значению игнорируется: `void f(const int x)` = `void f(int x)` в сигнатуре.
Низкоуровневый const (low-level): `const int*` — важен, влияет на перегрузку.

25–50 мин. const-методы и mutable.
```cpp
class Cache {
    mutable std::unordered_map<int, int> cache_;  // изменяется даже в const-методе
    int compute(int x) const {
        auto it = cache_.find(x);
        if (it != cache_.end()) return it->second;
        int result = heavy_compute(x);
        cache_[x] = result;  // OK: cache_ — mutable
        return result;
    }
};
```
Правило: const-метод не изменяет **наблюдаемое** состояние объекта (логическая константность). `mutable` используется для вещей, которые — детали реализации (кэш, мьютекс, счётчик обращений).

50–65 мин. constexpr, consteval, constinit.
```cpp
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}
constexpr int f120 = factorial(120);  // вычислено на этапе компиляции

consteval int must_be_compile_time(int n) { return n * 2; }  // C++20: только compile-time

constinit int global = factorial(5);  // C++20: гарантирует инициализацию до main
```
Объясни разницу: `constexpr` может выполняться и compile-time и runtime. `consteval` — только compile-time. `constinit` — переменная инициализирована статически (не в runtime), но не обязательно const.

65–80 мин. volatile — мифы и реальность.
`volatile` говорит компилятору не кэшировать переменную в регистрах — читать из памяти каждый раз. Используется:
- Для hardware-регистров (встраиваемые системы).
- Для переменных изменяемых прерываниями (в C, реже в C++).
**НЕ используется для многопоточности**: `volatile` не создаёт memory barrier и не является атомарным. Для потоков — `std::atomic`.

80–90 мин. Выдача ДЗ.
