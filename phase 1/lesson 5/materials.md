### Хеш-функция

Хеш-функция отображает ключ в индекс массива. Требования:
1. **Детерминизм**: `hash(k)` всегда одинаков для одного `k`.
2. **Равномерность**: равномерно заполняет диапазон.
3. **Скорость**: O(1) вычисление.

```cpp
// Для int:
size_t hash_int(int key, size_t cap) { return (size_t)key % cap; }

// djb2 для строк:
size_t hash_str(std::string_view s, size_t cap) {
    size_t h = 5381;
    for (unsigned char c : s) h = h * 33 + c;
    return h % cap;
}

// Стандартный:
std::hash<std::string>{}("hello");
```

### Chaining — разрешение коллизий

Каждый bucket — список пар (ключ, значение). При коллизии: новый элемент добавляется в список.

```cpp
template<typename K, typename V>
class HashMap {
    struct Entry { K key; V value; };
    using Bucket = std::list<Entry>;

    std::vector<Bucket> buckets_;
    size_t size_ = 0;

    size_t idx(const K& key) const {
        return std::hash<K>{}(key) % buckets_.size();
    }

public:
    explicit HashMap(size_t cap = 16) : buckets_(cap) {}

    void insert(K key, V value) {
        auto& bucket = buckets_[idx(key)];
        for (auto& e : bucket) {
            if (e.key == key) { e.value = std::move(value); return; }
        }
        bucket.push_back({std::move(key), std::move(value)});
        ++size_;
    }

    V* find(const K& key) {
        auto& bucket = buckets_[idx(key)];
        for (auto& e : bucket)
            if (e.key == key) return &e.value;
        return nullptr;
    }

    bool remove(const K& key) {
        auto& bucket = buckets_[idx(key)];
        for (auto it = bucket.begin(); it != bucket.end(); ++it) {
            if (it->key == key) { bucket.erase(it); --size_; return true; }
        }
        return false;
    }

    float load_factor() const { return (float)size_ / buckets_.size(); }
    size_t size() const { return size_; }
};
```

### Load factor

`load_factor = size / bucket_count`

| Load factor | Среднее число шагов в bucket | Сложность |
|---|---|---|
| 0.5 | 0.5 | O(1) |
| 1.0 | 1.0 | O(1) |
| 5.0 | 5.0 | O(1) (но медленно) |
| n/1 | n | O(n) — вырождение |

`std::unordered_map` рехеширует при load_factor > 1.0 (по умолчанию max_load_factor = 1.0).

### Open Addressing (для сравнения)

Вместо списков — линейное/квадратичное/двойное пробирование по массиву. Лучше кэш-поведение, но сложнее реализация и удаление.

```
Linear probing: если bucket[h] занят → попробовать h+1, h+2, ...
```
