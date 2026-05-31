### Two Pointers

**Когда**: отсортированный массив или строка, нужна пара/тройка элементов с условием.

```cpp
// Two Sum в отсортированном — O(n), O(1):
pair<int,int> two_sum_sorted(vector<int>& a, int target) {
    int lo = 0, hi = (int)a.size() - 1;
    while (lo < hi) {
        int s = a[lo] + a[hi];
        if (s == target) return {lo, hi};
        else if (s < target) lo++;
        else hi--;
    }
    return {-1, -1};
}

// Разворот строки — O(n), O(1):
void reverse(string& s) {
    int lo = 0, hi = s.size() - 1;
    while (lo < hi) swap(s[lo++], s[hi--]);
}
```

**Three Sum**: фиксировать один элемент, two pointers для остальных. O(n²).

### Sliding Window

**Фиксированный размер** (окно из k элементов):
```cpp
// Максимальная сумма подмассива длины k:
int window_sum = accumulate(a.begin(), a.begin()+k, 0);
int max_sum = window_sum;
for (int i = k; i < n; i++) {
    window_sum += a[i] - a[i-k];
    max_sum = max(max_sum, window_sum);
}
```

**Переменный размер** (условие на окно):
```cpp
// Длиннейшая подстрока без повторов — O(n):
int longest_unique(const string& s) {
    unordered_set<char> window;
    int lo = 0, ans = 0;
    for (int hi = 0; hi < s.size(); hi++) {
        while (window.count(s[hi])) window.erase(s[lo++]);
        window.insert(s[hi]);
        ans = max(ans, hi - lo + 1);
    }
    return ans;
}
```

### Префиксные суммы

```cpp
vector<int> build_prefix(const vector<int>& a) {
    vector<int> p(a.size() + 1, 0);
    for (int i = 0; i < a.size(); i++) p[i+1] = p[i] + a[i];
    return p;
}
// Сумма a[l..r] включительно:
int range_sum(const vector<int>& p, int l, int r) { return p[r+1] - p[l]; }
// O(1) запрос после O(n) предобработки
```

### Разностный массив

```cpp
// Быстро добавить val к a[l..r]:
// diff[l] += val; diff[r+1] -= val;
// Восстановить: partial_sum(diff, diff+n+1, a);
```

### Частотные таблицы

```cpp
// Частота символов в строке:
array<int, 26> freq{};
for (char c : s) freq[c - 'a']++;

// Или через unordered_map для произвольных ключей:
unordered_map<int, int> cnt;
for (int x : a) cnt[x]++;
```
