### Три шаблона бинарного поиска

```cpp
// 1. Найти точное значение (возвращает -1 если нет):
int binary_search(const vector<int>& a, int target) {
    int lo = 0, hi = (int)a.size() - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;  // НЕ (lo+hi)/2 — переполнение!
        if (a[mid] == target) return mid;
        else if (a[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}

// 2. lower_bound — первый индекс >= target (или n если нет такого):
int lower_bound(const vector<int>& a, int target) {
    int lo = 0, hi = a.size();  // полуоткрытый интервал [lo, hi)
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] < target) lo = mid + 1;
        else hi = mid;
    }
    return lo;
}

// 3. upper_bound — первый индекс > target:
int upper_bound(const vector<int>& a, int target) {
    int lo = 0, hi = a.size();
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] <= target) lo = mid + 1;
        else hi = mid;
    }
    return lo;
}
```

**Использование:**
```cpp
auto it = lower_bound(v.begin(), v.end(), 5);  // первый >= 5
auto it = upper_bound(v.begin(), v.end(), 5);  // первый > 5
// Количество вхождений 5: upper_bound - lower_bound
```

### Бинарный поиск по ответу

Шаблон: ответ монотонен (false...false, true...true). Ищем первый true.

```cpp
// Найти минимальный x при котором condition(x) выполняется:
long long lo = min_possible, hi = max_possible;
while (lo < hi) {
    long long mid = lo + (hi - lo) / 2;
    if (condition(mid)) hi = mid;  // mid может быть ответом
    else lo = mid + 1;             // mid точно не ответ
}
return lo;  // lo == hi == ответ
```

**Пример**: Koko eating bananas (LC 875) — минимальная скорость k:
```cpp
auto can_finish = [&](int speed) {
    int hours = 0;
    for (int pile : piles) hours += (pile + speed - 1) / speed;
    return hours <= h;
};
int lo = 1, hi = *max_element(piles.begin(), piles.end());
while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (can_finish(mid)) hi = mid;
    else lo = mid + 1;
}
return lo;
```

### Частые баги

1. `mid = (lo + hi) / 2` → переполнение если lo+hi > INT_MAX. Используй `lo + (hi-lo)/2`.
2. Бесконечный цикл: если `hi = mid` и `lo == hi-1`, то `mid = lo`, `hi = lo` → правильно. Если `hi = mid - 1` при `lo < hi` → можно пропустить элемент.
3. `lo <= hi` (закрытый интервал) vs `lo < hi` (полуоткрытый) — разные инварианты, не смешивать.
