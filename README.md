## Отчёт к лабораторной работе 03

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала:

bash```
lev@debian:~$ export GITHUB_USERNAME=Levgor-cmd
lev@debian:~$ cd ${GITHUB_USERNAME}/workspace
lev@debian:~/Levgor-cmd/workspace$ source scripts/activate

lev@debian:~/Levgor-cmd/workspace$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
lev@debian:~/Levgor-cmd/workspace$ cd projects/lab03
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ git remote remove origin
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ g++ -std=c++11 -I./include -c sources/print.cpp
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ ar rvs print.a print.o
a - print.o

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ g++ -std=c++11 -I./include -c examples/example1.cpp
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ g++ example1.o print.a -o example1
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ ./example1 && echo
Hello

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ g++ -std=c++11 -I./include -c examples/example2.cpp
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ g++ example2.o print.a -o example2
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ ./example2
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cat log.txt && echo
hello

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cmake -H. -B_build
-- Configuring done
-- Build files have been written to: /home/lev/Levgor-cmd/workspace/projects/lab03/_build

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cmake --build _build
[100%] Built target print

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cat >> CMakeLists.txt <<EOF
add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cmake --build _build
[100%] Built target example2

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ _build/example1 && echo
Hello

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ _build/example2
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cat log.txt && echo
hello

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ git clone https://github.com/tp-labs/lab03 tmp
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ mv -f tmp/CMakeLists.txt .
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ rm -rf tmp

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ cmake --build _build --target install
[100%] Built target print
-- Installing: /home/lev/Levgor-cmd/workspace/projects/lab03/_install/lib/libprint.a
-- Installing: /home/lev/Levgor-cmd/workspace/projects/lab03/_install/include/print.h
-- Installing: /home/lev/Levgor-cmd/workspace/projects/lab03/_install/cmake/print-config.cmake

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ tree _install
_install
├── cmake
├── include
│   ├── print.h
│   └── print.hpp
└── lib
    └── libprint.a

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ git add CMakeLists.txt
lev@debian:~/Levgor-cmd/workspace/projects/lab03$ git commit -m"added CMakeLists.txt"
[main 9c95911] added CMakeLists.txt

lev@debian:~/Levgor-cmd/workspace/projects/lab03$ git push origin main
To https://github.com/Levgor-cmd/lab03.git
 * [new branch]      main -> main
```
Этот CMakeLists.txt закоммичен на данный репозиторий
