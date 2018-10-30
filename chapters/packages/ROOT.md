# ROOT

ROOT is a C++ Toolkit for High Energy Physics. It is huge. There are really a lot of ways to use it in CMake, though many/most of the examples you'll find are probably wrong. Here's my recommendation.

Most importantly, there are lots of improvements in CMake in more recent versions of ROOT - try to use 6.14+!

## Finding ROOT

ROOT 6.10+ supports config file discovery, so you can just do:

[import:'find_package', lang:'cmake'](../../examples/root-simple/CMakeLists.txt)

to attempt to find ROOT. If you don't have your paths set up, you can pass `-DROOT_DIR=$ROOTSYS/cmake` to find ROOT. (But, really, you should source `thisroot.sh`).

## The too-simple way

ROOT [provides a utility](https://root.cern.ch/how/integrate-root-my-project-cmake) to set up a ROOT project, which you can activate using `include("${ROOT_USE_FILE}")`. This will automatically make ugly directory level and global variables for you. It will save you a little time setting up, and will waste massive amounts of time later if you try to do anything tricky. As long as you aren't making a library, it's probably fine for simple scripts. Includes and flags are set globally, but you'll still need to link to `${ROOT_LIBRARIES}` yourself, along with possibly `ROOT_EXE_LINKER_FLAGS` (You will have to `separate_arguments` first before linking or you will get an error if there are multiple flags, like on macOS).

Here's what it would look like:

[import:'main', lang:'cmake'](../../examples/root-usefile/CMakeLists.txt)

## The right way (Targets)

ROOT 6.12 and earlier do not add the include directory for imported targets. ROOT 6.14+ has corrected this error. To fix this error for older ROOT versions, you'll need something like:

[import:'setup_includes', lang:'cmake'](../../examples/root-simple/CMakeLists.txt)

You will also often want the compile flags and definitions:

[import:'setup_flags', lang:'cmake'](../../examples/root-simple/CMakeLists.txt)

In CMake 3.11, you can replace that last function call with:

[import:'modern_fix', lang:'cmake'](../../examples/root-simple-3.11/CMakeLists.txt)

All the ROOT targets will require `ROOT::Core`, so this will be enough regardless of which ROOT targets you need.

To link, just pick the libraries you want to use:

[import:'add_and_link', lang:'cmake'](../../examples/root-simple/CMakeLists.txt)

## Components

Find ROOT allows you to specify components. It will add anything you list to ${ROOT_LIBRARIES}, so you might want to build your own target using that to avoid listing the components twice. This does not solve dependencies though; so it is an error to list `RooFit` but not `RooFitCore`. If you link to `ROOT::RooFit` instead of `${ROOT_LIBRARIES}`, then `RooFitCore` is not required.

## Dictionary generation

Dictionary generation is ROOT's way of working around the missing reflection feature in C++. It allows ROOT to learn the details of your class so it can save it, show methods in the Cling interpreter, etc. You'll need three things in your source code to make it work for classes:

* Your class definition should end with `ClassDef(MyClassName, 1)`
* Your class implementation should have `ClassImp(MyClassName)` in it
* You should have a file with a name that ends with `LinkDef.h`

The `LinkDef.h` file follows a [specific formula][linkdef-root] and tells ROOT what parts to generate dictionaries for.

To generate, you should include the following in your CMakeLists:

```cmake
include("${ROOT_DIR}/modules/RootNewMacros.cmake")
include_directories(ROOT_BUG)
```

The second line is due to a bug in the NewMacros file that causes dictionary generation to fail if there is not at least one global include directory or a `inc` folder. Here I'm including a non-existent directory just to make it work. There is no `ROOT_BUG` directory.

To generate a file:

```cmake
root_generate_dictionary(G__Example Example.h LINKDEF ExampleLinkDef.h)
```

The final argument, listed after `LINKDEF`, must have a name that ends in `LinkDef.h`. This command will create three files. If you started output name with `G__`, that will be removed from the name, otherwise it will use the name given; this must match the final output library name you will soon be creating. Assuming this is `${NAME}`:

* `${NAME}.cxx`: This file should be included in your sources when you make the library.
* `lib{NAME}.rootmap` (`G__` prefix removed): The rootmap file in plain text
* `lib{NAME}_rdict.pcm` (`G__` prefix removed): A ROOT file

The final two output files must sit next to the library output. This is done by checking `CMAKE_LIBRARY_OUTPUT_DIRECTORY` (it will not pick up local target settings). If you have a libdir set but you don't have (global) install locations set, you'll also need to set `ARG_NOINSTALL` to `TRUE`. 

[linkdef-root]: https://root.cern.ch/selecting-dictionary-entries-linkdefh

