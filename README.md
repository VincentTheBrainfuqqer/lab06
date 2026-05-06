# Лабораторная работа №5

[![CMake CI](https://github.com/VincentTheBrainfuqqer/lab05/actions/workflows/ci.yml/badge.svg)](https://github.com/VincentTheBrainfuqqer/lab05/actions/workflows/ci.yml)

## Цель работы

Изучить основы модульного тестирования в C++ на примере фреймворка Google Test.

## Задание

В ходе лабораторной работы необходимо:

- создать публичный репозиторий `lab05`;
- использовать проект из предыдущей лабораторной работы как основу;
- подключить Google Test;
- добавить тесты для функции `print`;
- настроить сборку проекта с тестами;
- настроить автоматическую проверку через GitHub Actions;
- подготовить отчет.

## Выполнение работы

Сначала был создан репозиторий `lab05`, после чего в него была скопирована структура проекта из лабораторной работы №4.

```bash
git clone https://github.com/VincentTheBrainfuqqer/lab04 lab05
cd lab05
git remote remove origin
git remote add origin https://github.com/VincentTheBrainfuqqer/lab05.git
```

Для подключения Google Test был создан каталог `third-party`, после чего фреймворк был добавлен как submodule.

```bash
mkdir third-party
git submodule add https://github.com/google/googletest third-party/gtest
cd third-party/gtest
git checkout release-1.8.1
cd ../..
```

После этого в `CMakeLists.txt` была добавлена возможность включать сборку тестов через параметр `BUILD_TESTS`.

```cmake
option(BUILD_TESTS "Build tests" OFF)
```

Также был добавлен блок для сборки тестов:

```cmake
if(BUILD_TESTS)
    enable_testing()

    add_subdirectory(third-party/gtest)

    if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        target_compile_options(gtest PRIVATE -Wno-error=maybe-uninitialized)
        target_compile_options(gtest_main PRIVATE -Wno-error=maybe-uninitialized)
    endif()

    file(GLOB TEST_SOURCES tests/*.cpp)

    add_executable(check ${TEST_SOURCES})
    target_link_libraries(check print gtest_main)

    add_test(NAME check COMMAND check)
endif()
```

Параметр `BUILD_TESTS` позволяет собирать проект как с тестами, так и без них.

Был создан каталог `tests`, в который был добавлен файл `test1.cpp`.

```cpp
#include <print.hpp>

#include <gtest/gtest.h>

#include <fstream>
#include <string>

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

В этом тесте проверяется работа функции `print` при выводе строки в файловый поток.

Сначала создается файл `file.txt`, затем в него записывается строка `hello`. После этого файл открывается для чтения, считанное значение сравнивается с исходной строкой.

Если функция работает правильно, тест завершается успешно.

## Сборка проекта

Для сборки проекта с тестами использовались следующие команды:

```bash
cmake -H. -B_build -DBUILD_TESTS=ON
cmake --build _build
```

После сборки был запущен тест:

```bash
cmake --build _build --target test -- ARGS=--verbose
```

Также тест можно запустить напрямую:

```bash
_build/check
```

## GitHub Actions

Вместо Travis CI был использован GitHub Actions.

Для этого был создан файл:

```text
.github/workflows/ci.yml
```

Содержимое workflow:

```yaml
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
        with:
          submodules: recursive

      - name: Configure
        run: cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install -DBUILD_TESTS=ON

      - name: Build
        run: cmake --build _build

      - name: Run tests
        run: cmake --build _build --target test -- ARGS=--verbose

      - name: Install
        run: cmake --build _build --target install
```

Параметр:

```yaml
submodules: recursive
```

нужен для того, чтобы GitHub Actions автоматически загрузил Google Test, так как он подключен как submodule.

## Структура проекта

```text
lab05/
├── .github/
│   └── workflows/
│       └── ci.yml
├── examples/
│   ├── example1.cpp
│   └── example2.cpp
├── include/
│   └── print.hpp
├── sources/
│   └── print.cpp
├── tests/
│   └── test1.cpp
├── third-party/
│   └── gtest/
├── CMakeLists.txt
└── README.md
```

## Результат

В результате выполнения лабораторной работы:

- был создан проект `lab05`;
- был подключен фреймворк Google Test;
- был написан тест для функции `print`;
- была настроена сборка проекта с тестами;
- была настроена автоматическая проверка через GitHub Actions;
- проект успешно собирается и проходит тестирование.

## Вывод

В ходе лабораторной работы был изучен принцип подключения фреймворка Google Test к C++ проекту.

Была добавлена возможность собирать проект с тестами при помощи параметра `BUILD_TESTS`.  
Также был создан простой unit-тест, который проверяет корректность работы функции `print`.

Дополнительно была настроена автоматическая сборка и проверка проекта через GitHub Actions.
