[![CI](https://github.com/Levgor-cmd/lab04/actions/workflows/ci.yml/badge.svg)](https://github.com/Levgor-cmd/lab04/actions/workflows/ci.yml)
[![CI status](https://github.com/Levgor-cmd/lab04/actions/workflows/ci.yml/badge.svg)](https://github.com/Levgor-cmd/lab04/actions/workflows/ci.yml)
## Отчёт к лабораторной работе 04

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала, адаптированным под github actions:

```bash
lev@debian:~$ export GITHUB_USERNAME=Levgor-cmd
lev@debian:~$ cd ${GITHUB_USERNAME}/workspace
lev@debian:~/Levgor-cmd/workspace$ pushd .
~/Levgor-cmd/workspace ~/Levgor-cmd/workspace
lev@debian:~/Levgor-cmd/workspace$ source scripts/activate


lev@debian:~/Levgor-cmd/workspace$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
Клонирование в «projects/lab04»...
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (21/21), done.
Получение объектов: 100% (27/27), 8.44 КиБ | 480.00 КиБ/с, готово.
Определение изменений: 100% (7/7), готово.
remote: Total 27 (delta 7), reused 17 (delta 2), pack-reused 0 (from 0)
lev@debian:~/Levgor-cmd/workspace$ cd projects/lab04
lev@debian:~/Levgor-cmd/workspace/projects/lab04$ git remote remove origin
lev@debian:~/Levgor-cmd/workspace/projects/lab04$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04


lev@debian:~/Levgor-cmd/workspace/projects/lab04$ mkdir -p .github/workflows



lev@debian:~/Levgor-cmd/workspace/projects/lab04$ cat > .github/workflows/ci.yml << 'EOF'
---
name: CI

on:
  push:
    branches: [master, main]
  pull_request:
    branches: [master, main]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        compiler: [gcc, clang]

    steps:
      - uses: actions/checkout@v3

      - name: Install compilers
        run: |
          sudo apt-get update
          sudo apt-get install -y cmake g++ clang

      - name: Configure CMake
        run: cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install

      - name: Build
        run: cmake --build _build

      - name: Install
        run: cmake --build _build --target install
EOF


lev@debian:~/Levgor-cmd/workspace/projects/lab04$ sudo apt update
lev@debian:~/Levgor-cmd/workspace/projects/lab04$ sudo apt install yamllint


lev@debian:~/Levgor-cmd/workspace/projects/lab04$ yamllint .github/workflows/ci.yml
.github/workflows/ci.yml
  4:1       warning  truthy value should be one of [false, true]  (truthy)
```


