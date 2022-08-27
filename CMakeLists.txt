
cmake_minimum_required(VERSION 3.11)

if ((${CMAKE_VERSION} VERSION_EQUAL "3.24") OR (${CMAKE_VERSION} VERSION_GREATER "3.24"))
  cmake_policy(SET CMP0135 NEW)
endif()

project(nonpareil VERSION 0.79 LANGUAGES C)

include(GNUInstallDirs)
include(CheckSymbolExists)
include(FindPkgConfig)
include(ExternalProject)

find_package(BISON REQUIRED)
find_package(FLEX REQUIRED)
find_package(LibXml2 REQUIRED)
find_package(PNG REQUIRED)
find_package(SDL REQUIRED)

find_package(GTK2 2.20 REQUIRED gtk)
if(NOT GTK2_FOUND)
  message(FATAL_ERROR "GTK GTK2 >= 2.20 not found")
endif()

check_symbol_exists(strlcpy string.h HAVE_STRLCPY)
if(HAVE_STRLCPY)
    add_compile_definitions(HAVE_STRLCPY)
endif()

set(ENABLE_DEBUGGER_GUI ON  CACHE BOOL "Enable debugger GUI interface")
set(ENABLE_DEBUGGER_CLI OFF CACHE BOOL "Enable debugger command-line interface")
set(ENABLE_TCL          OFF CACHE BOOL "Use Tcl as debug command interpreter (only with debugger CLI)")
set(ENABLE_READLINE     OFF CACHE BOOL "Use Readline library for command editing and history (only with debugger CLI)")

ExternalProject_Add(nonpareil_roms
  URL      https://web.archive.org/web/20220328100104/https://nonpareil.brouhaha.com/download/nonpareil-0.79.tar.gz
  URL_HASH SHA256=3bcb2f9a829a4cad003f84a42c6f0e31a7bf527e634aea9cf13e370b6c175841
  CONFIGURE_COMMAND ""
  BUILD_COMMAND     ""
  INSTALL_COMMAND   ""
  TEST_COMMAND      ""
)
ExternalProject_Get_Property(nonpareil_roms SOURCE_DIR)
set(nonpareil_roms_SOURCE_DIR ${SOURCE_DIR})

add_subdirectory(asm)
add_subdirectory(doc)
add_subdirectory(image)
add_subdirectory(kml)
add_subdirectory(rom)
add_subdirectory(src)

install(FILES COPYING CREDITS DEBUGGING README TODO
  DESTINATION ${CMAKE_INSTALL_DOCDIR}
)
