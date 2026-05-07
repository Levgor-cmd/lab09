[![CI](https://github.com/Levgor-cmd/lab07/actions/workflows/ci.yml/badge.svg?branch=main&event=push)](https://github.com/Levgor-cmd/lab07/actions/workflows/ci.yml)

## Отчёт к лабораторной работе 07

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала

### 1. Подготовка репозитория

```bash
export GITHUB_USERNAME=Levgor-cmd
cd ${GITHUB_USERNAME}/workspace
source scripts/activate
git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07
cd projects/lab07
git remote remove origin
git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```

 

### 2. Удаление старого подмодуля gtest

```bash
git rm -rf third-party/gtest
git add -A
git commit -m "remove gtest submodule"
```



### 3. Настройка CMakeLists.txt с использованием FetchContent

```cmake
cmake_minimum_required(VERSION 3.14)
project(print)

set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION_TWEAK 0)
set(PRINT_VERSION
  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

# Библиотека print
add_library(print STATIC sources/print.cpp)
target_include_directories(print PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

# Тестирование через FetchContent
if(BUILD_TESTS)
  enable_testing()
  include(FetchContent)
  FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG v1.15.2
  )
  FetchContent_MakeAvailable(googletest)
  
  file(GLOB TEST_SOURCES tests/*.cpp)
  add_executable(check ${TEST_SOURCES})
  target_link_libraries(check print GTest::gtest_main)
  add_test(NAME check COMMAND check)
endif()

# Demo приложение
add_executable(demo demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)

include(CPackConfig.cmake)
```
**Комментарий:** Вместо устаревшего Hunter использован нативный FetchContent для автоматической загрузки Google Test.



### 4. Создание demo приложения

```cpp
#include <print.hpp>
#include <iostream>
#include <fstream>
#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
  return 0;
}
```
**Комментарий:** Приложение читает слова из stdin и записывает их в файл, указанный в переменной окружения LOG_PATH.



### 5. Сборка проекта

```bash
cmake -H. -B_builds -DBUILD_TESTS=ON
cmake --build _builds
```

**Результат сборки:**
- Библиотека `libprint.a`
- Тесты `check`
- Demo приложение `demo`
- Google Test (автоматически загружен через FetchContent)



### 6. Запуск тестов

```bash
_builds/check
```
**Результат:**
```
[==========] Running 1 test from 1 test suite.
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (0 ms)
[  PASSED  ] 1 test.
```



### 7. Проверка demo приложения

```bash
export LOG_PATH=/tmp/log.txt
echo "Hello from demo" | _builds/demo
cat /tmp/log.txt
```

**Результат:**
```
Hello
from
demo
```



### 8. Установка приложения

```bash
cmake --install _builds --prefix ./_install
```

**Структура установки:**
```
_install/
├── bin/
│   └── demo
├── include/
└── lib/
    ├── libprint.a
    └── cmake/
```



### 9. Создание пакетов CPack

```bash
cd _builds
cpack -G "TGZ"   # создан print-0.1.0.0-Linux.tar.gz
cpack -G "RPM"   # создан print-0.1.0.0-Linux.rpm
cpack -G "DEB"   # пропущен (требуется настройка maintainer)
```
