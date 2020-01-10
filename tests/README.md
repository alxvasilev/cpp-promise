# Tests for the promise library

This directory contains the test suite for the promise.hpp library. The tests use the `async-test` unit test framework,
which should already be checked as a submodule in this directory.

There are two ways to build the test suite "out of the box" - using the provided GNU `Makefile`, or using the provided `CMakeLists.txt` file.

## Using the Makefile
This option supports only `gcc` and `clang` compilers and requires GNU `make`.
Open a console in the `tests` subdir and type `make run`. The test suite should build and run.

## Using the CMakeLists.txt file
This option is compiler-agnostic and requires `cmake`
Open a terminal in `tests`, create a `build` subdir, and then run `cmake ..`, optionally selecting the type of project you want with the `-G <generator-name>` option, i.e. `cmake -G "Visual Studio 15 2017" ..`

## Manual build
You can easily build the test suite manually. Simply compile `promise-test.cpp`,
providing include path to the directories `./asyc-test` and `..`. Note that C++11 support must be enabled in the compiler.
