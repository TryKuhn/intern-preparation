0–10 мин. Вырождение BST.
Обсуди: отсортированный ввод создаёт «цепочку» — дерево высотой n, поиск O(n). Именно поэтому `std::map` использует Red-Black Tree — самобалансирующееся дерево, гарантирующее O(log n).

10–55 мин. Реализуй BST.
```cpp
template<typename K, typename V>
class BST {
    struct Node {
        K key; V value;
        Node *left = nullptr, *right = nullptr;
        Node(K k, V v) : key(std::move(k)), value(std::move(v)) {}
    };
    Node* root_ = nullptr;

    Node* insert(Node* node, K key, V val) {
        if (!node) return new Node(std::move(key), std::move(val));
        if (key < node->key) node->left = insert(node->left, key, val);
        else if (key > node->key) node->right = insert(node->right, key, val);
        else node->value = std::move(val);
        return node;
    }
    // find, erase, inorder traversal...
};
```
Практика: вставить 10 элементов, нарисовать дерево, выполнить inorder обход (должен дать отсортированный список).

55–70 мин. Удаление из BST — три случая.
1. Листовой узел: просто удалить.
2. Один потомок: заменить узел его потомком.
3. Два потомка: найти inorder successor (минимум правого поддерева), заменить, удалить successor.

70–82 мин. Балансировка — концептуально.
AVL: для каждого узла |высота левого поддерева - высота правого| ≤ 1. При вставке/удалении — ротации.
Red-Black Tree: 5 свойств цвета, амортизированные ротации. Использует `std::map` и `std::set`.
B-дерево: для дисковых структур, много ключей в узле. Использует PostgreSQL.
Не реализовываем — только понимаем зачем.

82–90 мин. Выдача ДЗ.
