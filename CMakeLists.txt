cmake_minimum_required(VERSION 3.10)
project(print)
set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION_TWEAK 0)
set(PRINT_VERSION
  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

# Добавляем исходники библиотеки print (используем sources вместо src)
add_library(print STATIC sources/print.cpp)
target_include_directories(print PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  
  file(GLOB TEST_SOURCES tests/*.cpp)
  add_executable(check ${TEST_SOURCES})
  target_link_libraries(check print gtest_main)
  add_test(NAME check COMMAND check)
endif()
include(CPackConfig.cmake)
