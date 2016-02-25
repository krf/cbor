cmake_minimum_required(VERSION 2.8.11)

set(CBOR_VERSION_MAJOR 0)
set(CBOR_VERSION_MINOR 0)
set(CBOR_VERSION_PATCH 1)
set(CBOR_VERSION "${CBOR_VERSION_MAJOR}.${CBOR_VERSION_MINOR}.${CBOR_VERSION_PATCH}")

project(cbor C)

include(ExternalProject)

# BEGIN: Options
option(CBOR_NO_FLOAT            "Build without floating point support" OFF)
option(CBOR_NO_CTIME            "Build without ctime support" OFF)
option(CBOR_NO_SEMANTIC_TAGGING "Build without CBOR semantic tagging support" OFF)
option(CBOR_NO_PRINT            "Build without printing support (i.e. no printf)" OFF)
option(CBOR_NO_TESTS            "Build without unit tests" OFF)
# ENDF

message("Build type: ${CMAKE_BUILD_TYPE}")

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE ON)

# BEGIN: Compiler flags
if (CBOR_NO_FLOAT)
   add_definitions(-DCBOR_NO_FLOAT)
endif()
if (CBOR_NO_CTIME)
   add_definitions(-DCBOR_NO_CTIME)
endif()
if (CBOR_NO_SEMANTIC_TAGGING)
   add_definitions(-DCBOR_NO_SEMANTIC_TAGGING)
endif()
if (CBOR_NO_PRINT)
   add_definitions(-DCBOR_NO_PRINT)
endif()

if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_C_COMPILER_ID STREQUAL "Clang")
    set(CMAKE_C_FLAGS "-Werror=implicit-function-declaration -Werror=missing-prototypes -Werror=missing-declarations ${CMAKE_C_FLAGS}")
    set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--fatal-warnings -Wl,--no-undefined -lc ${CMAKE_SHARED_LINKER_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "-Wl,--fatal-warnings -Wl,--no-undefined -lc ${CMAKE_EXE_LINKER_FLAGS}")
endif()
# END

add_library(cbor cbor.c)

if (NOT CBOR_NO_TESTS)
    ExternalProject_Add(embUnitProject
        URL ${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/embUnit/
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/embUnit/
        DOWNLOAD_COMMAND ""
        PREFIX embUnit
        CONFIGURE_COMMAND ${CMAKE_COMMAND}
            -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
            ${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/embUnit/
        #BUILD_IN_SOURCE 1
        #BUILD_COMMAND make && cd textui/ && make
        INSTALL_COMMAND ""
    )
    ExternalProject_Get_Property(embUnitProject source_dir)
    ExternalProject_Get_Property(embUnitProject binary_dir)

    set(include_dir "${source_dir}/..")
    # work-around: https://cmake.org/Bug/view.php?id=15052
    file(MAKE_DIRECTORY ${include_dir})

    add_library(embUnit IMPORTED STATIC)
    set_target_properties(embUnit PROPERTIES
        IMPORTED_LOCATION ${binary_dir}/libembUnit.a
        INTERFACE_INCLUDE_DIRECTORIES ${include_dir}
    )
    add_dependencies(embUnit embUnitProject)
    add_library(embUnit_textui IMPORTED STATIC)

    add_subdirectory(tests)
endif()
