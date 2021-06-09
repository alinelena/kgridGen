cmake_minimum_required(VERSION 3.0.0)

project(kgridGen
  VERSION 0.1.1
  HOMEPAGE_URL https://github.com/msg-byu/kgridGen
  LANGUAGES Fortran)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
if (NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
endif()
include(helper)
include(flags)
include(GNUInstallDirs)

set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR})
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_BINDIR})

option(INTERNAL_SYMLIB "Build with Internal symlib rather than external" OFF)

if(INTERNAL_SYMLIB)
    include(ExternalProject)
    ExternalProject_Add(int-symlib
      GIT_REPOSITORY https://github.com/alinelena/symlib
      GIT_TAG master
      GIT_PROGRESS true
      PREFIX int-symlib
      BINARY_DIR int-symlib/build
      SOURCE_DIR int-symlib/symlib
      CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DCMAKE_Fortran_COMPILER=${CMAKE_Fortran_COMPILER}  -DBUILD_SHARED_LIBS=off -DCMAKE_BUILD_TYPE=Release
      )
    set(SYMLIB_INC ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
    set(SYMLIB_LIB ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
    set(LIBS "sym;rational;utils")

else()
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(SYMLIB REQUIRED symlib>=2.0.1)
    set(SYMLIB_INC ${SYMLIB_INCLUDE_DIRS})
    set(SYMLIB_LIB ${SYMLIB_LIBRARY_DIRS})
    set(LIBS ${SYMLIB_LIBRARIES})
    message(STATUS "symlib include dirs ${SYMLIB_INC}")
    message(STATUS "symlib include dirs ${LIBS}")
endif()
link_directories(${SYMLIB_LIB})



add_subdirectory(src)

configure_file(${PROJECT_SOURCE_DIR}/cmake/kgridGen.pc.cmake ${CMAKE_CURRENT_BINARY_DIR}/kgridGen.pc)
install(
  FILES  ${CMAKE_CURRENT_BINARY_DIR}/kgridGen.pc
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
  COMPONENT pkgconfig)

