# This is a `CMakeLists.txt` file to configure a CMake project for
# Qt5 Framework's Library.

# Minimum CMake Version needed to process this `CMakeLists.txt` file.
cmake_minimum_required(VERSION 3.6)

# The "Project Name" that CMake uses to identify our project.
# This value is stored in `CMAKE_PROJECT_NAME` variable for further use.
project(sapcon-terminal)

# Tells CMake to generate compiler options appropriate for C++14.
set(CMAKE_CXX_STANDARD 14)

# Tells CMake to include current directory path in includes path.
# In CLion this is not needed very often as CLion itself starts CMake in a
# reasonable directory (base sourse directory) but generaly to avoid some
# misconfigurations of generated `Makefile` this option is recommended in
# Qt projects.
set(CMAKE_INCLUDE_CURRENT_DIR ON)
# Tells CMake to run `moc` any time needed.
# `MOC` is stands for Meta-Object Compiler and is a Qt framework's utility
# needed to have clean and correct definitions for signals and slots in any
# Qt class/object.
# CMake intelligently runs `moc` on just files that used any Qt MOC Macros.
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)

# Sets a custom variable named `Qt5_BASE_DIR` that we could use further to have
# a modular import of Qt Framework's Libraries.
set(Qt5_SYSTEMWIDE /opt/Qt5.8.0/5.8/gcc_64)
set(Qt5_LOCAL $ENV{HOME}/Qt5.8.0/5.8/gcc_64)
message("Testing against Qt")
if(EXISTS ${Qt5_SYSTEMWIDE})
    message("System-wide Qt found!")
    set(Qt5_BASE_DIR ${Qt5_SYSTEMWIDE})
elseif(EXISTS ${Qt5_LOCAL})
    message("Local Qt found!")
    set(Qt5_BASE_DIR ${Qt5_LOCAL})
else()
    message("No user installed Qt5 found!")
endif()

# This variable tells CMake to create appropriate library path, binary path and
# packages path to find and configure anything that told CMake to use (e.g.
# `find_package` or `find_library` directives)
if(DEFINED Qt5_BASE_DIR)
    message("Adjust CMAKE_PREFIX_PATH")
    set(CMAKE_PREFIX_PATH ${Qt5_BASE_DIR};${CMAKE_PREFIX_PATH})
endif()

# This variable is used to avoid repeating names of source file everywhere we
# need them.
set(SOURCE_FILES 
  main.cpp
  mainwindow.cpp
  answer_box.cpp
  quary_box.cpp
  serialcomm.cpp
)
#include_directory(${CMAKE_CURRENT_SOURCE_DIR})
# `find_package` directive tells CMake to search for given package name and
# applies appropriate configurations for that package.
# The informations needed by CMake to properly configure our `Makefile` for this
# package must be in a file named `<package>Config.cmake` or
# `<package>-config.cmake` in CMake search path or `<package>_DIR` variable.
# `REQUIRED` keyword tells CMake this package is required to properly configure
# and compile our project.
# `COMPONENTS` directive tells we need which of components of very package. CMake
# starts searching for configurations for these components in the package cmakefile.
# The very syntax
# (`find_package(<package> REQUIRED COMPONENTS <component_1> <component_2> ... )`)
# against using `find_package` for each component lets us to avoid worrying
# about each component's cmakefile path and have all of them configured in just
# one line.
find_package(Qt5 REQUIRED COMPONENTS Core Widgets Gui SerialPort)

# `add_executable` directive tells CMake to create a target in generated `Makefile`
# that compiles sources given to it and generates an executable named as target
# name.
# As `add_executabe` we also have `add_library` with the same logic just except
# generating an executable, its tells CMake to generate a target appropriate for
# compilation of a library. (librarie's compilations are differ in directives and
# options that must be passed to compiler from an executable's compilations)
add_executable(${CMAKE_PROJECT_NAME} ${SOURCE_FILES})

# Finally we need to tell CMake how to configure linker (here, `ld`).
# Linker needs to know which libraries are used to find address of symbols and
# generates a working binary file that linked properly to libraries used.
# Here, this directive generates needed linker options as `-l<libname>` switchs
# to tell linker which libraries are used.
target_link_libraries(${CMAKE_PROJECT_NAME} Qt5::Core Qt5::Widgets Qt5::Gui Qt::SerialPort)
