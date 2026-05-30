0–10 мин. Что лучше: массив или список.
Обсуди: массив лучше по производительности (cache locality), но ограничен по размеру или требует реаллокации. Список — динамический, но cache-unfriendly. `std::stack` и `std::queue` — адаптеры над `deque` по умолчанию, можно задать любой underlying container.

10–40 мин. Stack на массиве.
```cpp
template<typename T>
class Stack {
    std::vector<T> data_;  // или фиксированный массив
public:
    void push(T val) { data_.push_back(std::move(val)); }
    T& top() { return data_.back(); }
    void pop() { data_.pop_back(); }
    bool empty() const { return data_.empty(); }
    size_t size() const { return data_.size(); }
};
```
Показать: push/pop — O(1) амортизированно через vector.
Практика: использовать стек для проверки скобочных последовательностей.

40–75 мин. Очередь на кольцевом буфере.
Ключевая идея: head и tail движутся по кольцу, не нужно сдвигать элементы.
```
[_, e2, e3, e4, _, _, e0, e1]  capacity=8
         ^tail              ^head
Добавить: data_[tail_++], tail_ %= capacity_
Удалить: data_[head_++], head_ %= capacity_
```
```cpp
template<typename T>
class CircularQueue {
    std::vector<T> data_;
    size_t head_ = 0, tail_ = 0, size_ = 0;
public:
    CircularQueue(size_t capacity) : data_(capacity) {}
    void enqueue(T val) {
        data_[tail_] = std::move(val);
        tail_ = (tail_ + 1) % data_.size();
        ++size_;
    }
    T dequeue() {
        T val = std::move(data_[head_]);
        head_ = (head_ + 1) % data_.size();
        --size_;
        return val;
    }
};
```
Нарисуй на бумаге как tail и head движутся.

75–90 мин. Выдача ДЗ.
