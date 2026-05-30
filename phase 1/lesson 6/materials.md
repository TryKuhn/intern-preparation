### Рехеширование

При превышении `max_load_factor` (обычно 0.75–1.0) хеш-таблица создаёт новый массив buckets вдвое большего размера и перевставляет все элементы.

```cpp
void rehash(size_t new_cap) {
    std::vector<Bucket> new_buckets(new_cap);
    for (auto& bucket : buckets_) {
        for (auto& entry : bucket) {
            size_t new_idx = hash_key(entry.key) % new_cap;
            new_buckets[new_idx].push_back(std::move(entry));
        }
    }
    buckets_ = std::move(new_buckets);
}
```

**Амортизированная сложность вставки:**

Рехеширование удваивает размер → элементы перехешируются реже. Для n вставок суммарно:
```
Перехеширования на размерах: 1, 2, 4, 8, ... → сумма ≈ 2n
```
Итого O(n) для n вставок → O(1) амортизированно.

### HashMap с рехешированием (полная версия)

```cpp
template<typename K, typename V>
class HashMap {
    struct Entry { K key; V value; };
    using Bucket = std::list<Entry>;

    std::vector<Bucket> buckets_;
    size_t size_ = 0;
    size_t seed_;
    static constexpr float MAX_LOAD = 0.75f;

    size_t hash_key(const K& key) const {
        return std::hash<K>{}(key) ^ seed_;
    }

    size_t idx(const K& key) const {
        return hash_key(key) % buckets_.size();
    }

    void rehash(size_t new_cap) {
        std::vector<Bucket> nb(new_cap);
        for (auto& b : buckets_)
            for (auto& e : b)
                nb[hash_key(e.key) % new_cap].push_back(std::move(e));
        buckets_ = std::move(nb);
    }

public:
    HashMap(size_t cap = 16)
        : buckets_(cap), seed_(std::random_device{}()) {}

    void insert(K key, V value) {
        if (load_factor() > MAX_LOAD) rehash(buckets_.size() * 2);
        auto& bucket = buckets_[idx(key)];
        for (auto& e : bucket)
            if (e.key == key) { e.value = std::move(value); return; }
        bucket.push_back({std::move(key), std::move(value)});
        ++size_;
    }
    // ... find, remove, operator[] — как в предыдущем уроке

    float load_factor() const { return (float)size_ / buckets_.size(); }
    size_t bucket_count() const { return buckets_.size(); }
};
```

### Hash Flood Attack

Злоумышленник подбирает ключи с одинаковым хешем → все попадают в один bucket → вставка/поиск деградируют до O(n).

**Защита**: случайный seed добавляется к хешу при инициализации. `std::unordered_map` в большинстве реализаций использует похожий механизм.

### Сложности хеш-таблицы

| Операция | Среднее | Худшее |
|---|---|---|
| insert | O(1) amort | O(n) |
| find | O(1) | O(n) |
| remove | O(1) | O(n) |

Худший случай — при плохой хеш-функции или hash flood. В нормальных условиях практически всегда O(1).
