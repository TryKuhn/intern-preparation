0–10 мин. Середина за один проход.
Два указателя: медленный двигается на 1, быстрый на 2. Когда быстрый достигнет конца — медленный на середине.

10–45 мин. Разворот списка.
```cpp
ListNode* reverse(ListNode* head) {
    ListNode* prev = nullptr, *curr = head;
    while (curr) {
        ListNode* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
```
Нарисуй пошагово — где именно переставляются указатели. Частая ошибка: потерять next до переназначения.
Рекурсивная версия: `reverse(rest)` + `head->next->next = head`.

45–65 мин. Цикл Флойда.
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
```
Объясни математически: если есть цикл, быстрый догоняет медленного. Почему работает — через арифметику разности скоростей.

65–82 мин. Слияние отсортированных списков. Задачи. Выдача ДЗ.
