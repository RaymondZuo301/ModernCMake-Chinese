# Exporting

There are two ways to access a project from another project. First is exporting targets and using them in the build directory. It usually is simply a by-product of setting up the third method (installing), but is useful for development and as a way to prepare to discuss the installation procedure.

First, you should make an export set, probably near the end of your main `CMakeLists.txt`:

```cmake
export(TARGETS MyLib1 MyLib2 NAMESPACE MyLib:: FILE MyLibTargets.cmake)
```

This puts the targets you've listed into a file in the build directory, and optionally prefixes them with a namespace. Now, to allow CMake to find this package, export the package into the `$HOME/.cmake/packages` folder:

```cmake
export(PACKAGE MyLib)
```

Now, if you `find_package(MyLib)`, CMake can find the build folder. Look at the generated MyLibTargets.cmake file to help you understand exactly what is created; it's just a normal CMake file, with the exported targets.

Note that there's a downside: if you have imported dependencies, they will need to be imported before you `find_package`. That will be fixed in the next method.

