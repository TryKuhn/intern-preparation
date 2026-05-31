0–10 мин. Зачем несколько сортировок.
Ответ: merge sort гарантирует O(n log n), но требует O(n) доп. памяти. Quick sort — in-place, cache-friendly, быстрее на практике (меньше константа), но O(n²) при плохом pivot. Introsort: быстрый в среднем как quicksort, но переключается на heapsort при плохой рекурсии → гарантия O(n log n). Insertion sort — для маленьких массивов (<16 элементов).

10–40 мин. Merge Sort реализация.
```cpp
void merge(vector<int>& a, int lo, int mid, int hi) {
    vector<int> tmp(hi - lo);
    int i = lo, j = mid, k = 0;
    while (i < mid && j < hi)
        tmp[k++] = a[i] <= a[j] ? a[i++] : a[j++];
    while (i < mid) tmp[k++] = a[i++];
    while (j < hi) tmp[k++] = a[j++];
    copy(tmp.begin(), tmp.end(), a.begin() + lo);
}

void merge_sort(vector<int>& a, int lo, int hi) {
    if (hi - lo <= 1) return;
    int mid = (lo + hi) / 2;
    merge_sort(a, lo, mid);
    merge_sort(a, mid, hi);
    merge(a, lo, mid, hi);
}
```
Объясни рекуррентное соотношение: T(n) = 2T(n/2) + O(n) = O(n log n).

40–65 мин. Quick Sort реализация.
```cpp
int partition(vector<int>& a, int lo, int hi) {
    int pivot = a[hi - 1];  // или random pivot
    int i = lo;
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
Важно: random pivot предотвращает O(n²) на отсортированных данных.

65–80 мин. Counting sort и стабильность.
Counting sort: O(n + k) где k — диапазон значений. Для небольших диапазонов (байты, буквы).
Стабильная сортировка: сохраняет порядок равных элементов. Merge sort — стабильная. Quick sort — нет.
`std::stable_sort` — гарантированно стабильная (но медленнее `std::sort`).

80–90 мин. Компараторы для std::sort. Выдача ДЗ.
