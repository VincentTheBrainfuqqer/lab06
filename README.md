[![CMake CI](https://github.com/VincentTheBrainfuqqer/lab04/actions/workflows/ci.yml/badge.svg)](https://github.com/VincentTheBrainfuqqer/lab04/actions/workflows/ci.yml)
# lab03

Laboratory work 3.

Topic: project build automation using CMake.

## Task

The project from `lab02` was used as a base.  
CMake configuration was added for building the static library `print` and example applications.

Homework part was not completed because it is not required.

## Project structure

```text
.
├── CMakeLists.txt
├── README.md
├── examples
│   ├── example1.cpp
│   └── example2.cpp
├── include
│   └── print.hpp
└── sources
    └── print.cpp
```

## Files

### `include/print.hpp`

Header file with function declarations.

### `sources/print.cpp`

Source file with function definitions.

### `examples/example1.cpp`

Example that prints text to standard output.

### `examples/example2.cpp`

Example that prints text to a file.

### `CMakeLists.txt`

CMake build configuration file.

It builds:

- static library `print`;
- executable `example1`;
- executable `example2`.

## Build

To configure the project:

```bash
cmake -H. -B_build
```

To build the project:

```bash
cmake --build _build
```

## Run examples

Run first example:

```bash
_build/example1 && echo
```

Output:

```text
hello
```

Run second example:

```bash
_build/example2
cat log.txt && echo
```

Output:

```text
hello
```

## Install

Configure project with install directory:

```bash
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
```

Build and install:

```bash
cmake --build _build --target install
```

Check install directory:

```bash
tree _install
```

Expected structure:

```text
_install
├── bin
│   ├── example1
│   └── example2
├── include
│   └── print.hpp
└── lib
    └── libprint.a
```

## Result

The project was successfully configured and built using CMake.

The static library `print` was created.  
The example applications `example1` and `example2` were also built and tested.
