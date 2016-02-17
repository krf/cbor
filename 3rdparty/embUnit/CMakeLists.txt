cmake_minimum_required(VERSION 2.8.11)

project(embUnit C)

message(STATUS "FOO ${CMAKE_CURRENT_SOURCE_DIR}/..")
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/..)
add_library(embUnit
    AssertImpl.c      ColorTextOutputter.c  RepeatedTest.c  TestCaller.c  TestResult.c  TestSuite.c      TextUIRunner.c
    ColorOutputter.c  CompilerOutputter.c   stdImpl.c       TestCase.c    TestRunner.c  TextOutputter.c  XMLOutputter.c
)
target_include_directories(embUnit INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/..)
