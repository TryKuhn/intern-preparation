### Merge Sort — O(n log n), стабильная

```cpp
void merge_sort(vector<int>& a, int lo, int hi) {  // [lo, hi)
    if (hi - lo <= 1) return;
    int mid = lo + (hi - lo) / 2;
    merge_sort(a, lo, mid);
    merge_sort(a, mid, hi);
    // Слить [lo, mid) и [mid, hi):
    vector<int> tmp;
    int i = lo, j = mid;
    while (i < mid && j < hi)
        tmp.push_back(a[i] <= a[j] ? a[i++] : a[j++]);
    while (i < mid) tmp.push_back(a[i++]);
    while (j < hi) tmp.push_back(a[j++]);
    copy(tmp.begin(), tmp.end(), a.begin() + lo);
}
```

T(n) = 2T(n/2) + O(n) → **O(n log n)**. Требует O(n) доп. памяти.

### Quick Sort — O(n log n) avg, O(n²) worst

```cpp
int partition(vector<int>& a, int lo, int hi) {
    // Случайный pivot для защиты от O(n²):
    int ri = lo + rand() % (hi - lo);
    swap(a[ri], a[hi - 1]);
    int pivot = a[hi - 1], i = lo;
    for (int j = lo; j < hi - 1; j++)
        if (a[j] <= pivot) swap(a[i++], a[j]);
    swap(a[i], a[hi - 1]);
    return i;
}

void quick_sort(vector<int>& a, int lo, int hi) {
    if (hi - lo <= 1) return;
    int p = partition(a, lo, hi);
    quick_sort(a, lo, p);
    quick_sort(a, p + 1, hi);
}
```

In-place. Cache-friendly. Рандомизированный pivot → O(n log n) с высокой вероятностью.

### Counting Sort — O(n + k)

```cpp
void counting_sort(vector<int>& a, int max_val) {
    vector<int> cnt(max_val + 1, 0);
    for (int x : a) cnt[x]++;
    int i = 0;
    for (int v = 0; v <= max_val; v++)
        while (cnt[v]--) a[i++] = v;
}
```

Используется когда диапазон значений небольшой. Стабилен (в модифицированной версии).

### std::sort и компараторы

```cpp
#include <algorithm>
vector<int> v = {3, 1, 4, 1, 5};
sort(v.begin(), v.end());                    // возрастание
sort(v.begin(), v.end(), greater<int>());    // убывание
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });

// Сортировка структур:
struct Person { string name; int age; };
sort(people.begin(), people.end(), [](const Person& a, const Person& b) {
    return a.age != b.age ? a.age < b.age : a.name < b.name;
});

// Стабильная сортировка:
stable_sort(v.begin(), v.end(), cmp);
```

| Алгоритм | Время (worst) | Время (avg) | Память | Стабильный |
|---|---|---|---|---|
| Merge sort | O(n log n) | O(n log n) | O(n) | Да |
| Quick sort | O(n²) | O(n log n) | O(log n) | Нет |
| Heap sort | O(n log n) | O(n log n) | O(1) | Нет |
| Counting sort | O(n+k) | O(n+k) | O(k) | Да |
| std::sort | O(n log n) | O(n log n) | O(log n) | Нет |
