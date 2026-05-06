[![CI](https://github.com/Levgor-cmd/lab05/actions/workflows/ci.yml/badge.svg)](https://github.com/Levgor-cmd/lab05/actions/workflows/ci.yml)

## Отчёт к лабораторной работе 05

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала

### 1. Подготовка репозитория

```bash
export GITHUB_USERNAME=Levgor-cmd
cd ${GITHUB_USERNAME}/workspace
git clone https://github.com/${GITHUB_USERNAME}/lab04 projects/lab05
cd projects/lab05
git remote remove origin
git remote add origin https://github.com/${GITHUB_USERNAME}/lab05
```
**Комментарий:** Репозиторий lab04 скопирован в lab05 для дальнейшей работы.

---

### 2. Подключение Google Test

```bash
mkdir third-party
git submodule add https://github.com/google/googletest third-party/gtest
cd third-party/gtest && git checkout v1.15.2 && cd ../..
git add third-party/gtest
git commit -m "added gtest framework"
```
**Комментарий:** Добавлен подмодуль gtest версии 1.15.2 для модульного тестирования. Версия 1.8.1 вызвала ошибки компиляции, поэтому использована более новая.

---

### 3. Настройка CMake для тестирования

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(print)

add_library(print STATIC sources/print.cpp)
target_include_directories(print PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB TEST_SOURCES tests/*.cpp)
  add_executable(check ${TEST_SOURCES})
  target_link_libraries(check print gtest_main)
  add_test(NAME check COMMAND check)
endif()
```

---

### 4. Написание теста

**tests/test1.cpp:**
```cpp
#include <print.hpp>
#include <gtest/gtest.h>
#include <fstream>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};
  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;
  EXPECT_EQ(result, text);
}
```

---

### 5. Сборка и тестирование

```bash
cmake -H. -B_build -DBUILD_TESTS=ON
cmake --build _build
cmake --build _build --target test
```

**Результат:**
```
100% tests passed, 0 tests failed out of 1
```

**Запуск теста вручную:**
```bash
_build/check
```
```
[==========] Running 1 test from 1 test suite.
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (0 ms)
[  PASSED  ] 1 test.
```

---

### 6. Настройка CI (GitHub Actions)

**.github/workflows/ci.yml:**
```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Configure
      run: cmake -H. -B_build -DBUILD_TESTS=ON
    - name: Build
      run: cmake --build _build
    - name: Test
      run: cd _build && ctest
```
