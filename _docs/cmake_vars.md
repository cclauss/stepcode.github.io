---
layout: docs
title: CMake variables used by STEPcode
permalink: /docs/cmake_vars/
---


### Contents

* TOC
{:toc}

## Variables you can safely change

### SC_BUILD_SCHEMAS

-   List of schemas to be built. Defaults to special value ALL, which
    builds all schemas in SC/data.
-   Each list entry can be a relative or absolute path to a schema or to
    a directory. If an entry is a directory, CMake looks for **\*.exp**
    within that directory.

### SC_BUILD_TYPE

-   Release, Debug (**default**), etc.
-   See also: CMAKE_BUILD_TYPE documentation

### SC_GENERATE_LEXER_PARSER

-   See comments in root CMakeLists.txt for this variable.

### SC_INSTALL_PREFIX

-   prefix for 'make install'

### SC_IS_SUBBUILD

-   used when building as a subproject, as SCView and BRL-CAD do.
-   example of use:
    <http://github.com/LaurentBauer/SCView/blob/master/CMakeLists.txt>

### SC_MEMMGR_ENABLE_CHECKS

-   enables runtime memory leak checks. Output is very verbose!

### SC_SDAI_ADDITIONAL_EXES_SRCS

-   A list of paths to source files for additional executables that will
    be built for each schema.
-   Each source file is assumed to be stand-alone, much like p21read.cc
-   Each executable will be linked to the same libs as p21read - this
    will **not** work with lazy loading.

## Variables exclusively for CTest

These won't do anything useful unless you're running tests.

### SC_ENABLE_COVERAGE

-   for code coverage tests; requires gcov/lcov - [Linux Test Project: LCOV](http://ltp.sourceforge.net/coverage/lcov.php)

`ctest -S lcov.cmake`

### SC_ENABLE_TESTING

-   tests can be run manually (no submission) by setting this to ON,
    then

`make
make test`

-   also set by CTest - see [testing](/docs/testing/)

## Unlikely to be useful

### SC_ABI_SOVERSION

### SC_BASE_SOURCES

### SC_BINARY_DIR

### SC_CMAKE_DIR

### SC_SOURCE_DIR

### SC_VERSION_MAJOR

### SC_VERSION_MINOR
