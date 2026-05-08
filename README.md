[![Docker Build](https://github.com/Levgor-cmd/lab08/actions/workflows/docker.yml/badge.svg)](https://github.com/Levgor-cmd/lab08/actions/workflows/docker.yml)

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала

## Отчёт к лабораторной работе 08

### 1. Подготовка репозитория

```bash
export GITHUB_USERNAME=Levgor-cmd
cd ${GITHUB_USERNAME}/workspace
source scripts/activate
git clone https://github.com/${GITHUB_USERNAME}/lab07 lab08
cd lab08
git remote remove origin
git remote add origin https://github.com/${GITHUB_USERNAME}/lab08
```

### 2. Создание Dockerfile

```dockerfile
FROM ubuntu:22.04

RUN apt update && apt install -y gcc g++ cmake make

COPY . print/
WORKDIR print

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install

ENV LOG_PATH /home/logs/log.txt

VOLUME /home/logs

WORKDIR _install/bin

ENTRYPOINT ["./demo"]
```


### 3. Сборка Docker образа

```bash
sudo docker build -t logger .
```

**Результат:**
```
[+] Building 100.7s (13/13) FINISHED
 => naming to docker.io/library/logger
```

---

### 4. Просмотр образов

```bash
sudo docker images | grep logger
```

**Результат:**
```
logger       latest    488555733544   7 minutes ago   478MB
```


### 5. Запуск контейнера

```bash
sudo docker run -it -v "$(pwd)/logs/:/home/logs/" logger
```

```
hello
world
(Ctrl+D, чтобы выйти)
```

### 6. Проверка логов

```bash
cat logs/log.txt
```

**Результат:**
```
hello
world
```

### 7. Инспекция образа

```bash
sudo docker inspect logger
```

**Полученная информация:**
- **Размер:** 478MB
- **Точка входа:** `./demo`
- **Рабочая директория:** `/print/_install/bin`
- **Переменные окружения:** `LOG_PATH=/home/logs/log.txt`
- **Том:** `/home/logs`


### 8. Настройка CI (GitHub Actions)

**.github/workflows/docker.yml:**
```yaml
name: Docker Build

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Build Docker image
      run: docker build -t logger .

    - name: Test Docker container
      run: |
        mkdir -p logs
        echo "test message" | docker run -i -v "$(pwd)/logs/:/home/logs/" logger
        cat logs/log.txt
```

#### Коммитим & пушим все изменения на удалённый репозиторий
