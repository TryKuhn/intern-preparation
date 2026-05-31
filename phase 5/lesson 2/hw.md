### A. Трейты своими руками (около 60 мин)

Реализуй без использования `<type_traits>` (только для обучения):

1. `is_same<T, U>` — true если T == U.
2. `is_pointer<T>` — true если T является указателем.
3. `remove_const<T>` — убирает const.
4. `remove_reference<T>` — убирает ссылку.
5. `decay<T>` — комбинация: убрать ссылку + убрать top-level const + array→pointer.
6. `conditional<B, T, F>` — если B, то T, иначе F.
7. `is_base_of<Base, Derived>` — труднее: через SFINAE и неявное преобразование.

Для каждого трейта напиши static_assert-тесты:
```cpp
static_assert(is_same<int, int>::value);
static_assert(!is_same<int, double>::value);
// ...
```

### B. enable_if и SFINAE (около 40 мин)

1. Напиши `to_string(x)` — перегруженную для: arithmetic types (std::to_string), std::string (возвращает x), контейнеров (итерирует и собирает).
   Используй enable_if для диспетчеризации.

2. Напиши `serialize(x)` через void_t — использует `x.serialize()` если есть, иначе `std::to_string(x)`.

3. Tag dispatch версия `process(x)`: для integral, floating_point и всего остального.

### C. std::move и std::forward изнутри (около 30 мин)

1. Реализуй `my_move<T>(x)` — точная копия std::move. Напиши тесты показывающие идентичное поведение.
2. Реализуй `my_forward<T>(x)` — точная копия std::forward. Напиши wrapper:
   ```cpp
   template<typename T, typename... Args>
   T* my_make(Args&&... args) {
       return new T(my_forward<Args>(args)...);
   }
   ```
   Убедись через счётчик копирований/перемещений что forward сохраняет категорию.

### D. Detection idiom (около 20 мин)

1. Напиши детектор `has_push_back<T>` — true для vector/list, false для array/set.
2. Напиши `has_operator_less<T>` — true если `T a, b; a < b` компилируется.
3. Используй has_operator_less для условной реализации `my_sort(container)` — вызывает std::sort только если элементы сравнимы.

### Самопроверка (около 15 мин, письменно в `selfcheck.md`)

1. Что такое SFINAE? Почему failure «не ошибка»?
2. Как enable_if работает когда condition = false?
3. Зачем void_t?
4. Как std::move реализован? Почему возвращает rvalue-ссылку?
5. Tag dispatch vs enable_if — когда что использовать?

### Критерий "сделано"
Все трейты проходят static_assert тесты, to_string работает для трёх типов, my_move и my_forward идентичны стандартным.
