### Бинарная куча в массиве

Полное бинарное дерево хранится в массиве: индекс i имеет детей 2i+1 и 2i+2, родителя (i-1)/2.

```
Массив: [1, 3, 2, 7, 8, 5, 4]
Дерево:         1
              /   \
             3     2
            / \   / \
           7   8 5   4
```

**Heap property** (min-heap): `data_[parent] ≤ data_[child]` для всех узлов.

### MinHeap — реализация

```cpp
template<typename T>
class MinHeap {
    std::vector<T> data_;

    void sift_up(size_t i) {
        while (i > 0) {
            size_t p = (i - 1) / 2;
            if (data_[p] <= data_[i]) break;
            std::swap(data_[p], data_[i]);
            i = p;
        }
    }

    void sift_down(size_t i) {
        size_t n = data_.size();
        while (true) {
            size_t smallest = i;
            size_t l = 2*i+1, r = 2*i+2;
            if (l < n && data_[l] < data_[smallest]) smallest = l;
            if (r < n && data_[r] < data_[smallest]) smallest = r;
            if (smallest == i) break;
            std::swap(data_[i], data_[smallest]);
            i = smallest;
        }
    }

public:
    void push(T val) { data_.push_back(std::move(val)); sift_up(data_.size()-1); }
    const T& top() const { return data_[0]; }
    void pop() { data_[0] = std::move(data_.back()); data_.pop_back(); if (!empty()) sift_down(0); }
    bool empty() const { return data_.empty(); }
    size_t size() const { return data_.size(); }

    // Build heap: O(n)
    void build(std::vector<T> v) {
        data_ = std::move(v);
        for (int i = (int)data_.size()/2 - 1; i >= 0; i--) sift_down(i);
    }
};
```

### Сложности

| Операция | Сложность |
|---|---|
| push | O(log n) |
| pop | O(log n) |
| top | O(1) |
| build_heap | O(n) |

Build heap O(n): большинство узлов — листья. Суммарная «работа» sift_down по уровням = O(n).

### std::priority_queue

```cpp
// Max-heap (по умолчанию):
std::priority_queue<int> max_h;
max_h.push(3); max_h.push(1); max_h.push(4);
max_h.top();   // 4

// Min-heap:
std::priority_queue<int, std::vector<int>, std::greater<int>> min_h;

// Кастомный компаратор:
auto cmp = [](const Task& a, const Task& b) { return a.priority < b.priority; };
std::priority_queue<Task, std::vector<Task>, decltype(cmp)> task_queue(cmp);
```
