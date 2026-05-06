cmake_minimum_required(VERSION 3.4)

project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_TESTS "Build tests" OFF)

add_library(print STATIC sources/print.cpp)
target_include_directories(print PUBLIC include)

add_executable(example1 examples/example1.cpp)
add_executable(example2 examples/example2.cpp)

target_link_libraries(example1 print)
target_link_libraries(example2 print)

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
        LIBRARY DESTINATION lib)
