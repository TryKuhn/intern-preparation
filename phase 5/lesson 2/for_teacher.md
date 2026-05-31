0–10 мин. Почему нельзя просто if.
```cpp
template<typename T>
void process(T x) {
    if (std::is_integral_v<T>) {
        x.bit_count();  // Ошибка компиляции для T=double — оба пути компилируются!
    }
}
```
Компилятор инстанциирует обе ветки. `if constexpr` (C++17) — вот правильное решение. SFINAE — старый способ, до `if constexpr`.

10–45 мин. Механизм SFINAE и enable_if.
```cpp
// enable_if: type существует только если B = true
template<bool B, typename T = void>
struct enable_if {};
template<typename T>
struct enable_if<true, T> { using type = T; };

// Использование:
template<typename T,
         typename = std::enable_if_t<std::is_integral_v<T>>>
void process_int(T x) { std::cout << "integral: " << x; }

template<typename T,
         typename = std::enable_if_t<!std::is_integral_v<T>>>
void process_float(T x) { std::cout << "float: " << x; }
```
Объясни: при T=double попытка создать `enable_if<false>::type` — failure (нет type). SFINAE выбросит эту перегрузку из рассмотрения. Останется только вторая.

`void_t` (C++17):
```cpp
template<typename T, typename = void>
struct has_size : std::false_type {};

template<typename T>
struct has_size<T, std::void_t<decltype(std::declval<T>().size())>>
  : std::true_type {};
```

45–70 мин. Пишем трейты своими руками.
```cpp
template<typename T>
struct remove_reference { using type = T; };
template<typename T>
struct remove_reference<T&> { using type = T; };
template<typename T>
struct remove_reference<T&&> { using type = T; };

// std::move изнутри:
template<typename T>
typename remove_reference<T>::type&& my_move(T&& x) noexcept {
    return static_cast<typename remove_reference<T>::type&&>(x);
}

// std::forward изнутри:
template<typename T>
T&& my_forward(typename remove_reference<T>::type& x) noexcept {
    return static_cast<T&&>(x);
}
template<typename T>
T&& my_forward(typename remove_reference<T>::type&& x) noexcept {
    return static_cast<T&&>(x);
}
```

70–82 мин. Tag dispatch. Выдача ДЗ.
