### A. Makefile (около 50 мин)
1. Создай проект из 4 файлов: `main.cpp`, `math.cpp`, `math.h`, `strings.cpp`, `strings.h`. Напиши Makefile с:
   - Pattern rules для компиляции .cpp → .o
   - Линковка в исполняемый файл
   - .PHONY clean, all, install
   - Переменные CXX, CXXFLAGS, TARGET
2. Измени `math.h` — убедись что пересобираются только зависящие файлы.
3. Добавь автоматическое создание зависимостей через `-MMD -MP`. Убедись что изменение заголовочного файла вызывает пересборку.
4. Добавь цель `test` запускающую набор тестов.

### B. CMake (около 50 мин)
1. Перепиши проект из части A через CMakeLists.txt.
2. Разбей на: main executable + статическая библиотека `mathlib`.
3. Добавь поддержку Debug/Release через `CMAKE_BUILD_TYPE` — в Debug включить `-fsanitize=address`, в Release `-O3 -DNDEBUG`.
4. Добавь `enable_testing()` и `add_test(NAME unit_tests COMMAND ./tests)`.
5. Используй `cmake --build build --target test` — убедись что тесты запускаются.

### C. Вопросы для понимания (письменно в `selfcheck.md`)
1. Чем make отличается от cmake?
2. Зачем `.PHONY`?
3. Что делает `-MMD -MP` и зачем?
4. Почему `target_link_libraries` лучше чем `link_libraries`?
5. Что такое out-of-source build? Зачем?
6. Почему таргет «не пересобирается» — 3 возможные причины?
7. `add_library(STATIC)` vs `add_library(SHARED)` — разница?

### Критерий "сделано"
Makefile работает с инкрементальной сборкой, CMake проект собирается в Debug и Release, тесты запускаются через cmake.
