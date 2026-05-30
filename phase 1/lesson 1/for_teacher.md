0–5 мин. Зачем это всё.
Обсуди итератор-инвалидацию: при реаллокации вектора все итераторы, указатели, ссылки становятся невалидными. Это реальный баг в production-коде. Если сделал `reserve(достаточно)` — итераторы в порядке.

5–35 мин. Тур по контейнерам.
Проходи по каждому типу — показывай код, объясняй сложности:

Sequence: `vector` (O(1) amort push_back, O(1) access), `array` (фиксированный размер, стек), `deque` (O(1) push_front/back), `list`/`forward_list` (O(1) вставка по итератору, нет random access).

Associative (упорядоченные, Red-Black Tree): `map/set` — O(log n) все операции. `multimap/multiset`.

Unordered (hash table): `unordered_map/set` — O(1) amort. Но: худший случай O(n), порядок не гарантирован.

Адаптеры: `stack`, `queue`, `priority_queue` (по умолчанию max-heap).

Сложности — нарисуй таблицу:
```
         | доступ | вставка начало | вставка конец | поиск
vector   | O(1)   | O(n)          | O(1) amort    | O(n)
list     | O(n)   | O(1)          | O(1)          | O(n)
map      | O(log n)| O(log n)     | O(log n)      | O(log n)
unord_map| N/A    | O(1) amort    | O(1) amort    | O(1) amort
```

35–55 мин. Итераторы и алгоритмы.
```cpp
std::vector<int> v = {3,1,4,1,5,9};
std::sort(v.begin(), v.end());
auto it = std::lower_bound(v.begin(), v.end(), 4);  // указывает на 4
auto it2 = std::upper_bound(v.begin(), v.end(), 4); // указывает на 5
std::unique(v.begin(), v.end());  // убирает соседние дубли
```
Пяти категорий итераторов касайся кратко: random access, bidirectional, forward, input, output.

55–70 мин. Лямбды.
```cpp
std::sort(v.begin(), v.end(), [](int a, int b) { return a > b; });  // сортировка по убыванию
int threshold = 5;
auto it = std::find_if(v.begin(), v.end(), [threshold](int x) { return x > threshold; });
// init-capture (C++14):
auto multiplier = [factor = 2](int x) { return x * factor; };
```
Захват: `[=]` по значению, `[&]` по ссылке, `[&threshold]` конкретная переменная. Висячий `this`: лямбда захватившая `this` станет висячей если объект удалён. Захватывай `[self = shared_from_this()]`.

70–82 мин. string_view, optional, variant.
`string_view`: ненулевая ссылка на чужую строку — **не владеет**! Висит если строка удалена.
```cpp
std::string_view sv = std::string("hello");  // UB! temporary уничтожается
```
`optional<T>`: может содержать значение или быть пустым. Альтернатива `T*` для optional-возвращаемого.
`variant<A,B,C>`: тип-сумма, хранит одно из нескольких типов. `std::visit` для обработки.

std::regex: упомяни API, напомни синтаксис из Bash. Медленный — не для горячих путей.

82–90 мин. Выдача ДЗ.
