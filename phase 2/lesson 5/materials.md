### Шаблон бэктрекинга

```cpp
void backtrack(State state, vector<Result>& results) {
    if (is_complete(state)) {
        results.push_back(state);
        return;
    }
    for (auto choice : get_choices(state)) {
        if (!is_valid(state, choice)) continue;  // отсечение
        apply(state, choice);                     // сделать выбор
        backtrack(state, results);               // рекурсия
        undo(state, choice);                      // откатить (backtrack)
    }
}
```

### Подмножества (LC 78) — O(2^n × n)

```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> curr;
    function<void(int)> bt = [&](int start) {
        res.push_back(curr);
        for (int i = start; i < nums.size(); i++) {
            curr.push_back(nums[i]);
            bt(i + 1);
            curr.pop_back();
        }
    };
    bt(0);
    return res;
}
```

### Перестановки (LC 46) — O(n! × n)

```cpp
vector<vector<int>> permutations(vector<int>& nums) {
    vector<vector<int>> res;
    sort(nums.begin(), nums.end());
    do {
        res.push_back(nums);
    } while (next_permutation(nums.begin(), nums.end()));
    return res;
}

// Или рекурсивно:
void perm(vector<int>& nums, int start, vector<vector<int>>& res) {
    if (start == nums.size()) { res.push_back(nums); return; }
    for (int i = start; i < nums.size(); i++) {
        swap(nums[start], nums[i]);
        perm(nums, start + 1, res);
        swap(nums[start], nums[i]);
    }
}
```

### N-Queens — отсечения

```cpp
int n;
vector<vector<string>> solve_n_queens(int n_) {
    n = n_; vector<vector<string>> res;
    vector<string> board(n, string(n, '.'));
    vector<bool> col(n), diag1(2*n), diag2(2*n);
    function<void(int)> bt = [&](int row) {
        if (row == n) { res.push_back(board); return; }
        for (int c = 0; c < n; c++) {
            if (col[c] || diag1[row-c+n] || diag2[row+c]) continue;
            col[c] = diag1[row-c+n] = diag2[row+c] = true;
            board[row][c] = 'Q';
            bt(row + 1);
            col[c] = diag1[row-c+n] = diag2[row+c] = false;
            board[row][c] = '.';
        }
    };
    bt(0); return res;
}
```
