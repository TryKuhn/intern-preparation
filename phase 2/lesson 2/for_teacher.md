0–5 мин. Two sum в отсортированном.
Ответ: два указателя с концов. Если сумма > target — двигаем правый влево, если < target — двигаем левый вправо. O(n), O(1) памяти.

5–30 мин. Two pointers — паттерн.
Когда использовать: отсортированный массив, нужно найти пару/тройку с условием.
Базовый шаблон:
```cpp
int lo = 0, hi = n - 1;
while (lo < hi) {
    if (check(a[lo], a[hi])) { /* found */ lo++; hi--; }
    else if (need_bigger) lo++;
    else hi--;
}
```
Задачи: two sum II (LC 167), container with most water (LC 11), three sum.
Покажи на бумаге движение указателей.

30–55 мин. Sliding window.
Фиксированный размер: сумма подмассива длины k.
Переменный размер: минимальная подстрока с условием.
```cpp
// Переменное окно: минимальный подмассив с суммой >= target
int lo = 0, sum = 0, ans = INT_MAX;
for (int hi = 0; hi < n; hi++) {
    sum += a[hi];
    while (sum >= target) {
        ans = min(ans, hi - lo + 1);
        sum -= a[lo++];
    }
}
```
Задачи: LC 209 minimum size subarray, LC 3 longest substring without repeating.

55–75 мин. Префиксные суммы и разностный массив.
```cpp
// Префиксные суммы: sum(l, r) = prefix[r+1] - prefix[l]
vector<int> prefix(n+1, 0);
for (int i = 0; i < n; i++) prefix[i+1] = prefix[i] + a[i];
auto range_sum = [&](int l, int r) { return prefix[r+1] - prefix[l]; };

// Разностный массив: быстрое прибавление +val к отрезку [l, r]
diff[l] += val;
diff[r+1] -= val;
// После: partial_sum(diff, diff+n, a)
```

75–90 мин. Частотные таблицы. Выдача ДЗ.
