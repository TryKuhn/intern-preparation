### Паттерн DP

**4 шага:**
1. Определить состояние (что значит dp[i] или dp[i][j])
2. Написать рекуррентное соотношение
3. Определить базовые случаи
4. Определить порядок вычислений (обычно слева направо)

### 1D DP — классические задачи

```cpp
// Climbing Stairs (LC 70): dp[i] = dp[i-1] + dp[i-2]
int climb(int n) {
    if (n <= 2) return n;
    int a = 1, b = 2;
    for (int i = 3; i <= n; i++) { int c = a+b; a = b; b = c; }
    return b;
}

// 0/1 Knapsack — O(n×W):
int knapsack(vector<pair<int,int>>& items, int W) {
    vector<int> dp(W+1, 0);
    for (auto [w, v] : items)
        for (int j = W; j >= w; j--)  // обратный порядок для 0/1
            dp[j] = max(dp[j], dp[j-w]+v);
    return dp[W];
}

// Longest Increasing Subsequence (LC 300) — O(n²):
int lis(vector<int>& a) {
    int n = a.size();
    vector<int> dp(n, 1);
    for (int i = 1; i < n; i++)
        for (int j = 0; j < i; j++)
            if (a[j] < a[i]) dp[i] = max(dp[i], dp[j]+1);
    return *max_element(dp.begin(), dp.end());
}
// O(n log n) через бинпоиск тоже существует
```

### 2D DP — строки

```cpp
// Edit Distance (LC 72): dp[i][j] = min ops to convert s1[0..i) to s2[0..j)
int edit_distance(string& s1, string& s2) {
    int m = s1.size(), n = s2.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1));
    for (int i=0; i<=m; i++) dp[i][0]=i;
    for (int j=0; j<=n; j++) dp[0][j]=j;
    for (int i=1; i<=m; i++) for (int j=1; j<=n; j++)
        if (s1[i-1]==s2[j-1]) dp[i][j]=dp[i-1][j-1];
        else dp[i][j]=1+min({dp[i-1][j], dp[i][j-1], dp[i-1][j-1]});
    return dp[m][n];
}

// Coin Change (LC 322): dp[i] = min монет для суммы i
int coin_change(vector<int>& coins, int amount) {
    vector<int> dp(amount+1, INT_MAX);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++)
        for (int c : coins)
            if (c <= i && dp[i-c] != INT_MAX)
                dp[i] = min(dp[i], dp[i-c]+1);
    return dp[amount] == INT_MAX ? -1 : dp[amount];
}
```

### Восстановление ответа

Хранить `parent` или `choice` массив — при обходе назад от ответа восстанавливаем путь.
