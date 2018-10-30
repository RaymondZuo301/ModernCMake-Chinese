# Testing

## General Testing Information

In your main CMakeLists.txt you need to add the following function call (not in a subfolder):

```cmake
enable_testing()
```

You can register targets with:

```cmake
add_test(NAME TestName COMMAND TargetName)
```

If you put something else besides a target name after COMMAND, it will register as a command line to run. It would also be valid to put the generator expression:

```cmake
add_test(NAME TestName COMMAND $<TARGET_FILE:${TESTNAME}>)
```

which would use the output location (thus, the executable) of the produced target.


## Building as part of a test

If you want to run CMake to build a project as part of a test, you can do that too (in fact, this is how CMake tests itself). For example, if your master project was called `MyProject` and you had an `examples/simple` project that could build by itself, this would look like:

```cmake
add_test(
  NAME
    ExampleCMakeBuild
  COMMAND
    "${CMAKE_CTEST_COMMAND}"
             --build-and-test "${My_SOURCE_DIR}/examples/simple"
                              "${CMAKE_CURRENT_BINARY_DIR}/simple"
             --build-generator "${CMAKE_GENERATOR}"
             --test-command "${CMAKE_CTEST_COMMAND}"
)
```

## Testing Frameworks

Look at the subchapters for recipes for popular frameworks.
