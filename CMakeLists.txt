# Copyright (C) 2013-2015 DeathCore <http://www.noffearrdeathproject.net/>

# Set projectname (must be done AFTER setting configurationtypes)
project(Banditos)

# CMake policies (can not be handled elsewhere)
cmake_minimum_required(VERSION 2.8)
cmake_policy(SET CMP0005 OLD)
if(POLICY CMP0043)
  cmake_policy(SET CMP0043 OLD) # Disable 'Ignore COMPILE_DEFINITIONS_<Config> properties'
endif(POLICY CMP0043)

# add this options before PROJECT keyword
set(CMAKE_DISABLE_SOURCE_CHANGES ON)
set(CMAKE_DISABLE_IN_SOURCE_BUILD ON)

# Set RPATH-handing (CMake parameters)
set(CMAKE_SKIP_BUILD_RPATH 0)
set(CMAKE_BUILD_WITH_INSTALL_RPATH 0)
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH 1)

# set macro-directory
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/macros")

# build in Release-mode by default if not explicitly set
if( NOT CMAKE_BUILD_TYPE )
  set(CMAKE_BUILD_TYPE "Release")
endif()

include(CheckCXXSourceRuns)
include(CheckIncludeFiles)

# set default buildoptions and print them
include(cmake/options.cmake)

# turn off PCH totally if enabled (hidden setting, mainly for devs)
if( NOPCH )
  set(USE_COREPCH 0)
  set(USE_SCRIPTPCH 0)
endif()

include(CheckPlatform)

# basic packagesearching and setup (further support will be needed, this is a preliminary release!)
find_package(PCHSupport)
find_package(ACE REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(Threads REQUIRED)

if( NOT USE_MYSQL_SOURCES )
  find_package(MySQL REQUIRED)
endif()

if( UNIX )
  find_package(Readline)
  find_package(ZLIB)
  find_package(BZip2)
endif()

# Find revision ID and hash of the sourcetree
include(cmake/genrev.cmake)

# print out the results before continuing
include(cmake/showoptions.cmake)

# add dependencies
add_subdirectory(dep)

# add core sources
add_subdirectory(src)
