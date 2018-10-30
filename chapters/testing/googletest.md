# GoogleTest

## Submodule method (preferred)

To use this method, just checkout GoogleTest as a submodule:[^1]

```cmake
git submodule add --branch=release-1.8.0 ../../google/googletest.git extern/googletest
```

Then, in your main `CMakeLists.txt`:

```cmake
option(PACKAGE_TESTS "Build the tests" ON)
if(PACKAGE_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()
```

I would recommend using something like `PROJECT_NAME STREQUAL CMAKE_PROJECT_NAME` to set the default for the option, since this should only build by default if this is the current project. As mentioned before, you have to do the `enable_testing` in your main CMakeLists. Now, in your tests directory:

```cmake
add_subdirectory("${PROJECT_SOURCE_DIR}/extern/googletest" "extern/googletest")
```

If you did this in your main CMakeLists, you could use a normal `add_subdirectory`; the extra path here is needed to correct the build path because we are calling it from a subdirectory.

The next line is optional, but keeps your `CACHE` cleaner:

```cmake
mark_as_advanced(
    BUILD_GMOCK BUILD_GTEST BUILD_SHARED_LIBS
    gmock_build_tests gtest_build_samples gtest_build_tests
    gtest_disable_pthreads gtest_force_shared_crt gtest_hide_internal_symbols
)
```

If you are interested in keeping IDEs that support folders clean, I would also add these lines:

```cmake
set_target_properties(gtest PROPERTIES FOLDER extern)
set_target_properties(gtest_main PROPERTIES FOLDER extern)
set_target_properties(gmock PROPERTIES FOLDER extern)
set_target_properties(gmock_main PROPERTIES FOLDER extern)
```

Then, to add a test, I'd recommend the following macro:

```cmake
macro(package_add_test TESTNAME)
    add_executable(${TESTNAME} ${ARGN})
    target_link_libraries(${TESTNAME} gtest gmock gtest_main)
    add_test(${TESTNAME} ${TESTNAME})
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

package_add_test(test1 test1.cpp)
```

This will allow you to quickly and simply add tests. Feel free to adjust to suit your needs. If you haven't seen it before, `ARGN` is "every argument after the listed ones".

## Download method

You can use the downloader in my [CMake helper repository][CLIUtils/cmake], using CMake's `include` command.

This is a downloader for [GoogleTest], based on the excellent [DownloadProject] tool. Downloading a copy for each project is the recommended way to use GoogleTest (so much so, in fact, that they have disabled the automatic CMake install target), so this respects that design decision. This method downloads the project at configure time, so that IDE's correctly find the libraries. Using it is simple:

```cmake
cmake_minimum_required(VERSION 3.4)
project(MyProject CXX)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

enable_testing() # Must be in main file

include(AddGoogleTest) # Could be in /tests/CMakeLists.txt
add_executable(SimpleTest SimpleTest.cu)
add_gtest(SimpleTest)
```

> Note: `add_gtest` is just a macro that adds `gtest`, `gmock`, and `gtest_main`, and then runs `add_test` to create a test with the same name:
> ```cmake
> target_link_libraries(SimpleTest gtest gmock gtest_main)
> add_test(SimpleTest SimpleTest)
> ```

## FetchContent: CMake 3.11

The example for the FetchContent module is GoogleTest:

```cmake
include(FetchContent)

FetchContent_Declare(
  googletest
  GIT_REPOSITORY https://github.com/google/googletest.git
  GIT_TAG        release-1.8.0
)

FetchContent_GetProperties(googletest)
if(NOT googletest_POPULATED)
  FetchContent_Populate(googletest)
  add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})
endif()
```

[^1]: Here I've assumed that you are working on a GitHub repository by using the relative path to googletest.


[CLIUtils/cmake]:  https://github.com/CLIUtils/cmake
[GoogleTest]:      https://github.com/google/googletest
[DownloadProject]: https://github.com/Crascit/DownloadProject
