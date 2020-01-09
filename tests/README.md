## Tests for the promise library

This directory contains unit tests for the promise.hpp library. The tests use the `async-test` unit test framework,
which should already be checked as a submodule in this directory.
A Makefile is provided to build and run the tests under gcc or clang. To use it, simply type `make run`.

If you are using another compiler, you can easily build the test suite manually. Simply compile `promise-test.cpp`,
providing include path to the directories `./asyc-test` and `..`. Note that c++11 support must be enabled in the compiler.

