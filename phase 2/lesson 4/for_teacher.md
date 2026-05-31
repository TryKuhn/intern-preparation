0–10 мин. Бинпоиск по ответу.
Обсуди: ищем не в массиве, а по «ответу». Если при скорости s можно прочитать книги — значит при s+1 тоже можно. Монотонная функция! Бинарный поиск по s находит минимум за O(log(max_speed) × n).

10–40 мин. Три варианта бинарного поиска.
Покажи на конкретных примерах на бумаге:
```cpp
// 1. Найти exact:
int lo = 0, hi = n - 1;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;
    if (a[mid] == target) return mid;
    else if (a[mid] < target) lo = mid + 1;
    else hi = mid - 1;
}
return -1;

// 2. lower_bound — первый >= target:
int lo = 0, hi = n;  // [lo, hi)
while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (a[mid] < target) lo = mid + 1;
    else hi = mid;
}
return lo;  // lo == hi == первая позиция >= target

// 3. upper_bound — первый > target:
while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (a[mid] <= target) lo = mid + 1;
    else hi = mid;
}
return lo;
```
Покажи баг `mid = (lo+hi)/2` — переполнение при больших lo, hi. Правильно: `lo + (hi-lo)/2`.

40–65 мин. Бинпоиск по ответу — шаблон.
```cpp
// Шаблон: найти минимальный x такой что condition(x) == true
// При этом condition(x) монотонна: false...false,true...true
auto condition = [&](long long mid) { /* проверка */ return bool; };
long long lo = min_answer, hi = max_answer;
while (lo < hi) {
    long long mid = lo + (hi - lo) / 2;
    if (condition(mid)) hi = mid;
    else lo = mid + 1;
}
return lo;
```
Задача: «Koko eating bananas» (LC 875) — разобрать вместе.

65–82 мин. Частые баги.
- off-by-one: `lo <= hi` vs `lo < hi`
- infinite loop: когда `hi = mid` а не `mid - 1`
- переполнение mid

82–90 мин. Выдача ДЗ.
