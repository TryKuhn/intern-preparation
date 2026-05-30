### Амортизированный анализ

**Метод потенциала**: при удвоении capacity стоимость реаллокации «раскладывается» на все предыдущие cheap-операции. Каждый push_back «платит» 2 единицы: одну за себя, одну в «фонд» на будущую реаллокацию.

Стоимость n операций push_back:
- Реаллокации на размерах: 1, 2, 4, 8, ..., n → суммарно O(n) копирований
- Всего: O(n), значит одна операция — O(1) амортизированно

Рост на +1 вместо ×2 дал бы: 1+2+3+...+n = O(n²) — неприемлемо.

### Устройство vector

```
data_:    [ e0 | e1 | e2 | e3 | ?? | ?? | ?? | ?? ]
             |size_=4                    |capacity_=8
```

- `size_`: количество реальных элементов
- `capacity_`: выделенная память (в элементах)
- Когда `size_ == capacity_` при push_back → реаллокация (обычно ×2)

### Скелет MyVector

```cpp
template<typename T>
class MyVector {
    T* data_;
    size_t size_, capacity_;

    void grow() {
        size_t new_cap = capacity_ == 0 ? 1 : capacity_ * 2;
        T* new_data = static_cast<T*>(::operator new(new_cap * sizeof(T)));
        // Переместить элементы:
        for (size_t i = 0; i < size_; ++i) {
            new(new_data + i) T(std::move(data_[i]));  // placement new + move
            data_[i].~T();                              // явный деструктор
        }
        ::operator delete(data_);
        data_ = new_data;
        capacity_ = new_cap;
    }

public:
    MyVector() : data_(nullptr), size_(0), capacity_(0) {}

    ~MyVector() {
        for (size_t i = 0; i < size_; ++i) data_[i].~T();
        ::operator delete(data_);
    }

    void push_back(T val) {  // принять по значению, переместить
        if (size_ == capacity_) grow();
        new(data_ + size_) T(std::move(val));
        ++size_;
    }

    void reserve(size_t n) {
        if (n <= capacity_) return;
        // аналогично grow, но до конкретного размера
    }

    T& operator[](size_t i) { return data_[i]; }
    size_t size() const { return size_; }
    size_t capacity() const { return capacity_; }
};
```

### Инвалидация итераторов

При реаллокации все итераторы, указатели и ссылки на элементы вектора становятся невалидными. `reserve(n)` предотвращает реаллокацию до n элементов.
