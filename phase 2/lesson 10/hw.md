### A. 1D DP (около 50 мин)
1. **LC 70** Climbing Stairs.
2. **LC 198** House Robber: dp[i] = max(dp[i-2]+nums[i], dp[i-1]).
3. **LC 300** Longest Increasing Subsequence: O(n²), затем O(n log n).
4. 0/1 Knapsack: реализуй и восстанови набор предметов.

### B. 2D DP (около 50 мин)
1. **LC 72** Edit Distance.
2. **LC 322** Coin Change.
3. **LC 1143** Longest Common Subsequence.
4. **LC 64** Minimum Path Sum: в сетке.

### C. Восстановление (около 20 мин)
Для LC 300 LIS — восстанови саму подпоследовательность (не только длину).

### Самопроверка (около 15 мин, письменно в `selfcheck.md`)
1. Что такое мемоизация? Чем top-down DP отличается от bottom-up?
2. Почему в 0/1 knapsack обратный порядок обхода?
3. Рекуррентное соотношение Edit Distance?
4. Принцип оптимальности — что это значит?

### Критерий "сделано"
LC 70, 198, 300, 72, 322, 1143 приняты. 0/1 knapsack восстанавливает набор предметов.
