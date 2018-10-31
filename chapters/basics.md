# 基础知识简介

## 最低版本要求

以下是每个CMakeLists.txt（CMake会专门寻找这个文件名）的第一行代码：

Here's the first line of every CMakeLists.txt, which is the required name of the file CMake looks for:

```cmake
cmake_minimum_required(VERSION 3.1)
```

让我们来提一下CMake的语法。这行命令«command:`cmake_minimum_required`»不区分大小写，所以通常的做法是用小写[^1] 。`VERSION`是这个函数的特殊关键词，版本号的数值位于其后。可以单击本书各处的命令名称超链接以阅读官方文档，并可以在官方页面的下拉菜单中切换CMake版本。

Let's mention a bit of CMake syntax. The command name «command:`cmake_minimum_required`» is case insensitive, so the common practice is to use lower case. [^1] The `VERSION` is a special keyword for this function. And the value of the version follows the keyword. Like everywhere in this book, just click on the command name to see the official documentation, and use the dropdown to switch documentation between CMake versions.

这一行很特殊[^2]！`VERSION`还将用来规定CMake遵循的行为策略（policies)。所以如果你将«command:`cmake_minimum_required`»设定为`VERSION 2.8`，你将在macOS上遇到错误的链接行为，即使使用了最新的CMake版本。如果将其设定为3.3或更低，你将会遇到错误的隐藏符号表（hidden symbols）行为，等等。可以在«cmake:policies»查找策略和版本的列表。

This line is special! [^2] The version of CMake will also dictate the policies, which define behavior changes. So, if you set `minimum_required`to `VERSION 2.8`, you'll get the wrong linking behavior on macOS, for example, even in the newest CMake versions. If you set it to 3.3 or less, you'll get the wrong hidden symbols behaviour, etc. A list of policies and versions is available at «cmake:policies».

在CMake 3.12中，会支持类似于`VERSION 3.1...3.12`的版本范围；这意味着你支持像3.1这样的低版本，但也使用高达3.12的新策略设置对其进行了测试。对于需要更好设置的用户来说要好得多，并且由于语法上的技巧，它向后兼容旧版本的CMake（尽管实际运行CMake 3.2-3.11只会在此示例中设置3.1版本的策略） 。新版本的策略对于macOS和Windows用户来说往往是最重要的，他们通常也拥有最新版本的CMake。

以后建立新工程可以使用：

In CMake 3.12, this will support a range, such as `VERSION 3.1...3.12`; this means you support as low as 3.1 but have also tested it with the new policy settings up to 3.12. This is much nicer on users that need the better settings, and due to a trick in the syntax, it's backward compatible with older versions of CMake (though actually running CMake 3.2-3.11 will only set the 3.1 version of the policies in this example). New versions of policies tend to be most important for macOS and Windows users, who also
usually have a very recent version of CMake.

This is what new projects should do:

```cmake
cmake_minimum_required(VERSION 3.1...3.12)

if(${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif()
```

如果CMake版本小于3.12，则if将为true，并将策略设置为当前版本。如果CMake为3.12或更高，if块将为false，但新语法cmake_minimum_required仍将适用并正常工作！

警告：MSVC的CMake服务器模式似乎在读取此格式时存在[错误](https://github.com/fmtlib/fmt/issues/809) ，因此如果您需要支持非命令行Windows版本，则需要执行以下操作：

If CMake version is less than 3.12, the if block will be true, and the policy will be set to the current CMake version. If CMake is 3.12 or higher, the if block will be false, but the new syntax in `cmake_minimum_required` will be respected and this will continue to work properly!

WARNING: MSVC's CMake server mode [seems to have a bug](https://github.com/fmtlib/fmt/issues/809) in reading this format, so if you need to support non-command line Windows builds, you will want to do this instead:

```cmake
cmake_minimum_required(VERSION 3.1)

if(${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
else()
    cmake_policy(VERSION 3.12)
endif()
```

> 【注意】如果您确实需要在此处设置较低的值，则可以使用«command:`cmake_policy`»有条件地增加策略级别或设置特定策略。请至少为您的macOS用户这样做！

{% hint style='info' %}
If you really need to set to a low value here, you can use «command:`cmake_policy`» to conditionally increase the policy level or set a specific policy. Please at least do this for your macOS users!
{% endhint %}

## 设置一个工程

## Setting a project

所有的顶级（译注：位于整个工程结构的顶部）CMake文件都有如下命令：

Now, every top-level CMake file will have the next line:

```cmake
project(MyProject VERSION 1.0
                  DESCRIPTION "Very nice project"
                  LANGUAGES CXX)
```

现在我们看到更多的语法。引用字符串，空格无关紧要[^3]，项目名称是第一个参数。这里的所有关键字参数都是可选的。`VERSION`设置了一堆变量，如`MyProject_VERSION`和`PROJECT_VERSION`。`LANGUAGES`有C，CXX，FORTRAN和CUDA（CMake 3.7+）。`C CXX`是默认值。在CMake 3.9中，`DESCRIPTION`还添加了项目描述。文档«command:`project`» 可能会有所帮助。

Now we see even more syntax. Strings are quoted, white space doesn't matter [^3], and the name of the project is the first argument (positional). All the keyword arguments here are optional. The version sets a bunch of variables, like `MyProject_VERSION` and `PROJECT_VERSION`. The languages are C, CXX, FORTRAN, and CUDA (CMake 3.7+). `C CXX` is the default. In CMake 3.9, `DESCRIPTION` was added to set a project description, as well. The documentation for «command:`project`» may be helpful. 

> 【警告】CMake不关心空格，你可以用`#`字符添加注释，但禁止在函数调用括号内添加注释。

{% hint style='danger' %}
CMake doesn't care about white space, and you can add comments with the `#` character, but never put a comment inside the function call parenthesis.
{% endhint %}

项目名称真的没有什么特别之处。此时不添加任何编译目标（译注：后文将统一称之为**Target**）。

There's really nothing special about the project name. No targets are added at this point.

## 创建一个可执行程序

## Making an executable

尽管库（译注：后称Library或Lib）更有趣，而且我们将大部分时间花在它们上，但让我们从一个简单的可执行文件开始。

Although libraries are much more interesting, and we'll spend most of our time with them, let's start with a simple executable.

```cmake
add_executable(one two.cpp three.h)
```

这里要分解几件事。`one`是生成的可执行文件的名称，以及创建的CMake目标的名称（我保证会很快听到很多关于Target的信息）。接下来是源文件（译注：后称Source或Src）列表，您可以根据需要列出任意数量的文件名。CMake很聪明，只会编译source扩展名（译注：如.c、.cpp）。对于大多数意图和目的，头文件（译注：后称Header）将被忽略；列出它们的唯一原因是让它们出现在IDE中。Target在许多IDE中显示为文件夹。有关一般构建系统和target的更多信息可在«cmake:buildsystem»中获得。

There are several things to unpack here. `one` is both the name of the executable file generated, and the name of the CMake target created (you'll hear a lot more about targets soon, I promise). The source file list comes next, and you can list as many as you'd like. CMake is smart, and will only compile source file extensions. The headers will be, for most intents and purposes, ignored; the only reason to list them is to get them to show up in IDEs. Targets show up as folders in many IDEs. More about the general build system and targets is available at «cmake:buildsystem».

## 创建一个lib

## Making a library

创建一个lib通过«command:`add_library`»，就像下面这样简单：

Making a library is done with «command:`add_library`», and is just about as simple:

```cmake
add_library(one STATIC two.cpp three.h)
```

您可以选择一种类型的lib，`STATIC`、`SHARED`或`MODULE`。如果禁用此选项，`BUILD_SHARED_LIBS`的值将在`STATIC`和`SHARED`之间进行选择。

正如您将在后面章节中看到的那样，通常您需要创建一个虚构的target，即不需要编译的target，例如header-only lib，这是另一种选择，被称为`INTERFACE` lib（译注：例`add_library(<name> INTERFACE [IMPORTED [GLOBAL]])`），唯一的区别是其后不能跟随文件名。

您还可以使用现有lib创建一个`ALIAS`lib，它只为您提供target的新名称（译注：
例`add_library(<name> ALIAS <target>)`）。这样做的一个好处是你可以在创建lib的新名称中使用`::`（稍后会看到）。 [^3]

You get to pick a type of library, STATIC, SHARED, or MODULE. If you leave this choice off, the value of `BUILD_SHARED_LIBS` will be used to pick between STATIC and SHARED.

As you'll see in the following sections, often you'll need to make a fictional target, that is, one where nothing needs to be compiled, for example, for a header-only library. That is called an INTERFACE library, and is another choice; the only difference is it cannot be followed by filenames.

You can also make an `ALIAS` library with an existing library, which simply gives you a new name for a target. The one benefit to this is that you can make libraries with `::` in the name (which you'll see later). [^3]

## “Target”是你的好朋友

## Targets are your friend

现在我们已经指定了一个target，我们如何添加它的相关信息？例如，它可能需要一个include目录：

Now we've specified a target, how do we add information about it? For example, maybe it needs an include directory:

```cmake
target_include_directories(one PUBLIC include)
```

«command:`target_include_directories`»将include目录添加到target。`PUBLIC`对可执行文件来说意义不大；对于库，它让CMake知道链接到该目标的任何目标也必须包含该目录。其他选项有`PRIVATE`（仅影响当前目标，而不影响依赖项）和`INTERFACE`（仅限依赖项所需）。

然后我们可以链接目标（译注：后称Link）：

«command:`target_include_directories`» adds an include directory to a target. `PUBLIC` doesn't mean much for an executable; for a library it lets CMake know that any targets that link to this target must also need that include directory. Other options are `PRIVATE` (only affect the current target, not dependencies), and `INTERFACE` (only needed for dependencies).

We can then chain targets:

```cmake
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

«command:`target_link_libraries`» 可能是CMake中最有用和最令人困惑的命令。它需要一个target（`another`）并添加依赖项。如果不存在该名称（`one`）的target，则它会添加指向one路径上调用的库的链接（因此命令的名称）。或者你可以给它一个完整的图书馆路径。或链接器标志。只是为了增加最后的混淆，经典的CMake允许你跳过关键字选择PUBLIC，等等。如果这是在目标上完成的，如果你试图在链中进一步混合样式，你会收到错误。

专注于在任何地方使用目标，以及随处可见的关键字，你会没事的。

目标可以包含目录，链接库（或链接目标），编译选项，编译定义，编译功能（参见C ++ 11章节）等。正如您将在两个项目章节中看到的那样，您通常可以获得目标（并始终制作目标）来表示您使用的所有库。即使是不是真正的库的东西，比如OpenMP，也可以用目标来表示。这就是Modern CMake很棒的原因！

«command:`target_link_libraries`» is probably the most useful and confusing command in CMake. It takes a target (`another`) and adds a dependency if a target is given. If no target of that name (`one`) exists, then it adds a link to a library called `one` on your path (hence the name of the command). Or you can give it a full path to a library. Or a linker flag. Just to add a final bit of confusion, classic CMake allowed you to skip the keyword selection of `PUBLIC`, etc. If this was done on a target, you'll get an error if you try to mix styles further down the chain.

Focus on using targets everywhere, and keywords everywhere, and you'll be fine.

Targets can have include directories, linked libraries (or linked targets), compile options, compile definitions, compile features (see the C++11 chapter), and more. As you'll see in the two including projects chapters, you can often get targets (and always make targets) to represent all the libraries you use. Even things that are not true libraries, like OpenMP, can be represented with targets. This is why Modern CMake is great!


## Dive in

See if you can follow the following file. It makes a simple C++11 library and a program using it. No dependencies. I'll discuss more C++ standard options later, using the CMake 3.8 system for now.

```cmake
cmake_minimum_required(VERSION 3.8)

project(Calculator LANGUAGES CXX)
        
add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)

```


[^1]: In this book, I'll mostly avoid showing you the wrong way to do things; you can find plenty of examples of that online. I'll mention alternatives occasionally, but these are not recommended unless they are absolutely necessary; often they are just there to help you read older CMake code.

[^2]: You will sometimes see `FATAL_ERROR` here, that was needed to support nice failures when running this in CMake <2.6, which should not be a problem anymore.

[^3]: The `::` syntax was originally intended for `INTERFACE IMPORTED` libraries, which were explicitly supposed to be libraries defined outside the current project. But, because of this, most of the `target_*` commands don't work on `IMPORTED` libraries, making them hard to set up yourself. So don't use the `IMPORTED` keyword for now, and use an `ALIAS` target instead; it will be fine until you start exporting targets. This limitation was fixed in CMake 3.11.

