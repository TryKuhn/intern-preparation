### Выбор структуры данных

| Потребность | Структура | Обоснование |
|---|---|---|
| Быстрый поиск/вставка, порядок неважен | `unordered_map/set` | O(1) avg |
| Поиск/вставка + упорядоченность | `map/set` | O(log n) |
| Порядок + случайный доступ | `vector` | O(1) access |
| Частые вставки в начало | `deque`, `list` | O(1) push_front |
| Min/max всегда нужен | `priority_queue` | O(log n) push/pop, O(1) top |
| Вставка по итератору в середину | `list` | O(1) по итератору |
| Уникальные элементы | `unordered_set` / `set` | — |
| K наибольших/наименьших | `priority_queue` размера K | — |

**Шаблон выбора:**
1. Какие операции нужны (вставка, поиск, удаление, порядок, random access)?
2. Что чаще (read-heavy vs write-heavy)?
3. Нужен ли порядок/уникальность?
4. Ограничения по памяти?

### Перегрузка операторов

```cpp
struct Vector2D {
    double x, y;

    // Арифметика — возвращать по значению
    Vector2D operator+(const Vector2D& o) const { return {x+o.x, y+o.y}; }
    Vector2D operator*(double s) const { return {x*s, y*s}; }

    // operator= — возвращать T& (для цепочки a = b = c)
    Vector2D& operator=(const Vector2D&) = default;

    // Сравнение (C++20 spaceship)
    auto operator<=>(const Vector2D&) const = default;
    bool operator==(const Vector2D&) const = default;

    // operator[] без и с const
    double& operator[](size_t i) { return i==0 ? x : y; }
    const double& operator[](size_t i) const { return i==0 ? x : y; }
};

// Вне класса — operator<< для вывода:
std::ostream& operator<<(std::ostream& os, const Vector2D& v) {
    return os << "(" << v.x << ", " << v.y << ")";
}
```

**operator[] vs .at()**: `operator[]` — без проверки границ (UB при нарушении), `.at()` — бросает `std::out_of_range`. Используй `.at()` при неопределённом индексе, `[]` в hot path.

### ADL — Argument-Dependent Lookup

```cpp
namespace geometry {
    struct Point { int x, y; };

    // Без ADL пришлось бы писать: geometry::print(p)
    void print(const Point& p);
    void swap(Point& a, Point& b);
}

geometry::Point p{1, 2};
print(p);  // OK: компилятор ищет print в namespace geometry (из типа Point)
swap(p, q); // OK: аналогично
```

ADL нужен для:
- Операторов (`operator<<`, `operator+`)
- `swap` (чтобы использовалась специализированная версия, а не std::swap)
- Функций-хелперов в том же namespace что и тип
