### Монотонный стек

Монотонный стек поддерживает инвариант: элементы в стеке всегда упорядочены (убывают или возрастают). При добавлении нового элемента выталкиваем из стека всё, что нарушает порядок.

```cpp
// Следующий больший элемент справа — O(n):
vector<int> next_greater(const vector<int>& a) {
    int n = a.size();
    vector<int> res(n, -1);
    stack<int> st;  // стек индексов (убывающий стек значений)
    for (int i = 0; i < n; i++) {
        while (!st.empty() && a[st.top()] < a[i]) {
            res[st.top()] = a[i];
            st.pop();
        }
        st.push(i);
    }
    return res;
}

// Предыдущий меньший элемент — обходим слева, убывающий стек:
vector<int> prev_smaller(const vector<int>& a) {
    vector<int> res(a.size(), -1);
    stack<int> st;
    for (int i = 0; i < a.size(); i++) {
        while (!st.empty() && a[st.top()] >= a[i]) st.pop();
        res[i] = st.empty() ? -1 : a[st.top()];
        st.push(i);
    }
    return res;
}
```

### Скобочные последовательности (LC 20)

```cpp
bool is_valid(const string& s) {
    stack<char> st;
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') st.push(c);
        else {
            if (st.empty()) return false;
            char top = st.top(); st.pop();
            if ((c==')' && top!='(') || (c==']' && top!='[') || (c=='}' && top!='{'))
                return false;
        }
    }
    return st.empty();
}
```

### Наибольший прямоугольник в гистограмме (LC 84)

```cpp
int largest_rectangle(vector<int>& heights) {
    heights.push_back(0);  // sentinel
    stack<int> st; int res = 0;
    for (int i = 0; i < heights.size(); i++) {
        while (!st.empty() && heights[st.top()] > heights[i]) {
            int h = heights[st.top()]; st.pop();
            int w = st.empty() ? i : i - st.top() - 1;
            res = max(res, h * w);
        }
        st.push(i);
    }
    return res;
}
```
