### Анатомия Makefile

```make
# Синтаксис: РЕЦЕПТ ДОЛЖЕН НАЧИНАТЬСЯ С TAB (не пробелов)!

# Переменные:
CXX      := g++
CXXFLAGS := -std=c++17 -Wall -Wextra -O2
SRCS     := main.cpp utils.cpp server.cpp
OBJS     := $(SRCS:.cpp=.o)
TARGET   := myapp

# Главная цель:
$(TARGET): $(OBJS)
	$(CXX) $^ -o $@

# Pattern rule — вместо повторяющихся правил:
%.o: %.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

# Генерация зависимостей:
DEPS := $(OBJS:.o=.d)
-include $(DEPS)
%.o: %.cpp
	$(CXX) $(CXXFLAGS) -MMD -MP -c $< -o $@

.PHONY: clean all test
clean:
	rm -f $(OBJS) $(DEPS) $(TARGET)
all: $(TARGET)
```

**Автопеременные:**
| Переменная | Значение |
|---|---|
| `$@` | Имя цели |
| `$<` | Первая зависимость |
| `$^` | Все зависимости (без дублей) |
| `$*` | Stem (без расширения в pattern rule) |

### Инкрементальная сборка

make сравнивает timestamp цели и зависимостей. Если зависимость новее — пересобирает.
```
main.o — зависит от main.cpp, utils.h
utils.o — зависит от utils.cpp, utils.h
myapp — зависит от main.o, utils.o

Изменить utils.h → пересобрать main.o, utils.o, myapp
Изменить main.cpp → пересобрать main.o, myapp
touch utils.h → пересобрать всё (timestamp обновился)
```

### CMake

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyProject VERSION 1.0 LANGUAGES CXX)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Исполняемый файл:
add_executable(myapp main.cpp utils.cpp)

# Библиотека:
add_library(mylib STATIC lib.cpp)  # STATIC / SHARED / OBJECT
target_include_directories(mylib PUBLIC include/)
target_link_libraries(myapp PRIVATE mylib)

# Внешняя библиотека через find_package:
find_package(OpenSSL REQUIRED)
target_link_libraries(myapp PRIVATE OpenSSL::SSL)

# Компилятор флаги для конкретного target:
target_compile_options(myapp PRIVATE -Wall -Wextra)
```

```bash
# Out-of-source build:
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)

# Или:
cmake -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build -- -j4
```

### Ninja vs Make

Ninja: параллельная сборка из коробки, быстрее чем Make.
```bash
cmake -B build -G Ninja
cmake --build build
```
