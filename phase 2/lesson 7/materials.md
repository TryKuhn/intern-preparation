### Разворот списка (LC 206)

```cpp
// Итеративно — O(n), O(1):
ListNode* reverse(ListNode* head) {
    ListNode* prev = nullptr;
    while (head) {
        ListNode* next = head->next;
        head->next = prev;
        prev = head;
        head = next;
    }
    return prev;
}

// Рекурсивно — O(n), O(n) стек:
ListNode* reverse_rec(ListNode* head) {
    if (!head || !head->next) return head;
    ListNode* new_head = reverse_rec(head->next);
    head->next->next = head;
    head->next = nullptr;
    return new_head;
}
```

### Обнаружение цикла — алгоритм Флойда (LC 141)

```cpp
bool has_cycle(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }
    return false;
}

// Найти начало цикла (LC 142):
ListNode* detect_cycle(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next; fast = fast->next->next;
        if (slow == fast) {
            slow = head;
            while (slow != fast) { slow = slow->next; fast = fast->next; }
            return slow;
        }
    }
    return nullptr;
}
```

### Середина списка (LC 876)

```cpp
ListNode* middle(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}
```

### Слияние отсортированных (LC 21)

```cpp
ListNode* merge(ListNode* l1, ListNode* l2) {
    ListNode dummy; ListNode* tail = &dummy;
    while (l1 && l2) {
        if (l1->val <= l2->val) { tail->next = l1; l1 = l1->next; }
        else { tail->next = l2; l2 = l2->next; }
        tail = tail->next;
    }
    tail->next = l1 ? l1 : l2;
    return dummy.next;
}
```
