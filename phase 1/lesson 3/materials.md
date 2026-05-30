### Односвязный список

```cpp
template<typename T>
class SinglyLinkedList {
    struct Node {
        T data;
        Node* next;
        Node(T d, Node* n = nullptr) : data(std::move(d)), next(n) {}
    };
    Node* head_ = nullptr;
    size_t size_ = 0;

public:
    ~SinglyLinkedList() { while (head_) pop_front(); }

    void push_front(T val) {
        head_ = new Node(std::move(val), head_);
        ++size_;
    }

    void push_back(T val) {
        if (!head_) { push_front(std::move(val)); return; }
        Node* curr = head_;
        while (curr->next) curr = curr->next;
        curr->next = new Node(std::move(val));
        ++size_;
    }

    T pop_front() {
        Node* old = head_;
        T val = std::move(old->data);
        head_ = head_->next;
        delete old;
        --size_;
        return val;
    }

    size_t size() const { return size_; }
    bool empty() const { return size_ == 0; }
};
```

### Двусвязный список с итератором

```cpp
template<typename T>
class DoublyLinkedList {
    struct Node {
        T data;
        Node *prev, *next;
    };
    Node* head_ = nullptr;
    Node* tail_ = nullptr;
    size_t size_ = 0;

public:
    struct Iterator {
        Node* node_;
        T& operator*() { return node_->data; }
        Iterator& operator++() { node_ = node_->next; return *this; }
        Iterator& operator--() { node_ = node_->prev; return *this; }
        bool operator!=(const Iterator& o) const { return node_ != o.node_; }
    };

    Iterator begin() { return {head_}; }
    Iterator end() { return {nullptr}; }

    // Вставить перед итератором — O(1):
    Iterator insert(Iterator pos, T val) {
        Node* n = new Node{std::move(val), pos.node_->prev, pos.node_};
        if (n->prev) n->prev->next = n;
        else head_ = n;
        n->next->prev = n;
        ++size_;
        return {n};
    }

    // Удалить по итератору — O(1):
    Iterator erase(Iterator pos) {
        Node* n = pos.node_;
        if (n->prev) n->prev->next = n->next;
        else head_ = n->next;
        if (n->next) n->next->prev = n->prev;
        else tail_ = n->prev;
        Iterator next{n->next};
        delete n;
        --size_;
        return next;
    }
};
```

### Сравнение vector vs list

| Операция | vector | list |
|---|---|---|
| push_back | O(1) amort | O(1) |
| push_front | O(n) | O(1) |
| Вставка в середину по итератору | O(n) сдвиг | O(1) |
| Поиск итератора | O(n) | O(n) |
| Random access | O(1) | O(n) |
| Cache performance | Отличная | Плохая |

**Вывод**: `list` полезен только когда итераторы на элементы должны оставаться валидными при вставке/удалении, и при этом random access не нужен. На практике встречается редко.
