cmake_minimum_required(VERSION 3.11)
project(ueberzug LANGUAGES C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(Python_FIND_VIRTUALENV FIRST)
find_package(PythonInterp 3.6 REQUIRED)

exec_program(${PYTHON_EXECUTABLE}
    ARGS "-c \"import sysconfig; print(sysconfig.get_paths()['include'])\""
    OUTPUT_VARIABLE PYTHON_INCLUDE_DIRS
    RETURN_VALUE PYTHON_INCLUDE_DIRS_NOT_FOUND
    )
if(PYTHON_INCLUDE_DIRS_NOT_FOUND)
    message(FATAL_ERROR "Python headers not found")
endif()

find_package(X11 REQUIRED)
pkg_check_modules(XEXT xext REQUIRED IMPORTED_TARGET)
pkg_check_modules(XRES xres REQUIRED IMPORTED_TARGET)

set(UEBERZUG_SOURCES
    "ueberzug/X/X.c"
    "ueberzug/X/Xshm.c"
    "ueberzug/X/display.c"
    "ueberzug/X/window.c"
)

add_library(ueberzug SHARED ${UEBERZUG_SOURCES})

set_target_properties(
    ueberzug
    PROPERTIES
        PREFIX ""
        OUTPUT_NAME "X"
        LINKER_LANGUAGE C
)

target_include_directories(ueberzug PUBLIC
    ${PYTHON_INCLUDE_DIRS}
    ${PROJECT_SOURCE_DIR}/ueberzug/X
)

target_link_libraries(ueberzug X11::X11 PkgConfig::XEXT PkgConfig::XRES)
