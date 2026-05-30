### BST — свойства и операции

**Инвариант**: для каждого узла N: все ключи в левом поддереве < N.key, все в правом > N.key.

```cpp
template<typename K, typename V>
class BST {
    struct Node {
        K key; V value;
        Node *left = nullptr, *right = nullptr;
    };
    Node* root_ = nullptr;

    Node* insert(Node* n, K k, V v) {
        if (!n) return new Node{std::move(k), std::move(v)};
        if (k < n->key) n->left = insert(n->left, k, v);
        else if (k > n->key) n->right = insert(n->right, k, v);
        else n->value = std::move(v);  // обновить существующий
        return n;
    }

    Node* find(Node* n, const K& k) const {
        if (!n || n->key == k) return n;
        return k < n->key ? find(n->left, k) : find(n->right, k);
    }

    Node* min_node(Node* n) {
        while (n->left) n = n->left;
        return n;
    }

    Node* erase(Node* n, const K& k) {
        if (!n) return nullptr;
        if (k < n->key) n->left = erase(n->left, k);
        else if (k > n->key) n->right = erase(n->right, k);
        else {
            if (!n->left) { auto r = n->right; delete n; return r; }
            if (!n->right) { auto l = n->left; delete n; return l; }
            // Два потомка: заменить inorder successor
            Node* succ = min_node(n->right);
            n->key = succ->key; n->value = succ->value;
            n->right = erase(n->right, succ->key);
        }
        return n;
    }

    void inorder(Node* n, std::vector<K>& result) const {
        if (!n) return;
        inorder(n->left, result);
        result.push_back(n->key);
        inorder(n->right, result);
    }
public:
    void insert(K k, V v) { root_ = insert(root_, std::move(k), std::move(v)); }
    V* find(const K& k) { auto n = find(root_, k); return n ? &n->value : nullptr; }
    void erase(const K& k) { root_ = erase(root_, k); }
    std::vector<K> sorted_keys() { std::vector<K> r; inorder(root_, r); return r; }
};
```

### Сложности BST

| Операция | Средний случай | Худший случай (вырождение) |
|---|---|---|
| insert | O(log n) | O(n) |
| find | O(log n) | O(n) |
| erase | O(log n) | O(n) |

Вырождение: ввод в отсортированном порядке → односвязная цепочка.

### Самобалансирующиеся деревья

**AVL**: balance factor (разница высот) ≤ 1. Ротации при нарушении. Строже сбалансирован, чем RBT → быстрее чтение.

**Red-Black Tree**: 5 свойств (корень чёрный, красный узел имеет чёрных детей, ...). Используют `std::map`, `std::set`, `std::multimap`. Меньше ротаций при вставке → быстрее запись.

Оба гарантируют O(log n) для всех операций.
