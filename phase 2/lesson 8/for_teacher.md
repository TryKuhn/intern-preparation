0–10 мин. In-order и BST.
In-order обход: лево → корень → право. В BST: все левые < корень < все правые. Поэтому in-order выдаёт элементы по возрастанию. Для проверки BST: in-order обход + проверить что последовательность возрастает.

10–45 мин. Рекурсивные обходы.
```cpp
void preorder(TreeNode* n, vector<int>& res) {
    if (!n) return;
    res.push_back(n->val);  // root
    preorder(n->left, res);
    preorder(n->right, res);
}
// inorder и postorder — аналогично с разным порядком строк
```
Level-order через очередь:
```cpp
vector<vector<int>> level_order(TreeNode* root) {
    if (!root) return {};
    queue<TreeNode*> q; q.push(root);
    vector<vector<int>> res;
    while (!q.empty()) {
        int sz = q.size();
        res.push_back({});
        for (int i = 0; i < sz; i++) {
            auto n = q.front(); q.pop();
            res.back().push_back(n->val);
            if (n->left) q.push(n->left);
            if (n->right) q.push(n->right);
        }
    }
    return res;
}
```

45–65 мин. Итеративные обходы (стек) и задачи.
Inorder итеративно (LC 94), validate BST (LC 98).

65–82 мин. LCA интуиция (LC 235, 236). Выдача ДЗ.
