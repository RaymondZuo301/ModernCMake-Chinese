
## [main]
cmake_minimum_required(VERSION 3.1)

project(RootUseFileExample LANGUAGES CXX)

find_package(ROOT CONFIG REQUIRED)

include("${ROOT_USE_FILE}")

# This is required on if there is more than one flag (like on macOS)
# and this also fixes a bug in the linker flags
string(REPLACE "-L " "-L" ROOT_EXE_LINKER_FLAGS "${ROOT_EXE_LINKER_FLAGS}")
separate_arguments(ROOT_EXE_LINKER_FLAGS)

add_executable(RootUseFileExample SimpleExample.cxx)
target_link_libraries(RootUseFileExample PUBLIC ${ROOT_LIBRARIES} ${ROOT_EXE_LINKER_FLAGS})

## [main]

enable_testing()
add_test(NAME RootUseFileExample COMMAND RootUseFileExample)
