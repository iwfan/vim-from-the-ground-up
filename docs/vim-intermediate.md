# Vim 爱好者指南

> 原文链接：[https://thevaluable.dev/vim-intermediate/](https://thevaluable.dev/vim-intermediate/)

![Intermediate Vim concepts](https://thevaluable.dev/images/2020/vim_intermediate/vim_coffee.webp)

欢迎来到本 Vim 系列教程的第二部分！

<!-- TODO 通用菜单 -->

在这篇文章中，我将介绍更多的概念，这其中部分概念，便是 Vim 能如此特别的秘密之处。有谁会不被 Vim 的**宏**所震撼呢？

先让我们来一起看一下吧：

- 使用 buffer（缓冲区）、window（窗口）、tab（标签页）、以及 argument list（参数列表） 来管理打开的多个文件。
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
- **hidden** - 该 buffer 虽然未被展示，但它确实存在，并且文件仍旧处于打开状态。
- **inactive** - 该 buffer  未展示，并且它是一个空 buffer，未被链接到任何文件。

hidden 状态 buffer 中的文件内容在 Vim 中并不是直接可见的。那么你可能会想：如果我看不到 buffer 的话，那我怎么知道它是不是仍处于打开状态呢？

我们可以通过**buffer 列表**来查看所有打开的 buffer。你可以使用`:buffers` 命令来展示它们。它的每一行包括了：

1. buffer 的唯一 ID
2. 展示不同状态信息的标识符（例如： `a` 表示active，`h` 表示 hidden，`<SPACE>`（空格）表示 inactive）
3. buffer 的名称（如果有的话），也可能是 buffer 所链接文件的文件路径
4. 光标所在的行数

例如：`27 %a "layouts/shortcodes/notice.html" line 18`意味着 ID 为 27 的 buffer 处于 active 状态，它的名字是`layouts/shortcodes/notice.html`，并且该文件中的光标位于第 18 行。你也可以通过状态前的`%`来识别当前 buffer 是否正在展示中。

你可以使用这些命令来在 buffer 间导航：

- `:buffer <ID_or_name>` - 使用 ID 或者名字来定位到某个 buffer。
- `:bnext` 或 `:bn` - 切换到下一个 buffer。
- `:bprevious` 或 `:bp` - 切换到上一个 buffer。
- `:bfirst` 或 `:bf` - 切换到第一个 buffer。
- `:blast` 或 `:bl` - 切换到最后一个 buffer。
- `CTRL-^` - 切换到**交替 buffer**（**alternate buffer**, 一般为上一次访问的 buffer，处理在两个 buffer 间频繁切换的需求），该 buffer 会在你的 buffer 列表中用`#`标出来。
- `<ID>CTRL-^` - 切换到某个特定 ID 的 buffer。例如：`75CTRL-^`切换到 ID 为 75 的 buffer。

你也可以使用`:bufdo <command>`命令来对所有 buffer 执行同一条命令。

不是所有的 buffer 都会展示在 buffer 列表，查看那些隐藏的 buffer 可以使用`:buffers!`或者`:ls!`命令。隐藏 buffer 的 ID 之后的标识符为`u`。

现在，让我们来思考一个扩展问题，我们怎么才能创建 buffer 呢？

- 如果你创建了一个 window，那么它会自动创建一个 buffer(下面会讲到)。
- `:badd <filename>` - 将一个文件添加到 buffer 列表中。

能创建 buffer，当然也能删除 buffer：

- `:bdelete <ID_or_name>` - 通过 ID 或者名称来删除 buffer。你可以指定空格分割的多个 ID 或者名字。（译注：简写为`:bd`，也可以不传参，这会删除当前 buffer）
- `:1,10bdelete` - 删除 ID 在 1 到 10 之间的 buffer。
- `:%bdelete` - 删除所有 buffer。

如果你修改了某个文件并且没有保存它，你将无法关闭（或切换）当前 window 使得该 buffer 隐藏，也不能退出 Vim。Vim 会提示你有隐藏的未保存的 buffer。如果你不想看到这个提示的话，我推荐你在你的 vimrc （默认为`~/.vimrc`文件）中配置 hidden 选项：

```vimscript
set hidden
```

你可以直接在你当前的 Vim 中尝试使用`:set hidden!` 来切换该选项的激活与否，试一下切换这个选项，看一下哪个行为更适合你。

想查看任何选项当前的值，你可以使用问号，例如：`:set hidden?` 或者 `:set filetype?`。

> Vim help:
>
>- :help buffers
>- :help :buffers

### Window

Vim 中的 window 指的是你可以用来展示一个 buffer 内容的空间。别忘了，当你关闭 window 时，buffer 依然存在。

当你打开 Vim 时，会自动创建一个含有空 buffer 的 window。

你可以使用`:new`命令来创建窗口，也可以使用下面的命令之一：

- `CTRL-W s` - 水平划分当前的窗口。
- `CTRL-W v` - 竖直划分当前的窗口。
- `CTRL-W n` - 水平划分当前的窗口并编辑一个新文件。
- `CTRL-W ^` - 使用交替 buffer 来水平划分当前窗口（该 buffer 会在你的 buffer 列表中用`#`标记出来）
- `<buffer_ID>CTRL-W ^` - 使用指定 ID 的 buffer 来水平划分当前的窗口。例如：`75 CTRL-w ^`会使用 ID 为 75的 buffer 来打开窗口。

你可以使用以下这些快捷键来将光标移动到另一个窗口：

- `CTRL-W <Down>` 或者 `CTRL-W j`
- `CTRL-W <Up>` 或者 `CTRL-W k`
- `CTRL-W <Left>` 或者 `CTRL-W h`
- `CTRL-W <Right>` 或者 `CTRL-W l`

你可能也总是会想着移动你的窗口，可以使用下面这些命令来完成：

- `CTRL-W r` - 转动所有窗口。（旋转同一行/列划分的多个窗口）
- `CTRL-W x` - 与下一个窗口进行交换。

🚧 Working in progress, PR is welcome!
