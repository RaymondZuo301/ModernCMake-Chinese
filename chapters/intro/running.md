# 运行CMake

# Running CMake

在编写CMake之前，让我们确保你知道如何运行它来制作东西。几乎所有CMake项目都是如此。

Before writing CMake, let's make sure you know how to run it to make things. This is true for almost all CMake projects, which is almost everything.

## 新建一个工程

## Building a project

除非另有说明，否则您应该始终创建一个构建目录并从那里构建。你可以从技术上进行in-source构建，但你必须小心不要覆盖文件或将它们添加到git，所以还是不要这样做。

这是CMake的构建方式：

```bash
~/package $ mkdir build
~/package $ cd build
~/package/build $ cmake ..
~/package/build $ make
```

你可以根据需要将`make`更换为`cmake --build .`，它会调用`make`或你正在使用的构建工具。您你想安装它你可以用`make install`或`cmake --build . --target install`。

Unless otherwise noted, you should always make a build directory and build from there. You can technically do an in-source build, but you'll have to be careful not to overwrite files or add them to git, so just don't.

Here's the CMake Build Procedure (TM):

{% term %}
~/package $ mkdir build
~/package $ cd build
~/package/build $ cmake ..
~/package/build $ make
{% endterm %}

You can replace the make line with `cmake --build .` if you'd like, and it will call `make` or whatever build tool you are using. You can follow it by `make install` or `cmake --build . --target install` if you want to install it.

## 选择一个编译器

## Picking a compiler

选择编译器必须在第一次运行时在空目录中完成。它本身不是CMake语法，但您可能不熟悉它。选择Clang作为编译器：

```bash
~/package/build $ CC=clang CXX=clang++ cmake ..
```

这为`CC`和`CXX`设置了bash的环境变量，CMake将遵循这些变量。该设置只对本条命令生效，但你也只需要做这一次；之后，CMake继续使用从这些值中推导出的路径。

Selecting a compiler must be done on the first run in an empty directory. It's not CMake syntax per se, but you might not be familiar with it. To pick Clang:

{% term %}
~/package/build $ CC=clang CXX=clang++ cmake ..
{% endterm %}

That sets the environment variables in bash for CC and CXX, and CMake will respect those variables. This sets it just for that one line, but that's the only time you'll need those; afterwards CMake continues to use the paths it deduces from those values.

## 选择一个生成器

## Picking a generator

您可以使用各种工具进行构建，`make`通常是默认值。要查看您的系统上CMake支持的所有工具，请运行：

```bash
~/package/build $ cmake --help
```

您可以通过`-G "My Tool"`（只有在工具名称中有空格时才需要引号）选择一个生成工具。您应该像编译器一样，第一次在目录中调用CMake时选择一个工具。你可以同事拥有几个构建目录，比如`build/`和`buildXcode/`。

You can build with a variety of tools; `make` is usually the default. To see all the tools CMake knows about on your system, run

{% term %}
~/package/build $ cmake --help
{% endterm %}

And you can pick a tool with `-G"My Tool"` (quotes only needed if spaces are in the tool name). You should pick a tool on your first CMake call in a directory, just like the compiler. Feel free to have several build directories, like `build/` and `buildXcode`.

## 设定选项

## Setting options

您可以在CMake中使用`-D`来设置选项。您可以通过`-L`查看选项列表，或者通过`-LH`查看一个可读性好的列表。如果您没有列出`source/build`目录，则列表将不会重新运行CMake（`cmake -L`而不是`cmake -L .`）。

You set options in CMake with `-D`. You can see a list of options with `-L`, or a list with human-readable help with `-LH`. If you don't list the source/build directory, the listing will not rerun CMake (`cmake -L` instead of `cmake -L .`).

## Verbose和partial构建

## Verbose and partial builds

再说一次，这种做法很不“CMake”，但是如果你使用命令行构建工具`make`，你可以进行Verbose构建：

```bash
~/package/build $ VERBOSE=1 make
```

实际上你可以写`make VERBOSE=1`，并且make也会做正确的事，虽然这是`make`的一个功能但不是一个通常的命令。

您还可以通过指定目标来构建一部分，例如您在CMake中定义的lib或可执行文件的名称，make将只构建该目标。

Again, not really CMake, but if you are using a command line build tool like `make`, you can get verbose builds:

{% term %}
~/package/build $ VERBOSE=1 make
{% endterm %}

You can actually write `make VERBOSE=1`, and make will also do the right thing, though that's a feature of `make` and not the command line in general.

You can also build just a part of a build by specifying a target, such as the name of a library or executable you've defined in CMake, and make will just build that target.

## 标准选项

## Standard options

这些是大多数软件包的常见CMake选项：

* `-DCMAKE_BUILD_TYPE=` 从`Release`，`RelWithDebInfo`和`Debug`中选择，有时有更多选项。
* `-DCMAKE_INSTALL_PREFIX=`要安装的位置。UNIX上的系统安装路径通常是`/usr/local`（默认），用户目录经常是`~/.local`，或者您可以选择一个文件夹。
* `-D BUILD_SHARED_LIBS=`您可以设置此项`ON`或`OFF`控制共享库的默认值（作者可以显式选择一个而不是使用默认值）


These are common CMake options to most packages:

* `-DCMAKE_BUILD_TYPE=` Pick from Release, RelWithDebInfo, Debug, or sometimes more.
* `-DCMAKE_INSTALL_PREFIX=` The location to install to. System install on UNIX would often be `/usr/local` (the default), user directories are often `~/.local`, or you can pick a folder.
* `-D BUILD_SHARED_LIBS=` You can set this `ON` or `OFF` to control the default for shared libraries (the author can pick one vs. the other explicitly instead of using the default, though)

## 调试CMake文件

## Debugging your CMake files

我们已经提到了构建的verbose输出，但您也可以看到CMake配置的verbose输出。`--trace`选项将打印运行的每一行CMake。由于这非常详细，因此CMake 3.7添加了`--trace-source="filename"`，它将打印出运行时感兴趣的文件的每个执行语句。如果您选择要调试的文件的名称（如果您正在调试CMakeLists.txt，通常使用父目录，因为所有这些都具有相同的名称），您只能看到在该文件中运行的语句。很有用！

We've already mentioned verbose output for the build, but you can also see verbose CMake configure output too. The `--trace` option will print every line of CMake that is run. Since this is very verbose, CMake 3.7 added `--trace-source="filename"`, which will print out every executed line of just the file you are interested in when it runs. If you select the name of the file you are interested in debugging (usually with a parent directory if you are debugging a CMakeLists.txt, since all of those have the same name), you can just see the lines that run in that file. Very useful!

