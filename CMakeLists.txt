cmake_minimum_required ( VERSION 2.8.11 )

set(CBOR_VERSION_MAJOR 0)
set(CBOR_VERSION_MINOR 0)
set(CBOR_VERSION_PATCH 1)
set(CBOR_VERSION "${CBOR_VERSION_MAJOR}.${CBOR_VERSION_MINOR}.${CBOR_VERSION_PATCH}")

project(cbor C)

# BEGIN: Options
option(CBOR_NO_FLOAT "Build without floating point support" OFF)
# ENDF

message("Build type: ${CMAKE_BUILD_TYPE}")

set(CMAKE_INCLUDE_CURRENT_DIR ON)

if (CBOR_NO_FLOAT)
   add_definitions(-DCBOR_NO_FLOAT)
endif()

if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_C_COMPILER_ID STREQUAL "Clang")
    set(CMAKE_C_FLAGS "-Werror=implicit-function-declaration -Werror=missing-prototypes -Werror=missing-declarations ${CMAKE_C_FLAGS}")
endif()

add_library(cbor cbor.c)

# TODO: Re-enable tests
#add_subdirectory(tests)
