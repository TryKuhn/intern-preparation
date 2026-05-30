0–15 мин. Как обосновывать выбор структуры данных.
Обсуди системное мышление: сначала определи операции (что чаще — чтение или запись?), затем ограничения (нужен порядок? нужна уникальность?), затем выбери структуру. Логирование: только append → vector/deque. Редкий поиск → допустимо O(n) или сортировка по требованию.

15–50 мин. Разбор 5 сценариев.
Для каждого — обсудить и обосновать:
1. Словарь (слово → определение): `unordered_map` (O(1)) или `map` (алфавитный порядок)?
2. Очередь задач с приоритетом: `priority_queue`.
3. История браузера (последние N посещений): `deque` с ограниченным размером.
4. Уникальные IP-адреса за день: `unordered_set`.
5. Авто-дополнение (prefix matching): trie (упомянуть, не реализовывать).

50–65 мин. Перегрузка операторов.
```cpp
struct Point {
    int x, y;
    Point operator+(const Point& o) const { return {x+o.x, y+o.y}; }
    bool operator==(const Point& o) const { return x==o.x && y==o.y; }
    bool operator<(const Point& o) const {
        return x != o.x ? x < o.x : y < o.y;
    }
    // Для range-for: iterator pair
    Point& operator=(const Point&) = default;  // возвращать T& из operator=
};
// operator[] vs .at(): at() кидает исключение, [] — UB при OOB
// spaceship <=> (C++20):
auto operator<=>(const Point& o) const = default;  // генерирует все 6 операторов
```

65–80 мин. ADL — поиск Кёнига.
```cpp
namespace geometry {
    struct Point { int x, y; };
    void print(const Point& p) { std::cout << p.x << "," << p.y; }
}
geometry::Point p{1, 2};
print(p);  // OK! ADL находит geometry::print через тип аргумента
```
ADL: при вызове функции без квалификатора компилятор ищет функции в пространствах имён аргументов. Важно для операторов (`operator<<`, `swap`).

80–90 мин. Итог фазы 1. Выдача ДЗ.
