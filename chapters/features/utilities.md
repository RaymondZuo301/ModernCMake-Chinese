# CCache and Utilities

Over the versions, common utilities that help you write good code have had support added to CMake. This is usually in the form of a property and matching `CMAKE_*` initialization variable. The feature is not meant to be tied to one special program, but rather any program that is somewhat similar in behavior.

All of these take `;` separated values (a standard list in CMake) that describe the program and options that you should run on the source files of this target.

## CCache

Set the `CMAKE_<LANG>_COMPILER_LAUNCHER` variable or the `<LANG>_COMPILER_LAUNCHER` property on a target to use something like CCache to "wrap" the compilation of the target. Support for CCache has been expanding in the latest versions of CMake. In practice, this tends to look like this:


```cmake
find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
    set(CMAKE_CXX_COMPILER_LAUNCHER "${CCACHE_PROGRAM}")
    set(CMAKE_CUDA_COMPILER_LAUNCHER "${CCACHE_PROGRAM}") # CMake 3.9+
endif()
```


## Utilities

Set the following properties or `CMAKE_*` initializer variables to the command line for the tools. Most of them are limited to C or CXX with make or ninja generators.

* `<LANG>_CLANG_TIDY`: CMake 3.6+
* `<LANG>_CPPCHECK`
* `<LANG>_CPPLINT`
* `<LANG>_INCLUDE_WHAT_YOU_USE`

## Clang tidy

Here is a simple example of using Clang-Tidy:

```cmake
if(CMAKE_VERSION VERSION_GREATER 3.6)
    # Add clang-tidy if available
    option(CLANG_TIDY_FIX "Perform fixes for Clang-Tidy" OFF)
    find_program(
        CLANG_TIDY_EXE
        NAMES "clang-tidy"
        DOC "Path to clang-tidy executable"
    )

    if(CLANG_TIDY_EXE)
        if(CLANG_TIDY_FIX)
            set(CMAKE_CXX_CLANG_TIDY "${CLANG_TIDY_EXE}" "-fix")
        else()
            set(CMAKE_CXX_CLANG_TIDY "${CLANG_TIDY_EXE}")
        endif()
    endif()
endif()
```

The `-fix` part is optional, and will modify your source files to try to fix the tidy warning issued. If you are working in a git repository, this is fairly safe as you can see what has changed. However, make sure you **do not run your makefile/ninja build in parallel**! This will not work very well at all if it tries to fix the same header twice.

If you want to explicitly use the target form to ensure you only call this on your local targets, you can set a variable (I usually chose `DO_CLANG_TIDY`) instead of the `CMAKE_CXX_CLANG_TIDY` variable, then add it to your target properties as you create them.

## Include what you use

This is an example for using include what you use. First, you'll need to have the tool, such as in a docker container:

```term
gitbook $ docker run --rm -it tuxity/include-what-you-use:clang_4.0
```

Then, you can pass this into your build without modifying the source:

```term
build # cmake .. -DCMAKE_CXX_INCLUDE_WHAT_YOU_USE=include-what-you-use
```

Finally, you can collect the output and apply the fixes:

```term
build # make 2> iwyu.out
build # fix_includes.py < iwyu.out
```

## Link what you use

There is a boolean target property, `LINK_WHAT_YOU_USE`, that will check for extraneous files when linking.

## Clang-format

Clang-format doesn't really have an integration with CMake, unfortunately. You could make a custom target (See [this post](https://arcanis.me/en/2015/10/17/cppcheck-and-clang-format), or you can run it manually. An interesting project that I have not really tried is [here](https://github.com/kbenzie/git-cmake-format); it adds a format target and even makes sure that you can't commit unformatted files.

The following two line would do that in a git repository in bash (assuming you have a `.clang-format` file):

```term
gitbook $ git ls-files -- '*.cpp' '*.h' | xargs clang-format -i -style=file
gitbook $ git diff --exit-code --color
```
