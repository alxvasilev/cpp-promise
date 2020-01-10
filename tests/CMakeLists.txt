cmake_minimum_required(VERSION 2.8)

file(GLOB TEST_HEADERS "async-test/include/*")
if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
	set(CMAKE_CXX_FLAGS "-fsanitize=address -fsanitize=leak") 
	set(CMAKE_EXE_LINKER_FLAGS "-fsanitize=address -fsanitize=leak")
	add_definitions(-std=c++11)
endif()
include_directories(.. async-test/include)
set(CMAKE_BUILD_TYPE Debug)

add_executable(promise-test promise-test.cpp ../promise.hpp ${TEST_HEADERS})

