# 安装 CMake

> 【技巧】您的CMake版本应该比您的编译器更新。它应该比您正在使用的库（特别是Boost）更新。新版本更适合每个人。

{% hint style='tip' %}
Your CMake version should be newer than your compiler. It should be newer than the libraries you are using (especially Boost). New versions work better for everyone.
{% endhint %}

如果您有一个内置的CMake，它对您的系统来说并不特殊或自定义。您可以在系统级别或用户级别轻松安装新的版本。如果您的用户抱怨CMake要求设置得太高，请随时指他们。特别来说，如果他们想要支持低于3.1的版本，也许即他们想要的是低于3.12的支持......

If you have a built in copy of CMake, it isn't special or customized for your system. You can easily install a new one instead, either on the system level or the user level. Feel free to instruct your users here if they complain about a CMake requirement being set too high. Especially if they want < 3.1 support. Maybe even if they want CMake < 3.12 support...

## 官方安装包
## Official package

你可以从[KitWare](https://cmake.org/download/)下载CMake。如果你在Windows上，这就是你可能会得到CMake的方式。在macOS上获取它也不是很糟糕，但是如果你使用[Homebrew](https://brew.sh) （你应该这样），`brew install cmake`这样会更好。

在Linux上，官网提供了二进制文件，但您需要选择一个安装位置。如果您已经使用了`~/.local`用户空间包（user-space packages），则以下单行命令[^1]将为您获取CMake[^2]：

```bash
~ $ wget -qO- "https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C ~/.local
```

You can [download CMake from KitWare][cmake-download]. This is how you'll probably get CMake if you are on Windows. It's not a bad way to get it on macOS either, but using `brew install cmake` is much nicer if you use [Homebrew](https://brew.sh) (and you should).

On Linux, there are binaries provided, but you'll need to pick an install location. If you already use `~/.local` for user-space packages, the following single line command[^1] will get CMake for you [^2]:

{% term %}
~ $ wget -qO- "https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C ~/.local
{% endterm %}

如果您只想要一个本地文件夹的CMake：

```bash
~ $ mkdir -p cmake-3.12 && wget -qO- "https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C cmake-3.12
~ $ export PATH=`pwd`/cmake-3.12/bin:$PATH
```

If you just want a local folder with CMake only:

{% term %}
~ $ mkdir -p cmake-3.12 && wget -qO- "https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C cmake-3.12
~ $ export PATH=`pwd`/cmake-3.12/bin:$PATH
{% endterm %}

显然，每次启动新终端时都要附加到PATH，或者将其添加到您`.bashrc`或LMod系统中。

并且，如果您想要系统级安装，请安装到`/usr/local`；这是Docker容器中的绝佳选择，例如在GitLab CI上。不要在非容器化系统上尝试。

```bash
docker $ wget -qO- "https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C /usr/local
```

You'll obviously want to append to the PATH every time you start a new terminal, or add it to your `.bashrc` or to an [LMod] system.

And, if you want a system install, install to `/usr/local`; this is an excellent choice in a Docker container, for example on GitLab CI. Do not try it on a non-containerized system.

{% term %}
docker $ wget -qO- "https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C /usr/local
{% endterm %}

如果您在没有wget的系统上，请替换`wget -qO-`为`curl -s`。

你也可以在任何系统上构建CMake，这很简单，但二进制文件更快。

If you are on a system without wget, replace `wget -qO-` with `curl -s`.

You can also build CMake on any system, it's pretty easy, but binaries are faster.

## Pip

这也是一个官方软件包，由CMake的作者在KitWare维护。这是一种相当新的方法，并且在某些系统上可能会失败（最后我检查过Alpine不支持，但是它有CMake 3.8），但是当它工作时（例如在Travis CI上）效果很好。如果你有pip（Python的包安装程序），你可以这样做：

```bash
gitbook $ pip install cmake
```

This is also provided as an official package, maintained by the authors of CMake at KitWare. It's a rather new method, and might fail on some systems (Alpine isn't supported last I checked, but that has CMake 3.8), but works really well when it works (like on Travis CI). If you have pip (Python's package installer), you can do:

```term
gitbook $ pip install cmake
```

只要系统存在二进制文件，您几乎可以立即启动并运行。如果二进制文件不存在，它将尝试使用KitWare的scikit-build包进行构建，目前无法在软件包系统中列为依赖项，甚至可能需要（较旧的）CMake来构建。因此，如果存在二进制文件，就用吧，大多数情况下都是这样。

这样做的好处还在于遵从您当前的虚拟环境。

> 【注意】就个人而言，在Linux上，我将CMake的版本放在文件夹中，比如/opt/cmake312或~/opt/cmake312，然后将它们添加到[LMod](http://lmod.readthedocs.io/en/latest/)。有关[envmodule_setup](https://github.com/CLIUtils/envmodule_setup)在macOS或Linux上设置LMod系统的帮助，请参阅[envmodule_setup](https://github.com/CLIUtils/envmodule_setup)。它需要一些学习，但是管理包和编译器版本的好方法。

And as long as a binary exists for your system, you'll be up-and-running almost immediately. If a binary doesn't exist, it will try to use KitWare's `scikit-build` package to build, which currently can't be listed as a dependency in the packaging system, and might even require (an older) copy of CMake to build. So only use this system if binaries exist, which is most of the time.

This has the benefit of respecting your current virtual environment, as well.

{% hint style='info' %}
Personally, on Linux, I put versions of CMake in folders, like `/opt/cmake312` or `~/opt/cmake312`, and then add them to [LMod]. See [`envmodule_setup`][envmodule_setup] for help setting up an LMod system on macOS or Linux. It takes a bit to learn, but is a great way to manage package and compiler versions.
[envmodule_setup]: https://github.com/CLIUtils/envmodule_setup
{% endhint %}

[^1]: 我认为这是显而易见的，但是你正在下载并运行代码，这会让你暴露给中间攻击的人。如果您处于关键环境中，则应下载该文件并检查校验和。（并且，不，仅仅通过两个步骤执行此操作并不会使您更安全，只有校验和更安全）。

[^2]: 如果您的主目录中没有`.local`，则很容易启动。只要创建这个文件夹，然后添加`export PATH="$HOME/.local/bin:$PATH"`到您home目录的`.bashrc`或`.bash_profile`或`.profile`文件中。现在您可以安装您构建的任何软件包到`-DCMAKE_INSTALL_PREFIX=~/.local`而不是`/usr/local`！

[^1]: I assume this is obvious, but you are downloading and running code, which exposes you to a man in the middle attack. If you are in a critical environment, you should download the file and check the checksum. (And, no, simply doing this in two steps does not make you any safer, only a checksum is safer).
[^2]: If you don't have a `.local` in your home directory, it's easy to start. Just make the folder, then add `export PATH="$HOME/.local/bin:$PATH"` to your `.bashrc` or `.bash_profile` or `.profile` file in your home directory. Now you can install any packages you build to `-DCMAKE_INSTALL_PREFIX=~/.local` instead of `/usr/local`!

[cmake-download]: https://cmake.org/download/
[LMod]: http://lmod.readthedocs.io/en/latest/
