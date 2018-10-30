
## [main]
cmake_minimum_required(VERSION 3.11)

project(RootSimpleExample311 LANGUAGES CXX)

find_package(ROOT CONFIG REQUIRED)

## [modern_fix]
target_include_directories(ROOT::Core INTERFACE "${ROOT_INCLUDE_DIRS}")

add_library(ROOT::Flags_CXX IMPORTED INTERFACE)

separate_arguments(ROOT_CXX_FLAGS)
target_compile_options(ROOT::Flags_CXX INTERFACE ${ROOT_CXX_FLAGS})

separate_arguments(ROOT_DEFINITIONS)
target_compile_definitions(ROOT::Flags_CXX INTERFACE ${ROOT_DEFINITIONS})

# This fixes a bug in the linker flags
string(REPLACE "-L " "-L" ROOT_EXE_LINKER_FLAGS "${ROOT_EXE_LINKER_FLAGS}")
separate_arguments(ROOT_EXE_LINKER_FLAGS)
# Stuck into using old property method due to separate -L and -l arguments
# (A full path to -l is better!)
set_property(TARGET ROOT::Flags_CXX PROPERTY
    INTERFACE_LINK_LIBRARIES ${ROOT_EXE_LINKER_FLAGS})
## [modern_fix]

add_executable(RootSimpleExample311 SimpleExample.cxx)
target_link_libraries(RootSimpleExample311 PUBLIC ROOT::Physics ROOT::Flags_CXX)
## [main]

enable_testing()
add_test(NAME RootSimpleExample311 COMMAND RootSimpleExample311)
