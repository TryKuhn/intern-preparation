### Обходы дерева

```cpp
// Pre-order: корень → лево → право
void preorder(TreeNode* n, vector<int>& r) {
    if (!n) return;
    r.push_back(n->val); preorder(n->left, r); preorder(n->right, r);
}

// In-order: лево → корень → право (для BST = сортировка)
void inorder(TreeNode* n, vector<int>& r) {
    if (!n) return;
    inorder(n->left, r); r.push_back(n->val); inorder(n->right, r);
}

// Post-order: лево → право → корень
void postorder(TreeNode* n, vector<int>& r) {
    if (!n) return;
    postorder(n->left, r); postorder(n->right, r); r.push_back(n->val);
}

// Level-order через очередь:
vector<vector<int>> level_order(TreeNode* root) {
    if (!root) return {};
    queue<TreeNode*> q{};
    q.push(root);
    vector<vector<int>> res;
    while (!q.empty()) {
        res.push_back({});
        for (int sz = q.size(); sz--; ) {
            auto n = q.front(); q.pop();
            res.back().push_back(n->val);
            if (n->left) q.push(n->left);
            if (n->right) q.push(n->right);
        }
    }
    return res;
}
```

### Validate BST (LC 98)

```cpp
bool is_valid_bst(TreeNode* root, long lo = LLONG_MIN, long hi = LLONG_MAX) {
    if (!root) return true;
    if (root->val <= lo || root->val >= hi) return false;
    return is_valid_bst(root->left, lo, root->val) &&
           is_valid_bst(root->right, root->val, hi);
}
```

### LCA в BST (LC 235)

```cpp
TreeNode* lca_bst(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root) return nullptr;
    if (p->val < root->val && q->val < root->val) return lca_bst(root->left, p, q);
    if (p->val > root->val && q->val > root->val) return lca_bst(root->right, p, q);
    return root;
}
```

### LCA в обычном дереве (LC 236)

```cpp
TreeNode* lca(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root || root == p || root == q) return root;
    auto left = lca(root->left, p, q);
    auto right = lca(root->right, p, q);
    return left && right ? root : (left ? left : right);
}
```

### Сериализация/десериализация (LC 297)

Pre-order обход с null-маркерами. Строка: `"1,2,null,null,3,null,null"`.
