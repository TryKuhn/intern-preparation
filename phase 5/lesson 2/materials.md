### Механизм SFINAE

**SFINAE**: при попытке подставить тип в шаблон, если возникает ошибка — эта перегрузка тихо отбрасывается, а не вызывает ошибку компиляции.

```cpp
// Эта функция принимается только для типов с .size():
template<typename T>
auto get_size(const T& x) -> decltype(x.size()) {
    return x.size();  // если T не имеет size() — SFINAE, перегрузка отброшена
}

// Fallback для типов без .size():
template<typename T>
size_t get_size(const T& x) { return 1; }  // всегда подходит
```

### enable_if

```cpp
// Полная реализация enable_if:
template<bool B, typename T = void> struct enable_if {};
template<typename T> struct enable_if<true, T> { using type = T; };
template<bool B, typename T = void>
using enable_if_t = typename enable_if<B, T>::type;

// Применение — перегрузки по свойству типа:
template<typename T, enable_if_t<std::is_integral_v<T>, int> = 0>
void print(T x) { std::cout << "int: " << x; }

template<typename T, enable_if_t<std::is_floating_point_v<T>, int> = 0>
void print(T x) { std::cout << "float: " << x; }
```

### void_t и detection idiom

```cpp
// void_t: std::void_t<...> существует для любых типов (C++17)
// Используется для проверки существования выражения:

// Есть ли у T метод .serialize()?
template<typename T, typename = void>
struct has_serialize : std::false_type {};

template<typename T>
struct has_serialize<T, std::void_t<decltype(std::declval<T>().serialize())>>
  : std::true_type {};
```

### Трейты своими руками

```cpp
// Базовые блоки:
template<typename T, T v>
struct integral_constant { static constexpr T value = v; };
using true_type  = integral_constant<bool, true>;
using false_type = integral_constant<bool, false>;

// is_same:
template<typename T, typename U> struct is_same : false_type {};
template<typename T>             struct is_same<T, T> : true_type {};

// remove_const:
template<typename T>        struct remove_const       { using type = T; };
template<typename T>        struct remove_const<const T> { using type = T; };

// remove_reference:
template<typename T>  struct remove_reference       { using type = T; };
template<typename T>  struct remove_reference<T&>   { using type = T; };
template<typename T>  struct remove_reference<T&&>  { using type = T; };

// decay (упрощённо): remove_reference → remove_const → array→pointer
template<typename T>
struct decay { using type = std::remove_cv_t<std::remove_reference_t<T>>; };
```

### std::move и std::forward — изнутри

```cpp
template<typename T>
constexpr typename std::remove_reference<T>::type&&
move(T&& x) noexcept {
    return static_cast<typename std::remove_reference<T>::type&&>(x);
}

// forward: сохраняет категорию значения
template<typename T>
constexpr T&& forward(typename std::remove_reference<T>::type& x) noexcept {
    return static_cast<T&&>(x);
}
template<typename T>
constexpr T&& forward(typename std::remove_reference<T>::type&& x) noexcept {
    return static_cast<T&&>(x);
}
```

### Tag dispatch

```cpp
struct integral_tag {};
struct float_tag {};

template<typename T>
void process_impl(T x, integral_tag) { std::cout << "integral"; }

template<typename T>
void process_impl(T x, float_tag) { std::cout << "float"; }

template<typename T>
void process(T x) {
    using tag = std::conditional_t<std::is_integral_v<T>, integral_tag, float_tag>;
    process_impl(x, tag{});
}
```
