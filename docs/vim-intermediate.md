# Vim 爱好者指南

> 原文链接：[https://thevaluable.dev/vim-intermediate/](https://thevaluable.dev/vim-intermediate/)

![Intermediate Vim concepts](https://thevaluable.dev/images/2020/vim_intermediate/vim_coffee.webp)

欢迎来到本 Vim 系列教程的第二部分！

<!-- TODO 通用菜单 -->

在这篇文章中，我将介绍更多的概念，这其中部分概念，便是 Vim 能如此特别的秘密之处。有谁会不被 Vim 的**宏**所震撼呢？

先让我们来一起看一下吧：

- 使用 buffer、窗口、tab、以及参数列表来管理打开的多个文件。
- 一些实用的在你整个项目中快速跳转的方法。
- 命令的快捷键映射，或者为旧快捷键创建新的映射。
- 一种强大的功能，帮助你重复你的快捷键操作。
- 操作命令历史的方法。
- 一些为已知问题提供不同解决方式的插件。

这篇文章的信息量是非常大的。我的建议是：多花些时间，不要想着一蹴而就。在阅读的同时可以在 Vim 上进行实验，尽力去理解它是如何工作的。最终，你将拥有一个可以使用键盘来完全控制的强大工具。

你将在每个小节的结尾看到一些有关的 Vim 帮助命令。当你想要更进一步的探索时，你可以直接在 Vim 中阅读这些帮助文档。

## Vim 的空间组织方式

如果你在使用 IDE 的话，你肯定在使用 tab 管理你打开的多个文件。 Vim 使用了另外一种方式来代表与组织你打开的文件。事实上，这里有[四个抽象层](https://thevaluable.dev/abstraction-type-software-example/)用来处理这个问题：buffer、window、tab、以及 argument list。

### Buffers

一个**buffer**直接关联着一个内存中打开的文件。对比标准的 IDE 来说，一个 buffer 就是一个 tab 的**内容**。 最大的不同就是：当你在 IDE 中关闭了一个 tab 时，你确实的关掉了一个文件，但在 Vim 中不是这样的：当你关掉一个包含着 buffer 的 window 时，这个 buffer 其实并不会被关掉，只是隐藏起来了。

事实上，一个 buffer 可以拥有三种不同的状态：

- **active** - 该 buffer 正在一个 window 中展示。
- **hidden** - 该 buffer 正在一个 window 中展示。
- **active** - 该 buffer 正在一个 window 中展示。

🚧 Working in progress, PR is welcome!
