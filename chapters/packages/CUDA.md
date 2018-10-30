# CUDA (in progress)

CUDA support is available in two flavors. The new method, introduced in CMake 3.8 (3.9 for Windows), will be what I focus on first. The old method will be covered afterwards, but as you'll see, it's uglier and harder to get right. I'd stick to requiring CMake 3.8 or 3.9 for CUDA (and CMake 3.11 for IDEs like Xcode and Visual Studio).

A good resource for CUDA and Modern CMake is [this talk](http://on-demand.gputechconf.com/gtc/2017/presentation/S7438-robert-maynard-build-systems-combining-cuda-and-machine-learning.pdf) by CMake developer Robert Maynard at GTC 2017.

## Method 1: CUDA as a First Class Language


This method is quite new, and doesn't seem to have much documentation. There are several issues you need to watch out for when using it, but overall is should be a much nicer and cleaner way to use CUDA.

### Adding the CUDA Language

There are two ways to enable CUDA support. If CUDA is not optional:

```cmake
project(MY_PROJECT LANGUAGES CUDA CXX)
```

You'll probably want `CXX` listed here also. And, if CUDA is optional, you'll want to put this in somewhere conditionally:

```cmake
enable_language(CUDA)
```

To check to see if CUDA is available, use CheckLanuage:

```cmake
include(CheckLanguage)
check_language(CUDA)
```

You can check the version of the NVCC toolkit with `CMAKE_CUDA_COMPILER_VERSION` (for now, only NVCC is supported, but just to be sure, check `CMAKE_CUDA_COMPILER_ID STREQUAL "NVIDIA"`).

### Variables for CUDA

Many variables with `CXX` in the name have a CUDA version with `CUDA` instead. For example, to set the C++ standard required for CUDA,

```
if(NOT DEFINED CMAKE_CUDA_STANDARD)
    set(CMAKE_CUDA_STANDARD 11)
    set(CMAKE_CUDA_STANDARD_REQUIRED ON)
endif()
```


### Adding a library

This is the easy part; as long as you use `.cu` for CUDA files, you can just add libraries like you normally would.

You can also use separable compilation:

```cmake
set_target_properties(mylib PROPERTIES
                            CUDA_SEPERABLE_COMPILATION ON)
```

You can also direclty make a PTX file with the `CUDA_PTX_COMPILATION` property.

### Working with targets

Using targets should work similarly to CXX, but there's a problem. If you include a target that includes compiler options (flags), most of the time, the options will not be protected by the correct includes (and the chances of them having the correct CUDA wrapper is even smaller). Here's what a correct compiler options line should look like:

```cmake
"$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CXX>>:-fopenmp>$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CUDA>>:-Xcompiler=-fopenmp>"
```

However, if you using almost any find_package, and using the Modern CMake methods of targets and inheritance, everything will break. I've learned that the hard way.

For now, here's a pretty reasonable solution, _as long as you know the un-aliased target name_. It's a function that will fix a C++ only target by wrapping the flags if using a CUDA compiler:

```cmake
function(CUDA_CONVERT_FLAGS EXISTING_TARGET)
    get_property(old_flags TARGET ${EXISTING_TARGET} PROPERTY INTERFACE_COMPILE_OPTIONS)
    if(NOT "${old_flags}" STREQUAL "")
        string(REPLACE ";" "," CUDA_flags "${old_flags}")
        set_property(TARGET ${EXISTING_TARGET} PROPERTY INTERFACE_COMPILE_OPTIONS
            "$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CXX>>:${old_flags}>$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CUDA>>:-Xcompiler=${CUDA_flags}>"
            )
    endif()
endfunction()
```

### Useful variables

* `CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES`: Place for built-in Thrust, etc
* `CMAKE_CUDA_COMPILER`: NVCC with location

> ### Note that FindCUDA is deprecated, but for now, the following functions require FindCUDA:
> 
> * CUDA version checks / picking a version
> * Architecture detection (Note: 3.12 will fix this at least partially)
> * Linking to CUDA libraries from non-.cu files

## Method 2: FindCUDA

If you want to support an older version of CMake, I recommend at least including the FindCUDA from CMake version 3.9 in your cmake folder (see the CLIUtils github organization for a [git repository](https://github.com/CLIUtils/cuda_support)). You'll want two features that were added: `CUDA_LINK_LIBRARIES_KEYWORD` and `cuda_select_nvcc_arch_flags`, along with the newer architectures and CUDA versions.

To use the old CUDA support, you use `find_package`:

```cmake
find_package(CUDA 7.0 REQUIRED)
message(STATUS "Found CUDA ${CUDA_VERSION_STRING} at ${CUDA_TOOLKIT_ROOT_DIR}")
```

You can control the CUDA flags with `CUDA_NVCC_FLAGS` (list append) and you can control separable compilation with `CUDA_SEPARABLE_COMPILATION`. You'll also want to make sure CUDA plays nice and adds keywords to the targets (CMake 3.9+):

```cmake
set(CUDA_LINK_LIBRARIES_KEYWORD PUBLIC)
```

You'll also might want to allow a user to check for the arch flags of their current hardware:

```cmake
cuda_select_nvcc_arch_flags(ARCH_FLAGS) # optional argument for arch to add
```


