# Catch


Catch and [Catch2] (C++11 only version) are powerful, idomatic testing solutions similar in philosophy to PyTest for Python. To use Catch in a CMake project, there are several options.

## Vendoring

If you simply drop in the single include release of Catch into your project, this is what you would need to add Catch:


```cmake
# Prepare "Catch" library for other executables
set(CATCH_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/extern/catch)
add_library(Catch2::Catch IMPORTED INTERFACE)
set_property(Catch2::Catch PROPERTY INTERFACE_INCLUDE_DIRECTORIES "${CATCH_INCLUDE_DIR}")
```

Then, you would link to Catch2::Catch. This would have been okay as an INTERFACE target since you won't be exporting your tests.


## Direct inclusion

If you add the library using ExternalProject, FetchContent, or git submodules, you can also `add_subdirectory` Catch (CMake 3.1+).

Catch also provides two CMake modules that you can use to register the individual tests with CMake.

[Catch2]: https://github.com/catchorg/Catch2
