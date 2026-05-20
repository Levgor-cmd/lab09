```bash
## Отчёт к лабораторной работе 09

### После ознакомления с учебным материалом приступим к выполнению инструкций учебного материала:

```bash
lev@debian:~$ export GITHUB_TOKEN=************************
lev@debian:~$ export GITHUB_USERNAME=Levgor-cmd
lev@debian:~$ export PACKAGE_MANAGER=apt
lev@debian:~$ export GPG_PACKAGE_NAME=gpg2
lev@debian:~$ sudo $PACKAGE_MANAGER install xclip
lev@debian:~$ alias gsed=sed
lev@debian:~$ alias pbcopy='xclip -selection clipboard'
lev@debian:~$ alias pbpaste='xclip -selection clipboard -o'
lev@debian:~$ cd ${GITHUB_USERNAME}/workspace
lev@debian:~/Levgor-cmd/workspace$ pushd .
~/Levgor-cmd/workspace ~/Levgor-cmd/workspace
lev@debian:~/Levgor-cmd/workspace$ source scripts/activate
lev@debian:~/Levgor-cmd/workspace$ sudo apt update
lev@debian:~/Levgor-cmd/workspace$ sudo apt install -y wget golang-go
lev@debian:~/Levgor-cmd/workspace$ go version
go version go1.24.4 linux/amd64
lev@debian:~/Levgor-cmd/workspace$ go install github.com/github-release/github-release@latest
lev@debian:~/Levgor-cmd/workspace$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
Клонирование в «projects/lab09»...
remote: Enumerating objects: 447, done.
remote: Counting objects: 100% (447/447), done.
remote: Compressing objects: 100% (239/239), done.
remote: Total 447 (delta 149), reused 447 (delta 149), pack-reused 0 (from 0)
Получение объектов: 100% (447/447), 3.40 МиБ | 5.60 МиБ/с, готово.
Определение изменений: 100% (149/149), готово.
lev@debian:~/Levgor-cmd/workspace$ cd projects/lab09
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git remote remove origin
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ sed -i 's/lab08/lab09/g' README.md
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ gpg --full-generate-key
# Выбор: 1 (RSA and RSA)
# Размер: 4096
# Срок: 0 (без ограничения)
# Имя: lev
# Email: gorlinskiy.lev@bk.ru
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ gpg --list-secret-keys --keyid-format LONG
/home/lev/.gnupg/pubring.kbx
----------------------------
sec   rsa4096/011178F8EC7DB944 2026-05-20 [SC]
      F11A848EBB32766D6E5A17E5011178F8EC7DB944
uid               [  абсолютно ] lev <gorlinskiy.lev@bk.ru>
ssb   rsa4096/D8AEC6DC2D53AC09 2026-05-20 [E]
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ export GPG_KEY_ID=011178F8EC7DB944
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ gpg --armor --export ${GPG_KEY_ID}
-----BEGIN PGP PUBLIC KEY BLOCK-----
mQINBGoNgkUBEACvVHVt2ezpaWUYkq7/IzlpaoJrsEQOYebf7ZoZyhnURos7VARI
... (ключ добавлен на GitHub) ...
-----END PGP PUBLIC KEY BLOCK-----
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git config user.signingkey 011178F8EC7DB944
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git config gpg.program gpg
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ source ~/.profile
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
-- Configuring done (1.9s)
-- Generating done (0.0s)
-- Build files have been written to: /home/lev/Levgor-cmd/workspace/projects/lab09/_build
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ cmake --build _build --target package
[ 50%] Built target print
[100%] Built target demo
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: - package: /home/lev/Levgor-cmd/workspace/projects/lab09/_build/print-0.1.0.0-Linux.tar.gz generated.
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ mkdir -p .github/workflows
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ cat > .github/workflows/release.yml << 'EOF'
name: Create Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      with:
        fetch-depth: 0

    - name: Setup CMake
      uses: jwlawson/actions-setup-cmake@v1
      with:
        cmake-version: '3.22.x'

    - name: Build package
      run: |
        cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
        cmake --build _build --target package

    - name: Create Release
      uses: softprops/action-gh-release@v1
      with:
        name: "libprint"
        body: "my first release"
        tag_name: ${{ github.ref_name }}
        draft: false
        prerelease: false
        files: |
          _build/*.tar.gz
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
EOF
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git add .github/workflows/release.yml
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git commit -m "Add GitHub Actions workflow for automatic releases"
[main 5500396] Add GitHub Actions workflow for automatic releases
 1 file changed, 49 insertions(+)
 create mode 100644 .github/workflows/release.yml
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git tag -s v0.1.0.0 -m "First release v0.1.0.0"
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git tag -v v0.1.0.0
gpg: Подпись сделана Ср 20 мая 2026 13:13:01 MSK
gpg: ключом RSA с идентификатором F11A848EBB32766D6E5A17E5011178F8EC7DB944
gpg: Действительная подпись пользователя "lev <gorlinskiy.lev@bk.ru>" [абсолютное]
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ git push origin main --tags
Username for 'https://github.com': Levgor-cmd
Password for 'https://Levgor-cmd@github.com':
Перечисление объектов: 453, готово.
Подсчет объектов: 100% (453/453), готово.
Сжатие объектов: 100% (244/244), готово.
Запись объектов: 100% (453/453), 3.40 МиБ | 3.94 МиБ/с, готово.
Total 453 (delta 151), reused 445 (delta 149), pack-reused 0 (from 0)
To https://github.com/Levgor-cmd/lab09
 * [new branch]      main -> main
 * [new tag]         v0.1.0.0 -> v0.1.0.0
lev@debian:~/Levgor-cmd/workspace/projects/lab09$ cd ~/Levgor-cmd/workspace
lev@debian:~/Levgor-cmd/workspace$ git clone https://github.com/tp-labs/lab09 tasks/lab09
Клонирование в «tasks/lab09»...
remote: Enumerating objects: 87, done.
remote: Counting objects: 100% (26/26), done.
remote: Compressing objects: 100% (20/20), done.
remote: Total 87 (delta 8), reused 12 (delta 5), pack-reused 61 (from 1)
Получение объектов: 100% (87/87), 839.09 КиБ | 1.23 МиБ/с, готово.
Определение изменений: 100% (36/36), готово.
lev@debian:~/Levgor-cmd/workspace$ mkdir -p reports/lab09
lev@debian:~/Levgor-cmd/workspace$ cp tasks/lab09/README.md reports/lab09/REPORT.md
lev@debian:~/Levgor-cmd/workspace$ cd reports/lab09
lev@debian:~/Levgor-cmd/workspace/reports/lab09$ nano REPORT.md
```

### GitHub Actions workflow добавлен в репозиторий и настроен на автоматический релиз при создании тега

### Создан подписанный GPG-ключ и добавлен в настройки GitHub

### В репозиторий запушен тег v0.1.0.0, который автоматически запустил создание релиза

### Релиз доступен по ссылке:
https://github.com/Levgor-cmd/lab09/releases/tag/v0.1.0.0
```
