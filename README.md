[![CI](https://github.com/Levgor-cmd/lab06/actions/workflows/ci.yml/badge.svg?branch=main&event=push)](https://github.com/Levgor-cmd/lab06/actions/workflows/ci.yml)

## Отчёт к лабораторной работе 06

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала

### 1. Подготовка окружения и репозитория

```bash
export GITHUB_USERNAME=Levgor-cmd
export GITHUB_EMAIL=gorlinskiy.lev@bk.ru
cd ${GITHUB_USERNAME}/workspace
git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06
cd projects/lab06
git remote remove origin
git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```
**Комментарий:** Создана копия репозитория lab05 для дальнейшей работы с пакетированием.


### 2. Добавление версионирования в CMakeLists.txt

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)\
set(PRINT_VERSION_MINOR 1)\
set(PRINT_VERSION_PATCH 0)\
set(PRINT_VERSION_TWEAK 0)\
set(PRINT_VERSION ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})\
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
' CMakeLists.txt
```

**Результат (git diff):**
```diff
diff --git a/CMakeLists.txt b/CMakeLists.txt
index eba607f..f825257 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -1,5 +1,12 @@
 cmake_minimum_required(VERSION 3.10)
 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

 add_library(print STATIC sources/print.cpp)
 target_include_directories(print PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
```

### 3. Создание файлов описания

```bash
touch DESCRIPTION ChangeLog.md
export DATE="$(LANG=en_US date +'%a %b %d %Y')"
```

**DESCRIPTION:**
```
print library - static C++ library for printing
```

**ChangeLog.md:**
```
* ${DATE} Levgor-cmd <gorlinskiy.lev@bk.ru> 0.1.0.0
- Initial RPM release
```

### 4. Настройка CPack

**CPackConfig.cmake:**
```cmake
include(InstallRequiredSystemLibraries)

set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR ${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK ${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION ${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_PACKAGE_RELEASE 1)

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "gorlinskiy.lev@bk.ru")

include(CPack)
```

**Подключение CPack к CMakeLists.txt:**
```bash
echo "" >> CMakeLists.txt
echo "include(CPackConfig.cmake)" >> CMakeLists.txt
```

### 5. Коммит и отправка на GitHub

```bash
git add .
git commit -m "added cpack config"
git push origin main --tags
```

### 6. Сборка пакета

```bash
cmake -H. -B_build
cmake --build _build
cd _build
cpack -G "TGZ"
```

**Результат:**
```
CPack: - package: /home/lev/Levgor-cmd/workspace/projects/lab06/_build/print-0.1.0.0-Source.tar.gz generated.
```

### 7. Установка RPM и создание артефактов

```bash
sudo apt install rpm -y
mkdir -p ../artifacts
mv *.tar.gz ../artifacts/
```

**Проверка:**
```bash
ls -la ../artifacts/
```
```
print-0.1.0.0-Source.tar.gz
```

