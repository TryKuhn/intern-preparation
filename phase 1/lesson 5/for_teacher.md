0–10 мин. О(1) vs O(log n).
Объясни: сортированный массив позволяет сузить поиск вдвое каждый шаг. Хеш-таблица вычисляет адрес напрямую через функцию. Нет итерации — O(1). Но: нет упорядоченности (нельзя range query), зависит от качества хеш-функции.

10–35 мин. Хеш-функции и свойства.
Хорошая хеш-функция:
1. Детерминирована (одинаковый вход → одинаковый выход).
2. Равномерно распределяет значения.
3. Быстро вычисляется.

```cpp
// Простая хеш-функция для int:
size_t hash(int key, size_t capacity) { return key % capacity; }

// Хеш-функция для строки (djb2):
size_t hash_str(const std::string& s, size_t capacity) {
    size_t h = 5381;
    for (char c : s) h = h * 33 + c;
    return h % capacity;
}
```

35–70 мин. Разрешение коллизий: chaining.
Chaining: каждый bucket — список элементов с одинаковым хешем.
```
hash("alice") = 2  → bucket[2]: alice→30 → null
hash("bob")   = 5  → bucket[5]: bob→25 → null
hash("carol") = 2  → bucket[2]: carol→35 → alice→30 → null (коллизия!)
```
Реализуй вместе:
```cpp
template<typename K, typename V>
class HashMap {
    struct Pair { K key; V value; };
    std::vector<std::list<Pair>> buckets_;
    size_t size_;
    float load_factor() { return (float)size_ / buckets_.size(); }
    size_t bucket_index(const K& key) { return std::hash<K>{}(key) % buckets_.size(); }
public:
    HashMap(size_t capacity = 16) : buckets_(capacity), size_(0) {}
    void insert(K key, V value);
    V* find(const K& key);
    bool remove(const K& key);
};
```

70–82 мин. load_factor и производительность.
Показать на графике: при load_factor = 1 (один элемент на bucket) → O(1). При load_factor = 10 → O(10) в среднем. Стандарт: при load_factor > 0.75 → рехеширование (следующий урок).

82–90 мин. Выдача ДЗ.
