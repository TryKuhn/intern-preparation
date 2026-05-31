### Жадные алгоритмы

**Когда работает**: когда задача обладает свойством «жадного выбора» — локально оптимальный выбор ведёт к глобально оптимальному. Доказывается через exchange argument.

```cpp
// Activity Selection (максимальное число непересекающихся отрезков):
int activity_selection(vector<pair<int,int>>& intervals) {
    sort(intervals.begin(), intervals.end(),
         [](auto& a, auto& b) { return a.second < b.second; });
    int count = 0, last_end = INT_MIN;
    for (auto [s, e] : intervals) {
        if (s >= last_end) { count++; last_end = e; }
    }
    return count;
}

// LC 55 Jump Game:
bool can_jump(vector<int>& nums) {
    int reach = 0;
    for (int i = 0; i < nums.size(); i++) {
        if (i > reach) return false;
        reach = max(reach, i + nums[i]);
    }
    return true;
}
```

### Математика

```cpp
// GCD — O(log min(a,b)):
int gcd(int a, int b) { return b ? gcd(b, a%b) : a; }
int lcm(int a, int b) { return a / gcd(a,b) * b; }  // делить до умножения!

// Решето Эратосфена — O(n log log n):
vector<bool> sieve(int n) {
    vector<bool> is_prime(n+1, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; (long long)i*i <= n; i++)
        if (is_prime[i])
            for (int j = i*i; j <= n; j += i)
                is_prime[j] = false;
    return is_prime;
}

// Быстрое возведение в степень по модулю — O(log exp):
long long pow_mod(long long b, long long e, long long m) {
    long long r = 1;
    for (b %= m; e > 0; e >>= 1) {
        if (e & 1) r = r * b % m;
        b = b * b % m;
    }
    return r;
}
```

### Битовые трюки

```cpp
x & (x - 1)          // обнулить младший установленный бит
x & (-x)             // выделить младший установленный бит
__builtin_popcount(x) // число установленных битов
x ^ x == 0           // XOR числа с собой = 0
a ^ b ^ a == b       // XOR с константой дважды отменяет себя
(x >> k) & 1         // k-й бит (с 0)
x | (1 << k)         // установить k-й бит
x & ~(1 << k)        // сбросить k-й бит
x ^ (1 << k)         // перевернуть k-й бит

// Является ли x степенью двойки?
bool is_power_of_2(int x) { return x > 0 && (x & (x-1)) == 0; }
```
