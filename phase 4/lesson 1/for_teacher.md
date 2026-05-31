0–5 мин. touch пересобирает.
Да, `touch main.cpp` обновляет timestamp → make считает файл изменённым → пересобирает. Это поведение нужно знать: иногда помогает (принудительная пересборка), иногда мешает (ложные пересборки).

5–35 мин. Анатомия Makefile.
```make
# Переменные:
CC = g++
CFLAGS = -std=c++17 -Wall -O2
TARGET = myapp

# Цель: зависимости
$(TARGET): main.o utils.o
	$(CC) $^ -o $@           # $^ = все зависимости, $@ = цель

main.o: main.cpp utils.h
	$(CC) $(CFLAGS) -c $< -o $@   # $< = первая зависимость

utils.o: utils.cpp utils.h
	$(CC) $(CFLAGS) -c $< -o $@

# Pattern rule:
%.o: %.cpp
	$(CC) $(CFLAGS) -c $< -o $@

.PHONY: clean all
clean:
	rm -f *.o $(TARGET)
```
`.PHONY`: объявить цель «фиктивной» — не файл. Если бы файл `clean` существовал, `make clean` не работало бы без `.PHONY`.

Разберите пошагово: `make` → нет main.o → собрать main.o → нет utils.o → собрать utils.o → слинковать. Изменить main.cpp → `make` → пересобрать только main.o, слинковать.

35–65 мин. CMake.
```cmake
cmake_minimum_required(VERSION 3.15)
project(MyApp CXX)
set(CMAKE_CXX_STANDARD 17)

add_executable(myapp main.cpp utils.cpp)
target_include_directories(myapp PRIVATE include/)

# Внешняя библиотека:
find_package(Threads REQUIRED)
target_link_libraries(myapp PRIVATE Threads::Threads)

# Debug vs Release:
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(myapp PRIVATE -fsanitize=address -g)
    target_link_options(myapp PRIVATE -fsanitize=address)
endif()
```
```bash
cmake -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build
```
Объясни: CMake генерирует build system (Makefile или Ninja). Out-of-source build обязателен.

65–82 мин. Типичные вопросы: make vs cmake, .PHONY, не пересобирается. Выдача ДЗ.
