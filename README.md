# Лабораторная работа №4

[![CMake CI](https://github.com/VincentTheBrainfuqqer/lab04/actions/workflows/ci.yml/badge.svg)](https://github.com/VincentTheBrainfuqqer/lab04/actions/workflows/ci.yml)

## Цель работы

Познакомиться с системой непрерывной интеграции и настроить автоматическую сборку CMake-проекта.

## Задание

В лабораторной работе требовалось настроить автоматическую сборку проекта при помощи CI-сервиса.

Так как Travis CI сейчас неудобно использовать из-за платного доступа, вместо него был использован GitHub Actions.

## Выполнение работы

В качестве основы был использован проект из лабораторной работы №3.

Для настройки автоматической сборки была создана директория:

```text
.github/workflows
```

В ней был создан файл:

```text
ci.yml
```

Итоговая структура проекта:

```text
lab04/
├── .github/
│   └── workflows/
│       └── ci.yml
├── examples/
│   └── example1.cpp
├── include/
│   └── print.hpp
├── sources/
│   └── print.cpp
├── CMakeLists.txt
└── README.md
```

## Файл GitHub Actions

```yml
name: CMake CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Configure CMake
        run: cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install

      - name: Build
        run: cmake --build _build

      - name: Install
        run: cmake --build _build --target install
```

## Описание workflow

Workflow запускается автоматически при каждом `push` или `pull request` в ветку `main`.

Он выполняет следующие действия:

1. Загружает код репозитория.
2. Конфигурирует проект с помощью CMake.
3. Собирает проект.
4. Выполняет установку проекта в директорию `_install`.

## Проверка сборки локально

Для локальной проверки использовались команды:

```bash
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
cmake --build _build
cmake --build _build --target install
```

После сборки можно запустить пример:

```bash
_build/example1
```

Результат работы программы:

```text
hello
```

## Отправка проекта на GitHub

Для отправки изменений использовались команды:

```bash
git add .
git commit -m "added GitHub Actions CI"
git push -u origin main
```

## Вывод

В ходе лабораторной работы была настроена система непрерывной интеграции для CMake-проекта.

Вместо Travis CI был использован GitHub Actions. После каждого обновления репозитория GitHub автоматически запускает сборку проекта и проверяет, что проект успешно компилируется.
