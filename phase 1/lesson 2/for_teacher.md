0–10 мин. Амортизированный анализ.
Объясни на примере: при заполнении вектора происходят редкие дорогие (O(n)) реаллокации и частые дешёвые (O(1)) вставки. Суммарная стоимость n операций — O(n), значит одна операция «в среднем» O(1).
Ключевой момент: удвоение capacity (×2) гарантирует O(1) амортизированно. Рост на константу (+1) даст O(n²).

10–60 мин. Реализуй MyVector.
Реализуйте вместе прямо на занятии:
```cpp
template<typename T>
class MyVector {
    T* data_;
    size_t size_, capacity_;
public:
    MyVector() : data_(nullptr), size_(0), capacity_(0) {}
    ~MyVector() { /* placement destroy + deallocate */ }
    void push_back(const T& val);
    void push_back(T&& val);
    T& operator[](size_t i) { return data_[i]; }
    size_t size() const { return size_; }
    size_t capacity() const { return capacity_; }
    void reserve(size_t n);
private:
    void grow();  // удвоить capacity
};
```
Важные моменты при реализации:
- В `grow()`: выделить новый блок, placement-new переместить элементы, уничтожить старые.
- Почему `noexcept` move важен для корректности `grow()`.
- Показать что после каждого grow число элементов = половина capacity.

60–75 мин. Тестирование.
Добавить логирование в grow(). Запустить push_back 1..100, посмотреть когда происходят реаллокации (2, 4, 8, 16, 32, 64, 128).
Сравнить с `std::vector` — проверить что поведение идентично.

75–90 мин. Обсудить reserve и SBO. Выдать ДЗ.
