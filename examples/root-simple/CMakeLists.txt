
## [main]
cmake_minimum_required(VERSION 3.1)

project(RootSimpleExample LANGUAGES CXX)

## [find_package]
find_package(ROOT CONFIG REQUIRED)
## [find_package]

# ROOT targets are missing includes and flags
## [setup_includes]
set_property(TARGET ROOT::Core PROPERTY
    INTERFACE_INCLUDE_DIRECTORIES "${ROOT_INCLUDE_DIRS}")
## [setup_includes]

## [setup_flags]
add_library(ROOT::Flags_CXX IMPORTED INTERFACE)

# Fix for ROOT_CXX_FLAGS not actually being a CMake list
separate_arguments(ROOT_CXX_FLAGS)
set_property(TARGET ROOT::Flags_CXX APPEND PROPERTY
    INTERFACE_COMPILE_OPTIONS ${ROOT_CXX_FLAGS})

# Add definitions
separate_arguments(ROOT_DEFINITIONS)
foreach(_flag ${ROOT_EXE_LINKER_FLAG_LIST})
    # Remove -D or /D if present
    string(REGEX REPLACE [=[^[-//]D]=] "" _flag ${_flag})
    set_property(TARGET ROOT::Flags APPEND PROPERTY INTERFACE_LINK_LIBRARIES ${_flag})
endforeach()

# This also fixes a bug in the linker flags
string(REPLACE "-L " "-L" ROOT_EXE_LINKER_FLAGS "${ROOT_EXE_LINKER_FLAGS}")
separate_arguments(ROOT_EXE_LINKER_FLAGS)
set_property(TARGET ROOT::Flags_CXX APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES ${ROOT_EXE_LINKER_FLAGS})
## [setup_flags]

# Adding an exectuable program and linking to needed ROOT libraries
## [add_and_link]
add_executable(RootSimpleExample SimpleExample.cxx)
target_link_libraries(RootSimpleExample PUBLIC ROOT::Physics ROOT::Flags_CXX)
## [add_and_link]

## [main]

enable_testing()
add_test(NAME RootSimpleExample COMMAND RootSimpleExample)
