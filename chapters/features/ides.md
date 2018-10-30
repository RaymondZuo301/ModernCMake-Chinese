# Supporting IDEs

In general, IDEs are already supported by a standard CMake project. There are just a few extra things that can help IDEs perform even better.

## Folders

Some IDEs, like Xcode, support folders. You have to manually enable the `USE_FOLDERS` global property to allow CMake to organize your files by folders:

```cmake
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
```

Then, you can add targets to folders when after you create them:

```cmake
set_property(TARGET MyFile PROPERTY FOLDER "Scripts")
```

Folders can be nested with `/`. You can control how files show up in each folder with regular expressions or explicit listings in [`source_group`](https://cmake.org/cmake/help/latest/command/source_group.html):

```
source_group("Source Files" REGULAR_EXPRESSION ".*\\.c[ucp]p?")
```

Take a look at [this blog post][sorting] for tips on ways to automatically sort folders to match your source code layout.

## Running with an IDE

To use an IDE, either pass `-G"name of IDE" if CMake can produce that IDE's files (like Xcode, Visual Studio), or open the CMakeLists.txt file from your IDE if that IDE has built in support for CMake (CLion, QtCreator, many others).


[sorting]: http://blog.audio-tk.com/2015/09/01/sorting-source-files-and-projects-in-folders-with-cmake-and-visual-studioxcode/
