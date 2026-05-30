0–10 мин. Куча в массиве.
Дерево неявно хранится в массиве: для узла i: левый потомок = 2i+1, правый = 2i+2, родитель = (i-1)/2.
Нарисуй дерево и соответствующий массив. Покажи что индексы работают.

10–50 мин. Реализуй MinHeap.
```cpp
template<typename T>
class MinHeap {
    std::vector<T> data_;

    void sift_up(size_t i) {
        while (i > 0) {
            size_t parent = (i - 1) / 2;
            if (data_[parent] <= data_[i]) break;
            std::swap(data_[parent], data_[i]);
            i = parent;
        }
    }

    void sift_down(size_t i) {
        size_t n = data_.size();
        while (true) {
            size_t smallest = i;
            size_t l = 2*i + 1, r = 2*i + 2;
            if (l < n && data_[l] < data_[smallest]) smallest = l;
            if (r < n && data_[r] < data_[smallest]) smallest = r;
            if (smallest == i) break;
            std::swap(data_[i], data_[smallest]);
            i = smallest;
        }
    }
public:
    void push(T val) { data_.push_back(std::move(val)); sift_up(data_.size()-1); }
    T top() const { return data_[0]; }
    void pop() { data_[0] = std::move(data_.back()); data_.pop_back(); sift_down(0); }
    size_t size() const { return data_.size(); }
    bool empty() const { return data_.empty(); }
};
```
Объясни sift_up (после push) и sift_down (после pop). Нарисуй на бумаге.

50–65 мин. Build heap — O(n).
```cpp
// Превратить произвольный массив в кучу:
for (int i = data_.size()/2 - 1; i >= 0; i--) sift_down(i);
```
Почему O(n): большинство узлов — листья, им sift_down почти ничего не стоит. Математически: сумма высот всех узлов = O(n).

65–80 мин. Heap sort и priority_queue.
Heap sort: build_heap O(n) + n раз pop O(log n) = O(n log n). In-place, не требует доп. памяти.
`std::priority_queue`: max-heap по умолчанию. `priority_queue<int, vector<int>, greater<int>>` → min-heap.

80–90 мин. Выдача ДЗ.
