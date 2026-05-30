### Stack на массиве (через vector)

```cpp
template<typename T>
class Stack {
    std::vector<T> data_;
public:
    void push(T val) { data_.push_back(std::move(val)); }
    T& top() { if (empty()) throw std::underflow_error("stack empty"); return data_.back(); }
    const T& top() const { return data_.back(); }
    void pop() { if (empty()) throw std::underflow_error("stack empty"); data_.pop_back(); }
    bool empty() const { return data_.empty(); }
    size_t size() const { return data_.size(); }
};
```

Все операции O(1) амортизированно (через vector). Cache-friendly.

### Queue на кольцевом буфере

Проблема наивной реализации очереди на массиве: при dequeue нужно сдвигать все элементы → O(n). Решение — кольцевой буфер.

```cpp
template<typename T>
class CircularQueue {
    std::vector<T> buf_;
    size_t head_ = 0, tail_ = 0, size_ = 0;

public:
    explicit CircularQueue(size_t capacity) : buf_(capacity) {}

    void enqueue(T val) {
        if (size_ == buf_.size()) throw std::overflow_error("queue full");
        buf_[tail_] = std::move(val);
        tail_ = (tail_ + 1) % buf_.size();
        ++size_;
    }

    T dequeue() {
        if (empty()) throw std::underflow_error("queue empty");
        T val = std::move(buf_[head_]);
        head_ = (head_ + 1) % buf_.size();
        --size_;
        return val;
    }

    const T& front() const { return buf_[head_]; }
    bool empty() const { return size_ == 0; }
    size_t size() const { return size_; }
    size_t capacity() const { return buf_.size(); }
};
```

Визуализация кольцевого буфера (capacity=8):
```
Состояние:  [_, _, e2, e3, e4, e5, e0, e1]
индексы:     0  1   2   3   4   5   6   7
head=6, tail=2, size=4
```

После `enqueue(e6)`:
```
[_, _, e2, e3, e4, e5, e0, e1]  → buf_[2] = e6, tail = (2+1)%8 = 3
```

Все операции O(1) без амортизации.

### std::stack и std::queue

```cpp
#include <stack>
#include <queue>

std::stack<int> s;               // на deque
std::stack<int, std::vector<int>> sv; // на vector
s.push(1); s.push(2);
s.top(); s.pop();

std::queue<int> q;               // на deque
q.push(1); q.push(2);
q.front(); q.back(); q.pop();
```
