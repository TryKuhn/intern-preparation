0–10 мин. Кэш и реальность.
Обсуди вопрос: связный список теоретически O(1) для вставки по итератору, но на практике медленнее вектора во многих задачах из-за cache misses. Каждый узел — отдельная аллокация, данные разбросаны по памяти. Вектор — непрерывный, cache-friendly. В реальном коде `std::list` используется редко.

10–50 мин. Реализуй SinglyLinkedList.
```cpp
template<typename T>
class SinglyLinkedList {
    struct Node {
        T data;
        Node* next;
        Node(T d, Node* n = nullptr) : data(std::move(d)), next(n) {}
    };
    Node* head_;
    size_t size_;
public:
    void push_front(T val);  // O(1)
    void push_back(T val);   // O(n) без tail
    T pop_front();           // O(1)
    // iterator support
};
```
Реализуй вместе push_front, push_back, pop_front. Нарисуй на бумаге что происходит с указателями.
Ключевые моменты: порядок операций с указателями при вставке/удалении. Удалить узел в середине — нужен указатель на предыдущий.

50–75 мин. DoublyLinkedList и итератор.
Добавить `Node* prev`. Реализовать insert/erase по итератору за O(1).
```cpp
struct Iterator {
    Node* node_;
    T& operator*() { return node_->data; }
    Iterator& operator++() { node_ = node_->next; return *this; }
    bool operator!=(const Iterator& o) { return node_ != o.node_; }
};
```
Показать что для range-for нужны begin() и end().

75–90 мин. Выдача ДЗ.
