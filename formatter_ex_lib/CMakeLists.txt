cmake_minimum_required(VERSION 2.8)

add_definitions(-std=c++11)

project(my_formatter_ex)
include_directories(formatter_lib)
add_subdirectory(formatter_lib)
add_library(formatter_ex STATIC formatter_ex.h formatter_ex.cpp)
target_link_libraries(formatter_ex formatter)
