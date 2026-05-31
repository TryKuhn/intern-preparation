0–10 мин. Жадность vs DP.
Жадный алгоритм корректен если локальный оптимум → глобальный. Доказательство обычно через «exchange argument»: покажи что любое нежадное решение можно превратить в жадное без потери качества. Для отрезков: берём тот что кончается раньше — освобождаем место для большего числа последующих.

10–35 мин. Жадные задачи.
- Activity selection (выбор максимального числа активностей): сортировать по finish time.
- Jump game (LC 55): можно ли дойти до конца? Поддерживать максимально достижимую позицию.
- Fractional knapsack: в отличие от 0/1 — берём дроби предметов. Сортируем по value/weight.

35–55 мин. GCD, LCM, решето.
```cpp
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }  // O(log min(a,b))
int lcm(int a, int b) { return a / gcd(a,b) * b; }  // порядок делений важен!

// Решето Эратосфена:
vector<bool> sieve(int n) {
    vector<bool> prime(n+1, true);
    prime[0] = prime[1] = false;
    for (int i=2; i*i<=n; i++)
        if (prime[i]) for (int j=i*i; j<=n; j+=i) prime[j] = false;
    return prime;
}

// Быстрое возведение в степень по модулю:
long long pow_mod(long long base, long long exp, long long mod) {
    long long res = 1;
    while (exp > 0) {
        if (exp & 1) res = res * base % mod;
        base = base * base % mod;
        exp >>= 1;
    }
    return res;
}
```

55–75 мин. Битовые операции.
`x & (x-1)` убирает младший бит. `x & -x` — выделяет младший бит. `x ^ x = 0`, `x ^ 0 = x`. `popcount`.

75–90 мин. Выдача ДЗ. Итог фазы 2.
