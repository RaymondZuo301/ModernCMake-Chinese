# Modern CMake 简介

人们对构建系统爱之深责之切。
如果你看了CppCon17上的演讲，你会发现很多开发者嘲笑构建系统的例子。
那么问题来了：为什么呢？
诚然，构建过程不乏很多问题。
但是我认为，在2018年我们对这些问题有很好的解决方案。
那就是CMake，不过CMake 2.8除外，那是C++11存在前的产物！
对于CMake而言有很多糟糕的示例（即使是那些发布在KitWare自己的教程列表上的示例）。
我们聊的是Modern CMake，CMake 3.1+，也许甚至是CMake 3.12+!
它干净、强大、优雅，因此你可以将大部分时间花在编码上，而不是在不可读的、不可维护的Make（或CMake 2）文件中添加内容，并且CMake 3.11+也应该明显更快！

> 这是一本活的文档，你可以在Gitlab上提issue或者发起merge request [GitLab](https://gitlab.com/CLIUtils/modern-cmake)。（译注：英文原版可以看这个Gitlab链接）

简而言之，如果你正在考虑使用Modern CMake，这些可能是最先浮现在你脑海的问题：

## 我为什么需要一个好的构建系统

以下任意一项是否适用于你？

- 你希望避免对路径的硬编码
- 你需要在多台计算机上构建包
- 你想使用CI（持续集成）
- 你需要支持不同的操作系统（甚至可能只是Unix的风格）
- 你想支持多种编译器
- 你想使用IDE，但可能不是一直都用
- 你想要描述逻辑上的程序结构，而不是标志和命令
- 你想使用库
- 你想使用像Clang-Tidy这样的工具来帮助你进行编码
- 你想使用调试器

如果是这样，你将受益于CMake式的构建系统。

## 为什么答案一定是CMake呢？

构建系统是一个热门话题。当然，你有很多选择。但是，即使是那种非常好的，或者那种对熟悉的语法进行再利用的，也无法接近CMake。为什么？支持性。每个IDE都支持CMake（或CMake支持该IDE）。比起任何其他构建系统，更多的软件包使用CMake。因此，如果你使用了被设计为包含在代码中的库，你的选择是：创建自己的构建系统，或使用某一个现有构建系统，但总会包含CMake。如果你包含了多个项目，CMake将很快成为公有选项。并且，如果你需要预安装的库，那么它恰好就有相应查询脚本或配置脚本的可能性非常高。

## 为什么要用 Modern CMake（现代化CMake）?

大约在CMake 2.6-2.8版本期间，CMake开始接管（译注：Kitware开始接管？）。它包含与绝大多数Linux系统的包管理工具，并且被用于大量软件包。然后Python 3出现了。我知道，这无论如何跟CMake没关系。但是Python有了版本3，并且是伴随版本2诞生的，它是一个艰难，丑陋的过渡，在某些地方仍在进行，即使在今天。我相信CMake 3运气比Python 3好不到哪去。 [^1] 尽管每个版本的CMake都在疯狂地向后兼容，但3系列被视为新东西。因此，你会发现操作系统就像拥有GCC 4.8的CentOS7，几乎完全支持C ++ 14，但却包含了CMake 2.8，一个在C ++ 11之前推出的版本。

你真的应该**至少**使用你的编译器发布之后出现的CMake版本，因为它需要知道该版本的编译器flags等。而且，由于CMake将自己降级为CMake文件中所需的最低版本，因此即使是系统范围内安装新的CMake也是非常安全的。你应该**至少**在本地安装它，这容易（在许多情况下为1-2行），你会发现5分钟的工作将为你节省数百行和几小时的CMakeLists.txt写作，并且从长远来看将更容易维护。

本书试图解决您在网络上发现的不良示例和最佳实践的问题。

## 其他来源

网上还有一些其他地方可以找到好的信息，以下是其中一些：

- [The official help](https://cmake.org/cmake/help/latest/)：真的很棒的文档。组织精良，搜索方便，您可以在顶部切换版本。它只是没有一个伟大的“最佳实践教程”，这正是这本书试图补充的。
- [Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1)：一个很棒的做和不做的清单。
- [Embracing Modern CMake](https://steveire.wordpress.com/2017/11/05/embracing-modern-cmake/)：一个对该术语有良好描述的帖子。
- [It's time to do CMake Right](https://pabloariasal.github.io/2018/02/19/its-time-to-do-cmake-right/)：一套现代CMake项目的很好的最佳实践。
- [The Ultimate Guide to Modern CMake](https://rix0r.nl/blog/2015/08/13/cmake-guide/)：一个具有类似意图的略微过时的帖子。

## 制作组

Modern CMake 最早由 [Henry Schreiner](https://iscinumpy.gitlab.io)撰写。其他贡献者可以在这里找到 [listed on GitLab](https://gitlab.com/CLIUtils/modern-cmake/graphs/master).

[^1]: CMake 3.0还从非常旧版本的CMake中删除了几个长期弃用的功能，并对与方括号相关的语法进行了一次非常微小的向后不兼容的更改，因此这并不完全公平；可能会有一些非常非常古老的CMake文件与3不兼容，但我一个都没遇到。

