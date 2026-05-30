0–10 мин. Амортизация рехеширования.
Объясни аналогию с вектором: рехеширование происходит редко (при load_factor > threshold), каждый раз удваивает размер. Суммарная стоимость n вставок — O(n). Одна вставка — O(1) амортизированно.

10–50 мин. Добавь рехеширование в HashMap из урока 1.5.
```cpp
void insert(K key, V value) {
    if (load_factor() > 0.75f) rehash(buckets_.size() * 2);
    // ... остальной insert
}

void rehash(size_t new_cap) {
    std::vector<Bucket> new_buckets(new_cap);
    for (auto& bucket : buckets_) {
        for (auto& entry : bucket) {
            size_t new_idx = std::hash<K>{}(entry.key) % new_cap;
            new_buckets[new_idx].push_back(std::move(entry));
        }
    }
    buckets_ = std::move(new_buckets);
}
```
Важно: при рехешировании все хеши пересчитываются. Индекс `entry.key % old_capacity` ≠ `entry.key % new_capacity`.

50–70 мин. Hash flood attack.
Злоумышленник может подобрать ключи с одинаковым хешем → все в один bucket → O(n) для каждой операции.
Решение: рандомизированный seed (к хешу добавляется случайное число при инициализации таблицы). `std::unordered_map` делает это по умолчанию начиная с определённых реализаций.
```cpp
HashMap(size_t cap = 16) : buckets_(cap), seed_(random_seed()) {}
size_t idx(const K& key) const {
    return (std::hash<K>{}(key) ^ seed_) % buckets_.size();
}
```

70–82 мин. Worst case O(n) — когда всё плохо.
Даже с хорошей хеш-функцией худший случай O(n) — все ключи попадают в один bucket. Поэтому для safety-критичных приложений — либо random seed, либо другая структура данных.

82–90 мин. Выдача ДЗ.
