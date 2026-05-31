### std::thread

```cpp
#include <thread>
std::thread t(function, arg1, arg2);  // запустить
t.join();       // ждать завершения
t.detach();     // отпустить в фоне (нельзя join после)
t.joinable();   // можно ли join
```

### Мьютексы и RAII-обёртки

```cpp
std::mutex mtx;

// lock_guard — простой RAII, нельзя unlock вручную:
{ std::lock_guard<std::mutex> lock(mtx); /* critical section */ }

// unique_lock — гибкий, нужен для condition_variable:
std::unique_lock<std::mutex> lock(mtx);
lock.unlock();
lock.lock();

// scoped_lock (C++17) — для нескольких мьютексов без дедлока:
std::scoped_lock lock(mtx1, mtx2);
```

### condition_variable

```cpp
std::condition_variable cv;
std::mutex mtx;
bool ready = false;

// Ожидать:
std::unique_lock lock(mtx);
cv.wait(lock, []{ return ready; });  // предикат защищает от ложных пробуждений

// Уведомить:
{ std::lock_guard lock(mtx); ready = true; }
cv.notify_one();  // разбудить одного
cv.notify_all();  // разбудить всех
```

### std::atomic

```cpp
std::atomic<int> counter{0};
counter++;              // seq_cst по умолчанию
counter.fetch_add(1);   // аналогично
counter.load();         // прочитать
counter.store(5);       // записать
counter.exchange(7);    // прочитать и записать
counter.compare_exchange_strong(expected, desired);  // CAS
```

Модели памяти: `relaxed` (только атомарность) < `acquire/release` (синхронизация пар) < `seq_cst` (полный порядок, дефолт).

### Семафоры C++20

```cpp
#include <semaphore>
std::counting_semaphore<10> sem{3};  // max 10, начальное значение 3
sem.acquire();  // уменьшить (ждать если 0)
sem.release();  // увеличить
```

### Дедлок и std::lock

```cpp
// Дедлок: t1 держит mtx1, ждёт mtx2; t2 держит mtx2, ждёт mtx1

// Решение 1: std::lock — атомарный захват нескольких
std::lock(mtx1, mtx2);
std::lock_guard l1(mtx1, std::adopt_lock);
std::lock_guard l2(mtx2, std::adopt_lock);

// Решение 2: scoped_lock (C++17)
std::scoped_lock lock(mtx1, mtx2);
```

### thread_local

```cpp
thread_local int per_thread_counter = 0;  // своя копия в каждом потоке
// Не нужен мьютекс для thread_local переменных
```
