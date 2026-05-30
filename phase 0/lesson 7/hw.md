### A. RAII своими руками (около 40 мин)

1. Напиши `class MutexGuard` который принимает `std::mutex&` в конструкторе (вызывает `lock()`), а в деструкторе — `unlock()`. Убедись что unlock вызывается даже если между lock и unlock брошено исключение.
2. Напиши `class ScopeTimer` — в конструкторе запоминает время (`std::chrono::high_resolution_clock::now()`), в деструкторе выводит прошедшее время. Используй для измерения времени блока кода.
3. Возьми класс `Buffer` из предыдущего урока и перепиши его используя `std::unique_ptr<char[]>` вместо сырого `char*`. Покажи что деструктор теперь не нужен.

### B. unique_ptr (около 40 мин)

1. Перепиши следующий код чтобы не было утечек, используя `unique_ptr`:
   ```cpp
   MyClass* p = new MyClass();
   if (condition) throw std::exception();  // утечка!
   delete p;
   ```
2. Реализуй функцию `make_node(int val)` которая возвращает `unique_ptr<Node>`. Реализуй связный список через `unique_ptr<Node> next` в каждом узле. Покажи что memory не утекает.
3. Напиши `unique_ptr` с кастомным делитером для `FILE*`: `auto f = std::unique_ptr<FILE, decltype(&fclose)>(fopen("test.txt", "w"), fclose);`

### C. shared_ptr и weak_ptr (около 50 мин)

1. Воспроизведи цикл `shared_ptr`:
   ```cpp
   struct A { std::shared_ptr<B> b; };
   struct B { std::shared_ptr<A> a; };
   auto a = std::make_shared<A>();
   auto b = std::make_shared<B>();
   a->b = b; b->a = a;
   // Проверь через valgrind или ASan — утечка!
   ```
   Исправь, заменив один из указателей на `weak_ptr`.
2. Напиши `class Cache` хранящий `std::unordered_map<int, std::weak_ptr<Resource>>`. Функция `get(int id)` возвращает `shared_ptr<Resource>` — существующий (если не разрушен) или создаёт новый. Это паттерн «слабый кэш».
3. Замерь производительность: создай 10 000 000 `make_shared<int>` vs `make_unique<int>`. Сравни время и объясни разницу.

### D. enable_shared_from_this (около 20 мин)

1. Напиши класс `AsyncTask : public enable_shared_from_this<AsyncTask>` с методом `run()` который создаёт `std::thread` захватывающий `shared_from_this()`. Убедись что объект не разрушается до завершения потока.
2. Покажи что происходит если вызвать `shared_from_this()` до создания `shared_ptr` — поймай исключение `bad_weak_ptr`.

### Самопроверка (около 20 мин, письменно в `selfcheck.md`)

1. Что такое RAII? Почему именно «в деструкторе»?
2. В чём разница между `unique_ptr` и `shared_ptr` с точки зрения семантики владения?
3. Почему нельзя копировать `unique_ptr`?
4. Что такое control block у `shared_ptr`? Почему атомарный счётчик стоит денег?
5. Как возникает утечка при циклических `shared_ptr`? Как исправить?
6. Чем `make_shared` отличается от `shared_ptr(new T)`?
7. Что такое «невладеющий указатель»? Можно ли передавать `unique_ptr.get()` в функции?

### Критерий "сделано"
Цикл shared_ptr воспроизведён и исправлен (valgrind чистый), ScopeTimer работает, класс Buffer переписан на unique_ptr без деструктора.
