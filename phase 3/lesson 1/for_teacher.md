0–5 мин. Только чтение — не гонка.
Гонка данных: одновременное чтение И запись, или две записи без синхронизации. Только чтение — безопасно. Это важно: read-only данные не нужно защищать мьютексом.

5–30 мин. std::thread, mutex, RAII-обёртки.
```cpp
#include <thread>
#include <mutex>

std::mutex mtx;
int counter = 0;

void increment(int n) {
    for (int i = 0; i < n; i++) {
        std::lock_guard<std::mutex> lock(mtx);  // RAII: unlock при уничтожении
        ++counter;
    }
}

int main() {
    std::thread t1(increment, 1000000);
    std::thread t2(increment, 1000000);
    t1.join(); t2.join();  // join = ждём завершения
    std::cout << counter;  // должен быть 2000000
}
```
lock_guard: захватывает при конструировании, освобождает при уничтожении. unique_lock: можно unlock/lock вручную (нужен для condition_variable). scoped_lock: для захвата нескольких мьютексов без дедлока.

30–55 мин. condition_variable и producer-consumer.
```cpp
std::queue<int> q;
std::mutex mtx;
std::condition_variable cv;
bool done = false;

void producer() {
    for (int i = 0; i < 10; i++) {
        { std::lock_guard lock(mtx); q.push(i); }
        cv.notify_one();
    }
    { std::lock_guard lock(mtx); done = true; }
    cv.notify_all();
}

void consumer() {
    while (true) {
        std::unique_lock lock(mtx);
        cv.wait(lock, [&]{ return !q.empty() || done; });  // ложные пробуждения!
        if (q.empty() && done) break;
        int val = q.front(); q.pop();
        lock.unlock();
        process(val);
    }
}
```
Ложные пробуждения: `wait` с лямбдой-предикатом защищает от них.

55–70 мин. atomic и модели памяти.
```cpp
std::atomic<int> cnt{0};
cnt.fetch_add(1, std::memory_order_relaxed);  // только атомарность, без sync
cnt.fetch_add(1, std::memory_order_acquire);   // acquire: всё после видит
cnt.fetch_add(1, std::memory_order_release);   // release: всё до видно
cnt.fetch_add(1, std::memory_order_seq_cst);   // полная упорядоченность (дефолт)
```
Гонка данных = UB. Даже `i++` для обычного int — не атомарно.
volatile — НЕ для потоков (только для hardware registers).

70–82 мин. Семафоры C++20, дедлок, thread_local. Выдача ДЗ.
