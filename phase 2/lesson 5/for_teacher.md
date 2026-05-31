0–10 мин. Сколько вариантов.
Подмножеств: 2^n (каждый элемент либо включён, либо нет). Перестановок: n!. При n=20: 2^20 ≈ 10^6 — приемлемо. n! при n=12: 479 001 600 ≈ 5×10^8 — на грани. Поэтому бэктрекинг с отсечениями — ключевое.

10–40 мин. Генерация подмножеств — два подхода.
```cpp
// Рекурсивный DFS:
void subsets(vector<int>& nums, int i, vector<int>& curr, vector<vector<int>>& res) {
    res.push_back(curr);
    for (int j = i; j < nums.size(); j++) {
        curr.push_back(nums[j]);
        subsets(nums, j+1, curr, res);
        curr.pop_back();  // backtrack
    }
}

// Через битовые маски:
for (int mask = 0; mask < (1 << n); mask++) {
    vector<int> subset;
    for (int i = 0; i < n; i++)
        if (mask & (1 << i)) subset.push_back(nums[i]);
}
```
Объясни «состояние» в бэктрекинге: дерево решений, каждый узел — частичное решение.

40–65 мин. N-Queens бэктрекинг.
```cpp
void solve(int row, vector<int>& cols, vector<int>& diag1, vector<int>& diag2,
           vector<string>& board, vector<vector<string>>& res) {
    if (row == n) { res.push_back(board); return; }
    for (int col = 0; col < n; col++) {
        if (cols[col] || diag1[row-col+n] || diag2[row+col]) continue;
        // Поставить ферзя
        cols[col] = diag1[row-col+n] = diag2[row+col] = 1;
        board[row][col] = 'Q';
        solve(row+1, ...);
        // Убрать
        cols[col] = diag1[row-col+n] = diag2[row+col] = 0;
        board[row][col] = '.';
    }
}
```
Отсечения: не рассматриваем позиции где уже есть конфликт.

65–82 мин. Перестановки. Общий шаблон бэктрекинга. Выдача ДЗ.
