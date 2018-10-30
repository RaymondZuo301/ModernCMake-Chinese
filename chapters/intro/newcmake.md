# What's new in in CMake


This is an abbreviated version of the CMake changlog with just the highlights for authors. Names for each release are arbitrarily picked by the author.

## [CMake 3.0] : Interface libraries

There were a ton of additions to this version of CMake, primarily to fill out the target interface. Some bits of needed functionality were missed and implemented in CMake 3.1 instead.

* New documentation
* INTERFACE libraries
* Project VERSION support
* Exporting build trees easily
* Bracket arguments and comments available (not widely used)
* Lots of improvements

## [CMake 3.1] : C++11 and compile features

This is the first release of CMake to support C++11. Combined with fixes to the new features of CMake 3.0, this is currently a common minimum version of CMake for libraries that want to support old CMake builds.

* C++11 Support
* Compile features support
* Sources can be added later with `target_sources`
* Better support for generator expressions and INTERFACE targets

## [CMake 3.2] : UTF8

This is a smaller release, with mostly small features and fixes. Internal changes, like better Windows and UTF8 support, were the focus.

* `continue()` inside loops
* File and directory locks added

## [CMake 3.3] : if IN_LIST

This is notable for the useful `IN_LIST` option for if, but it also added better library search using `$PATH` (See CMake 3.6), dependencies for INTERFACE libraries, and several other useful improvements. The addition of a `COMPILE_LANGUAGE` generator expression would prove very useful in the future as more languages are added. Makefiles now produce better output in parallel.

* `IN_LIST` added to `if`
* `*_INCLUDE_WHAT_YOU_USE` property added
* `COMPILE_LANGUAGE` generator expression (limited support in some generators)

## [CMake 3.4] : Swift & CCache

This release adds lots of useful tools, support for the Swift language, and the usual improvements. It also started supporting compiler launchers, like CCache.

* Added `Swift` language
* Added `BASE_DIR` to `get_filename_component`
* `if(TEST ...)` added
* `string(APPEND ...)` added
* `CMAKE_*_COMPILER_LAUNCHER` added for make and ninja
* `TARGET_MESSAGES` allow makefiles to print messages after target is completed
* Imported targets are beginning to show up in the official `Find*.cmake` files

## [CMake 3.5] : ARM

This release expanded CMake to more platforms, and make warnings easier to control from the command line.

* Multiple input files supported for several of the `cmake -E` commands.
* `cmake_parse_arguments` now builtin
* Boost, GTest, and more now support imported targets
* ARMCC now supported, better support for iOS
* XCode backslash fix

## [CMake 3.6] : Clang-Tidy

This release added Clang-Tidy support, along with more utilities and improvements. It also removed the search of `$PATH` on Unix systems due to problems, instead users should use `$CMAKE_PREFIX_PATH`.

* `EXCLUDE_FROM_ALL` for install
* `list(FILTER` added
* `CMAKE_*_STANDARD_INCLUDE_DIRECTORIES` and `CMAKE_*_STANDARD_LIBRARIES` added for toolchains
* Try-compile improvements
* `*_CLANG_TIDY` property added
* External projects can now be shallow clones, and other improvements


## [CMake 3.7] : Android & CMake Server 

You can now cross-compile to Android. Useful new if statement options really help clarify code. And the new server mode should (eventually?) improve integration with IDEs. Support for the VIM editor was also improved.

* `PARSE_ARGV` mode for `cmake_parse_arguments`
* Better 32-bit support on 64-bit machines
* Lots of useful new if comparisons, like `VERSION_GREATER_EQUAL` (really, why did it take this long?)
* `LINK_WHAT_YOU_USE` added
* Lots of custom properties related to files and directories
* CMake Server added
* Added `--trace-source="filename"` to monitor certain files only


## [CMake 3.8] : C# & CUDA

This adds CUDA as a language, as well as `cxx_std_11` as a compiler meta-feature. The new generator expression could be really useful if you can require CMake 3.8+! 

* Native support for C# as a language
* Native support for CUDA as a language
* Meta features cxx_std_11 (and 14, 17) added
* `try_compile` has better language support
* `BUILD_RPATH` property added
* `COMPILE_FLAGS` now supports generator expression
* `*_CPPLINT` added
* `$<IF:cond,true-value,false-value>` added (wow!)

## [CMake 3.9] : IPO

Lots of fixes to CUDA support went into this release, including `PTX` support and MSVC generators. Interprocedural Optimizations are now supported properly.
Even more modules provide imported targets, including MPI.

* CUDA supported for Windows
* Better object library support in several situations
* `DESCRIPTION` added to `project`
* `separate_arguments` gets `NATIVE_COMMAND`
* `INTERPROCEDURAL_OPTIMIZATION` enforced (and `CMAKE_*` initializer added, CheckIPOSupported added, Clang and GCC support)
* New `GoogleTest` module
* `FindDoxygen` drastically improved


## [CMake 3.10] : CppCheck

CMake now is built with C++11 compilers. Lots of useful improvements help write cleaner code.

* Support for flang Fortran compiler
* Compiler launcher added to CUDA
* Indented `#cmakedefines` now supported for `configure_file`
* `include_guard()` added to ensure a file gets included only once
* `string(PREPEND` added
* `*_CPPCHECK` property added
* `LABELS` added to directories
* FindMPI vastly expanded
* FindOpenMP improved
* Dynamic test discovery for `GoogleTest`

## [CMake 3.11] : Faster & IMPORTED INTERFACE

This release is [supposed to be][fastercmake] much faster. You can also finally directly add INTERFACE targets
to IMPORTED libraries (the internal `Find*.cmake` scripts should become much cleaner eventually).

* Fortran supports compiler launchers
* Xcode and Visual Studio support `COMPILE_LANGUAGE` generator expressions finally
* You can now add INTERFACE targets directly to IMPORTED INTERFACE libraries (Wow!)
* Source file properties have been expanded
* `FetchContent` module now allows downloads to happen at configure time (Wow)

## [CMake 3.12] : Version ranges and CONFIGURE_DEPENDS

Very powerful release, containing lots of smaller long-requested features. One of the smaller
but immediately noticeable changes is the addition of version ranges;
you can now set both the minimum and maximum known CMake version easily. You can also set
`CONFIGURE_DEPENDS` on a `GLOB`ed set of files, and the build system will check those files and
rerun if needed! You can use the general `PackageName_ROOT` 
for all `find_package` searches. Lots of additions to strings and lists, module updates,
shiny new Python find module (2 and 3 versions too), and many more.

* Support for `cmake_minimum_required` ranges (backward compatible)
* Support for `-j,--parallel` in `--build` mode (passed on to build tool)
* Support for `$<SHELL:` shell strings (not deduplicated)
* New FindPython module
* `string(JOIN` and `list(JOIN`, and `list(TRANSFORM`
* `file(TOUCH` and `file(GLOB CONFIGURE_DEPENDS`
* C++20 support
* CUDA as a language improvements: CUDA < 7.5 supported
* Support for OpenMP on macOS (command line only)
* Several new properties and property initializers
* CPack finally reads `CMAKE_PROJECT_VERSION` variables

## [CMake 3.13 rc1] : Linking control

You can now make symbolic links on Windows! Lots of new functions that fill out the
popular requests for CMake, such as `add_link_options`, `target_link_directories`, and
`target_link_options`. You can now do quite a bit more modification to targets outside
of the source directory, for better file separation. Generator expressions can be used in
more places. And, `target_sources` *finally* handles relative paths properly.

* New `ctest --progress` option for live output
* `target_link_options` and `add_link_options` added
* `target_link_directories` added
* Symbolic link creation, `-E create_symlink`, supported on Windows
* IPO supported on Windows
* You can use `-S` and `-B` for source and build directories
* `target_link_libraries` and `install` work outside the current target directory
* `STATIC_LIBRARY_OPTIONS` property added
* `target_sources` is now relative to the current source directory

[Releases]: https://cmake.org/cmake/help/latest/release/index.html
[CMake 3.0]: https://cmake.org/cmake/help/latest/release/3.0.html 
[CMake 3.1]: https://cmake.org/cmake/help/latest/release/3.1.html 
[CMake 3.2]: https://cmake.org/cmake/help/latest/release/3.2.html 
[CMake 3.3]: https://cmake.org/cmake/help/latest/release/3.3.html 
[CMake 3.4]: https://cmake.org/cmake/help/latest/release/3.4.html 
[CMake 3.5]: https://cmake.org/cmake/help/latest/release/3.5.html 
[CMake 3.6]: https://cmake.org/cmake/help/latest/release/3.6.html 
[CMake 3.7]: https://cmake.org/cmake/help/latest/release/3.7.html 
[CMake 3.8]: https://cmake.org/cmake/help/latest/release/3.8.html 
[CMake 3.9]: https://cmake.org/cmake/help/latest/release/3.9.html 
[CMake 3.10]: https://cmake.org/cmake/help/latest/release/3.10.html 
[CMake 3.11]: https://cmake.org/cmake/help/latest/release/3.11.html 
[CMake 3.12]: https://cmake.org/cmake/help/latest/release/3.12.html 
[CMake 3.13 rc1]: https://blog.kitware.com/cmake-3-13-0-rc1-is-ready-for-testing/ 
[fastercmake]: https://blog.kitware.com/improving-cmakes-runtime-performance/
