
# Copyright Disney Enterprises, Inc.  All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License
# and the following modification to it: Section 6 Trademarks.
# deleted and replaced with:
#
# 6. Trademarks. This License does not grant permission to use the
# trade names, trademarks, service marks, or product names of the
# Licensor and its affiliates, except as required for reproducing
# the content of the NOTICE file.
#
# You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0


## CMake compatibility issues: don't modify this, please!
CMAKE_MINIMUM_REQUIRED( VERSION 2.4.6 )
MARK_AS_ADVANCED(CMAKE_BACKWARDS_COMPATIBILITY)
## allow more human readable "if then else" constructs
SET( CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS TRUE )
## Use verbose make file
SET( CMAKE_VERBOSE_MAKEFILE TRUE )
set(CMAKE_SKIP_BUILD_RPATH TRUE)

## project name & version
PROJECT( ptexutils )
SET( ${PROJECT_NAME}_MAJOR_VERSION 0 )
SET( ${PROJECT_NAME}_MINOR_VERSION 1 )
SET( ${PROJECT_NAME}_PATCH_LEVEL 0 )

set(BIN_INSTALL_DIR bin CACHE STRING "Executable install directory")

# output executable and library path
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

## Setup platform specific helper defines build variants
if(WIN32)
  ADD_DEFINITIONS (-DPTEXUTILS_WIN32 -D_USE_MATH_DEFINES)
endif(WIN32)

## Choose build options
# Disney specific method of choosing variant
if("$ENV{FLAVOR}" STREQUAL "optimize")
    SET(CMAKE_BUILD_TYPE "Release" CACHE STRING "type of build" FORCE)
endif("$ENV{FLAVOR}" STREQUAL "optimize")
if("$ENV{FLAVOR}" STREQUAL "debug")
    SET(CMAKE_BUILD_TYPE "Debug" CACHE STRING "type of build" FORCE)
endif("$ENV{FLAVOR}" STREQUAL "debug")
# Set to release if nothing else defined
if(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE "Release" CACHE STRING
      "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel."
      FORCE)
endif(NOT CMAKE_BUILD_TYPE)

## Set install location
if (NOT DEFINED CMAKE_INSTALL_PREFIX)
    EXECUTE_PROCESS(COMMAND sh -c "echo `uname`-`uname -r | cut -d'-' -f1`-`uname -m`"
                    OUTPUT_VARIABLE VARIANT_DIRECTORY
		    OUTPUT_STRIP_TRAILING_WHITESPACE)
    SET(CMAKE_INSTALL_PREFIX "${CMAKE_SOURCE_DIR}/${VARIANT_DIRECTORY}")
endif(NOT DEFINED CMAKE_INSTALL_PREFIX)

## Set the cmake module path
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

## Search for useful libraries
find_package(PTex REQUIRED)
find_package(OpenMP REQUIRED)
find_package(GLUT REQUIRED)
find_package(GLEW REQUIRED)
find_package(OpenGL REQUIRED)
find_package(OIIO REQUIRED)

## Trasitive dependency, needed when linking with static libPTex
find_package(ZLIB REQUIRED)

#INCLUDE_DIRECTORIES( ${CMAKE_SOURCE_DIR}/src/lib )

## Use static ptex library
option(PTEX_USE_STATIC_LIBS "Link against Ptex static libraries" OFF)
if(PTEX_USE_STATIC_LIBS)
    add_definitions(-DPTEX_STATIC)
endif()

## Traverse subdirectories
ADD_SUBDIRECTORY (src)
