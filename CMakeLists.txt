cmake_minimum_required(VERSION 3.4)

project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION_TWEAK 0)

set(PRINT_VERSION
    ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK}
)

set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

option(BUILD_TESTS "Build tests" OFF)

add_library(print STATIC sources/print.cpp)
target_include_directories(print PUBLIC include)

add_executable(example1 examples/example1.cpp)
target_link_libraries(example1 print)

add_executable(example2 examples/example2.cpp)
target_link_libraries(example2 print)

add_executable(solver examples/example1.cpp)
target_link_libraries(solver print)

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

include(CPackConfig.cmake)
