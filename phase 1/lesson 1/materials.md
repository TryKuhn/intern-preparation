### Таблица сложностей STL контейнеров

| Контейнер | Доступ | Вставка начало | Вставка конец | Вставка середины | Поиск |
|---|---|---|---|---|---|
| `vector` | O(1) | O(n) | O(1) amort | O(n) | O(n) |
| `deque` | O(1) | O(1) | O(1) | O(n) | O(n) |
| `list` | O(n) | O(1) | O(1) | O(1)* | O(n) |
| `map`/`set` | O(log n) | — | — | O(log n) | O(log n) |
| `unordered_map`/`set` | O(1) avg | — | — | O(1) avg | O(1) avg |
| `priority_queue` | O(1) top | — | O(log n) push | — | — |

*O(1) если есть итератор, O(n) для поиска позиции

### Когда что использовать

```cpp
// vector: почти всегда — кэш-дружелюбный, быстрый random access
std::vector<int> v;

// list: когда часто вставляешь/удаляешь в середину по итератору
std::list<Task> tasks;

// map/set: отсортированный порядок, range queries
std::map<std::string, int> freq;

// unordered_map/set: максимальная скорость O(1), порядок неважен
std::unordered_map<std::string, int> fast_freq;

// priority_queue: всегда нужен min/max
std::priority_queue<int, std::vector<int>, std::greater<int>> min_heap;
```

### Итераторы и алгоритмы

```cpp
#include <algorithm>

std::vector<int> v = {5, 3, 1, 4, 2};
std::sort(v.begin(), v.end());           // {1,2,3,4,5}
auto it = std::lower_bound(v.begin(), v.end(), 3);  // указывает на 3 (первый >=3)
auto it2 = std::upper_bound(v.begin(), v.end(), 3); // указывает на 4 (первый >3)

std::vector<int> w = {1,1,2,2,3};
auto end = std::unique(w.begin(), w.end());  // {1,2,3,...}, end указывает на мусор
w.erase(end, w.end());                       // реально удалить

std::count_if(v.begin(), v.end(), [](int x) { return x > 3; }); // сколько > 3
std::transform(v.begin(), v.end(), v.begin(), [](int x) { return x * 2; });
```

### Лямбды

```cpp
// Базовая лямбда:
auto add = [](int a, int b) { return a + b; };

// Захват по значению:
int offset = 10;
auto add_offset = [offset](int x) { return x + offset; };  // копия offset

// Захват по ссылке (осторожно — висячая ссылка если лямбда живёт дольше переменной):
auto inc = [&offset]() { offset++; };

// Захват всего:
auto capture_all_by_value = [=]() { return offset; };
auto capture_all_by_ref = [&]() { offset++; };

// init-capture (C++14):
auto make_adder = [value = std::make_unique<int>(42)](int x) { return x + *value; };

// mutable (можно менять захваченные по значению):
auto counter = [n = 0]() mutable { return n++; };

// generic lambda (C++14):
auto print = [](auto x) { std::cout << x; };
```

### string_view, optional, variant

```cpp
// string_view — ненулевое представление строки, не владеет
void process(std::string_view sv) { /* sv.size(), sv[0], ... */ }
process("hello");            // OK
process(std::string("hi")); // OK
// std::string_view sv = get_string_temp();  // ОПАСНО если get_string возвращает временную

// optional
std::optional<int> find_value(std::vector<int>& v, int key) {
    auto it = std::find(v.begin(), v.end(), key);
    if (it == v.end()) return std::nullopt;
    return *it;
}
auto result = find_value(v, 42);
if (result) std::cout << *result;

// variant
std::variant<int, std::string, double> val;
val = 42; // int
val = "hello"; // string
std::visit([](auto& v) { std::cout << v; }, val);
```

### std::regex

```cpp
#include <regex>
std::string text = "user@example.com";
std::regex email_re(R"([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})");
bool is_email = std::regex_match(text, email_re);  // полное совпадение

std::smatch match;
if (std::regex_search(text, match, email_re)) {
    std::cout << match[0];  // вся совпавшая строка
}

std::string result = std::regex_replace(text, std::regex("@"), " at ");
```

**Важно**: `std::regex` работает, но **очень медленный** — компилирует regex при каждом вызове если не хранить объект. Не использовать в горячих путях.
