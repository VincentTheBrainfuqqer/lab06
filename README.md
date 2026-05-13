# Лабораторная работа №6

![CMake CI](https://github.com/VincentTheBrainfuqqer/lab06/actions/workflows/ci.yml/badge.svg)

## Цель работы

Изучить средства пакетирования C++ проектов на примере CPack.

## Задание

В ходе лабораторной работы необходимо:

* создать публичный репозиторий `lab06`;
* использовать проект из предыдущей лабораторной работы как основу;
* настроить CPack;
* собрать пакет проекта;
* подготовить отчет.

В домашней части необходимо настроить сборку пакетов для релизов. При создании git-тега должны собираться архивы исходного кода и пакеты с бинарным файлом `solver`.

## Выполнение работы

Сначала был создан репозиторий `lab06`, после чего в него была скопирована структура проекта из лабораторной работы №5.

    git clone --recurse-submodules https://github.com/VincentTheBrainfuqqer/lab05 projects/lab06
    cd projects/lab06
    git remote remove origin
    git remote add origin https://github.com/VincentTheBrainfuqqer/lab06.git
    git branch -M main

В файл `CMakeLists.txt` была добавлена информация о версии проекта.

    set(PRINT_VERSION_MAJOR 0)
    set(PRINT_VERSION_MINOR 1)
    set(PRINT_VERSION_PATCH 0)
    set(PRINT_VERSION_TWEAK 0)

    set(PRINT_VERSION
        ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK}
    )

    set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

Также был добавлен исполняемый файл `solver`.

    add_executable(solver examples/example1.cpp)
    target_link_libraries(solver print)

После этого были добавлены правила установки для библиотеки, заголовочного файла и исполняемого файла.

    install(TARGETS print
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
    )

    install(TARGETS solver
        RUNTIME DESTINATION bin
    )

    install(FILES include/print.hpp
        DESTINATION include
    )

Для настройки CPack был создан файл `CPackConfig.cmake`.

    include(InstallRequiredSystemLibraries)

    set(CPACK_PACKAGE_NAME "solver")
    set(CPACK_PACKAGE_VENDOR "VincentTheBrainfuqqer")
    set(CPACK_PACKAGE_CONTACT "VincentTheBrainfuqqer@users.noreply.github.com")

    set(CPACK_PACKAGE_VERSION_MAJOR ${PRINT_VERSION_MAJOR})
    set(CPACK_PACKAGE_VERSION_MINOR ${PRINT_VERSION_MINOR})
    set(CPACK_PACKAGE_VERSION_PATCH ${PRINT_VERSION_PATCH})
    set(CPACK_PACKAGE_VERSION_TWEAK ${PRINT_VERSION_TWEAK})
    set(CPACK_PACKAGE_VERSION ${PRINT_VERSION})

    set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
    set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "simple console solver application")

    set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
    set(CPACK_RESOURCE_FILE_README ${CMAKE_CURRENT_SOURCE_DIR}/README.md)

    set(CPACK_PACKAGE_FILE_NAME
        "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CMAKE_SYSTEM_NAME}"
    )

    set(CPACK_SOURCE_PACKAGE_FILE_NAME
        "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-source"
    )

    set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")

    set(CPACK_SOURCE_IGNORE_FILES
        "/_build/"
        "/artifacts/"
        "/.git/"
        "/.github/"
        "~$"
    )

    if(UNIX AND NOT APPLE)
        set(CPACK_PACKAGING_INSTALL_PREFIX "/usr")
    endif()

    set(CPACK_RPM_PACKAGE_NAME "solver")
    set(CPACK_RPM_PACKAGE_LICENSE "MIT")
    set(CPACK_RPM_PACKAGE_GROUP "Development/Tools")
    set(CPACK_RPM_PACKAGE_RELEASE 1)
    set(CPACK_RPM_CHANGELOG_FILE ${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)

    set(CPACK_DEBIAN_PACKAGE_NAME "solver")
    set(CPACK_DEBIAN_PACKAGE_MAINTAINER "VincentTheBrainfuqqer")
    set(CPACK_DEBIAN_PACKAGE_SECTION "devel")
    set(CPACK_DEBIAN_PACKAGE_RELEASE 1)

    set(CPACK_WIX_UPGRADE_GUID "7D4A72B7-56C0-4C6A-9B34-9E2B71AF0D10")

    include(CPack)

В конец файла `CMakeLists.txt` был подключен файл с настройками CPack.

    include(CPackConfig.cmake)

Также были созданы файлы `DESCRIPTION` и `ChangeLog.md`.

Файл `DESCRIPTION` содержит краткое описание проекта.

    Solver is a simple console application based on the print library.

    The project contains a static C++ library, examples, tests and CPack configuration.
    It is used to demonstrate packaging with CPack and automatic release publishing with GitHub Actions.

Файл `ChangeLog.md` содержит информацию о первом релизе.

    * Wed May 13 2026 VincentTheBrainfuqqer <VincentTheBrainfuqqer@users.noreply.github.com> 0.1.0.0
    - Initial release
    - Added CPack configuration
    - Added solver executable
    - Added release packages

## Сборка проекта

Для сборки проекта использовались команды:

    cmake -H. -B_build -DBUILD_TESTS=ON
    cmake --build _build

Для запуска тестов использовалась команда:

    cmake --build _build --target test -- ARGS=--verbose

Для сборки пакета использовалась команда:

    cmake --build _build --target package

После сборки в директории `_build` был создан архив проекта.

## GitHub Actions

Вместо Travis CI и AppVeyor был использован GitHub Actions.

Для этого был создан файл:

    .github/workflows/ci.yml

Обычная сборка запускается при каждом push в ветку `main`.

При создании git-тега вида `v*` запускается сборка релиза.

На Linux собираются пакеты:

* `.tar.gz`
* `.zip`
* `.deb`
* `.rpm`

На macOS собирается пакет:

* `.dmg`

На Windows собирается пакет:

* `.msi`

После сборки все файлы автоматически загружаются в GitHub Releases.

Для создания релиза использовались команды:

    git tag v0.1.0.0
    git push origin v0.1.0.0

## Структура проекта

    lab06/
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
    ├── CPackConfig.cmake
    ├── ChangeLog.md
    ├── DESCRIPTION
    ├── LICENSE
    └── README.md

## Результат

В результате выполнения лабораторной работы:

* был создан проект `lab06`;
* была настроена упаковка проекта с помощью CPack;
* был добавлен исполняемый файл `solver`;
* была настроена сборка пакетов;
* была настроена автоматическая публикация релизов через GitHub Actions;
* проект успешно собирается и проходит тестирование.

## Вывод

В ходе лабораторной работы были изучены основы пакетирования C++ проекта с помощью CPack.

Также была выполнена домашняя часть: при создании git-тега GitHub Actions автоматически собирает архивы исходного кода и бинарные пакеты для разных операционных систем.
