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

同样，也可以使用这些快捷键来调整你的窗口大小：

- `CTRL-W =` - 调整窗口大小以适应屏幕。
- `CTRL-W -` - 缩小窗口的高。
- `CTRL-W +` - 与下一个窗口进行交换。
- `CTRL-W <` - 与下一个窗口进行交换。
- `CTRL-W >` - 与下一个窗口进行交换。

当你想要退出窗口时，你可以使用下面的命令：

- `:q` - **退出(quit)**当前的窗口。之前的你可能被骗了，`:q` 并不会退出 Vim，而是退出窗口，只有在你的 Vim 中只剩下一个窗口时，你才会退出 Vim。Vim
- `:q!` - 退出当前的窗口，即使当前的窗口里链接着一个未保存的 buffer。

> Vim help
>
>- :help windows
>- :help opening-window
>- :help windows-move-cursor
>- :help windows-moving
>- :help windows-resize

### Vim Tabs

我们已经了解了，buffer 就是一个个打开的文件，而窗口就是容纳激活 buffer 的容器。而同样的，标签就是容纳一堆窗口的容器，这一点与标准的 IDE 中的标签是完全不同的。

这里是一些用来创建或者删除标签的命令：

- `:tabnew` 或者 `tabe` - 打开一个新页签。
- `:tabclose` 或者 `tabc` - 关闭当前页签。
- `:tabonly` 或者 `tabo` - 关闭除当前页签之外的其他页签。

你可以使用这些命令在页签之间切换：

- `gt` - 切换到下一个页签。
- `gT` - 切换到上一个页签。

你也可以在上面两个快捷键前面加上数字。例如：`1gT` 将切换到第一个页签去。是的，页签是从 1 开始编号的。

> Vim help
>
>- :help tab-page

### 参数列表（arglist）

参数列表(argument list，也叫做 arglist)是第四个，也是最后一个用来管理你打开文件的容器。正如 Drew Neil 在他的[一篇 Vimcast](http://vimcasts.org/episodes/meet-the-arglist/) 中指出的那样，可以将参数列表看做是 buffer 列表的一个**稳定子集**。因此，它遵循下面两个规则：

1. 参数列表中的每个文件都存在于 buffer 列表中。
2. buffer 列表中有些文件不会出现在参数列表中。

在你打开 Vim 时打开的那些文件（例如当你执行`vim file1 file2 file3`时），都会自动的添加到参数列表中去，同样的，也会添加到 buffer 列表中。

参数列表在我们需要将 buffer 列表中的一些文件隔离出来进行后续操作时非常有用。下面是一些可以用来操作参数列表的命令：

- `:args` - 显示参数列表。
- `:argadd` - 将文件添加到参数列表。
- `:argdo` - 对参数列表中的每个文件执行某个命令。

你可以使用这些命令来编辑参数列表中的文件：

- `:next` - 切换到参数列表中的下一个文件。
- `:prev` - 切换到参数列表中的上一个文件。
- `:first` - 切换到参数列表的第一个文件。

我个人并不会经常使用参数列表，但有些用户会。buffer 列表会被一些与 buffer 并无直接关系的动作所修改，比如打开一个新窗口。但除非你进行显式的修改，否则参数列表会保持不变。这就是我为什么说它是稳定的。

> Vim help
>
>- :help arglist

### 映射快捷键（Mapping Keystrokes）

我们已经看过了很多快捷键与命令了。有没有什么办法可以修改这些快捷键，或者定义一些新的快捷键呢？

你可以对于 Vim 的每个模式定义不同的快捷键：

- `:nmap` - 为 NORMAL 模式创建新的快捷键映射。
- `:imap` - 为 INSERT 模式创建新的快捷键映射。
- `:xmap` - 为 VISUAL 模式创建新的快捷键映射。
- `:cmap` - 为 COMMAND-LINE 模式创建新的快捷键映射。

不同的模式下拥有不同的快捷键听起来可能会让你非常混乱，但实际上多亏了我们的肌肉记忆，这些快捷键都是非常容易记忆的。

让我们一起尝试一个简单的例子来将`w`映射为`dd`。默认情况下，`dd` 删除一行文本，而`w` 是将你的光标在单词之间移动的动作。

1. 运行命令`:nmap w dd`
2. 尝试使用快捷键`dd`，这会删除一行文本
3. 尝试使用`w`，这也会删除一行文本

然而，`w` 不再能被用来在单词之间移动了。让我们尝试修复这个问题，运行命令：`:nmap v w`。

这会试一下使用`v`，会发现也会删除一行文本。这是因为刚刚我们做了一个循环映射（recursive mapping）：将`v` 映射到已经映射为`dd` 的快捷键`w` 上了。 有效的解决方案应该是：

1. 将`w` 映射为`dd`
2. 将`v`映射为`w` 在映射为`dd` 之前的动作上

你可以使用这些命令来做到这点：

- `:nnoremap` - 为 NORMAL 模式创建（不循环映射的）快捷键映射。
- `:inoremap` - 为 INSERT 模式创建（不循环映射的）快捷键映射。
- `:xnoremap` - 为 VISUAL 模式创建（不循环映射的）快捷键映射。
- `:cnoremap` - 为 COMMAND-LINE 模式创建（不循环映射的）快捷键映射。

为了重置我们之前的错误操作，你可以重启 Vim 以回到默认映射，然后执行下面的命令：

```vimscript
:nnoremap w dd
:nnoremap v w
```

现在，`w` 会删除一行文本，`v` 会在单词之间移动。

你也可以在你的映射中使用一些特殊字符，例如：

- `<space>`代表空格
- `<c-w>`代表`CTRL+w`
- `<cr>`代表回车换行（`ENTER`）
- `<esc>` 代表`ESC`

大小写在这里并不重要，`<cr>`与`<CR>`是一样的。你可以使用命令`:help key-natation`来查看完整的列表。

现在，你拥有了可怕的力量，但我建议你不要修改 Vim 默认的键位映射，前面的例子就是原因。尽可能多的使用默认的按键是非常实用的，这保证了你在任何的 Vim 实例上都可以使用这些快捷键，即使你使用的是 docker 或者是服务器上的 Vim。

当你想要自定义一个键位时，你应该使用一个特殊的键位：`leader`。这本质上是一种创建快捷键命名空间的方式，你需要先按下你的 leader 键，再按下你定义的快捷键。多亏了 leader 键的存在，我们自定义的快捷键才不会与 Vim 默认的快捷键冲突。

你可以通过设定变量 mapleader 的值来配置你的 leader 键。例如：

```vimscript
:let mapleader = "<space>"
```

为了能在 Vim 启动时生效，我们见到的快捷键映射命令通常都写在你的 vimrc 文件中。例如，你可以写下下面的命令：

```vimscript
let mapleader = "<space>"
nnoremap <leader>bn :bn<cr>    ; buffer next
nnoremap <leader>tn gt         ; new tab
```

快捷键`<space> bn`会跳转到下一个 buffer，快捷键`<space> tn` 会移动到下一个 tab。注意当你想要将一个快捷键作为命令来映射时，你需要在后面加上`<cr>`,就好像你在输入快捷键之后按下回车一样。

> Vim help
>
>- :help mapping
>- :help leader
>- :help key-notation

### 跳转

Vim 中有一些特殊的动作，叫做跳转动作(jump-motion)。这些动作可以跳转到数行外，比如我们在上一篇文章中接触到的快捷键`G`。

使用`k` 或者`j` 的移动不被认为是跳转动作。

#### 跳转列表（Jump list）

每当我们使用跳转命令时，鼠标的位置都会被记录在跳转列表中。你可以使用这些命令在跳转列表中移动：

- `CTRL-o` - 回到上一个（`o`lder）鼠标位置
- `CTRL-i` - 回到下一个鼠标位置（`i` 在`o` 的旁边，方便操作）

你可以使用这些命令在行与行，甚至 buffer与 buffer之间移动。我几乎任何时候都在使用它们，相信你也会。

想要查看跳转列表的话，可以使用`:jumps` 命令。

#### 修改列表（Change list）

还有一个有用的列表叫修改列表，每当你（仅在使用 INSERT 模式时）插入一些文本时，你当前鼠标的位置会被保存到修改列表中。

你可以使用这些命令在修改之间切换：

- `g;` - 跳转到下一个修改。
- `g,` - 跳转到上一个修改。

### 方法之间的跳转

对于我们写代码的人来说，要是能够在方法与方法之间跳转就太好了。你可以使用下面这些快捷键来做到：

- `[m` - 跳转到方法的开始
- `]m` - 跳转到方法的末尾

但要使得这个快捷键能够正常工作的话，这些方法的语法应该是类似于 Java 的。

> Vim help
>
>- :help jump-motions
>- :help jumplist
>- :help changelist

## 重复快捷键操作

你知道什么是最棒的吗？全自动！相应的，Vim 中也可以全自动的执行快捷键。这是一个非常强大的特性，所以让我们开始吧。

>  译注：
>  该篇中提到的`.`操作与宏，都是非常强大的实用功能，但本文作者对其只进行了基本用法的介绍。并且在宏中给出的例子非常简易， 并未完全展示出其作用。这里推荐之前提到的书《Practical Vim》（中文版叫 《Vim 实战教程》）。书里对这部分进行了大量具有实战价值的举例讲解。
>- [Practical Vim](https://www.goodreads.com/book/show/13607232-practical-vim) - Drew Neil

### 重复单次操作

作为一名开发人员，我们的工作就是自动化那些重复而又冗长的杂事。这就是我们被称为懒人的原因，但我更喜欢称之为将精力放在更重要的事情上。

当我得知vim 中可以进行单次操作的重复时，我的 vim 生涯发生了巨大的改变。这些功能可以让你觉得自己是个超人：

- `.` - 重复上一次修改
- `@:` - 重复上一个执行的命令

这对快捷键现在是我最好的朋友。它们简单，而又高效的可怕。

>- Vim help
>
>- :help single-repeat

### 重复复杂操作：宏

或许你觉得点号操作还不够用？那来看看这一个。宏将带给你更强大的指尖力量。

在 Vim 中，你可以录制一系列操作，并且按顺序来重复它们。这需要下面这些步骤：

1. `q<小写字母>` - 开始在某个寄存器中录制操作。你可以将寄存器认为是内存中的一块空间，或者也可以当成一个剪贴板。
2. 现在你所做的任何快捷键操作都会被保存起来。
3. `q` - 停止录制。
4. `@<小写字母>` - 执行你在该寄存器中录制的所有快捷键。

例如，当你需要在多行重复某些操作时：

1. 按下 `qa`。
2. 处理你需要做的工作。例如：`^cawhello<esc>`。
3. 按下 `q`。
4. 使用`@a` 执行你录制的一系列快捷键。例如你可能需要在新的一行去执行上面的修改操作（将该行第一个单词替换为`hello`）

在这里我使用`a` 来作为一个例子。你可以使用任何你需要的小写字母来作为寄存器。当你执行过一次宏之后，你可以使用`@@`来重复你上一次使用的宏，而不需要指定寄存器。

> Vim help
>
>- :help complex-repeat

## 命令行窗口

你可以这样在 Vim 中查看你的 Ex 命令历史：

- `q:` - 打开命令行历史
- `q/` `q?` - 打开搜索历史
- `CTRL+f` - 当你位于 COMMAND LINE 模式时，打开命令行历史

使用上面的快捷键，你可以修改任何你想要的命令，并在你需要的时候按下`ENTER`来执行它，这在你想要重复执行命令，但又需要轻微修改时是非常有用的。或者在你想要使用 Vim 的编辑能力来输入复杂的命令时，这也是很棒的功能。

你也可以使用这些命令来展示命令行历史：

- `:history :` - 命令行历史。
- `:history /` 或 `:history ?` - 搜索历史。

如果你不喜欢使用`CTRL-f`来打开命令行历史的话，你可以修改选项`cedit` 的值，默认值为`^f`(`CTRL-f`)。

最后，你可以指定你想要将多少行历史命令保存下来，修改`history` 的值即可。最大值为 10000。

> Vim help
>
>- :help cmdline-window
>- :help 'history'

## Undo 树

我们已经在上一篇文章中看过了如何进行撤销重做。Vim 允许你将在所有文件中的修改都保存在一个文件中。这意味着即使你关掉了 Vim，当你回到文件中，你仍旧能够通过撤销/重做来访问之前的修改。

你需要使用这些命令来在你的 vimrc 中进行配置：
```vim
" save undo trees in files
set undofile
set undodir=~/.vim/undo

" number of undo saved
set undolevels=10000
```

你并不一定需要将你的修改都保存在`~/.vim/undo`中，你可以将其修改为任何你想要的路径。

将`undolevels`的值设置为 10000 来指定为每个文件最多保存 10000 次修改。

这还没完，你可能会认为 Vim 只会保存一个修改列表，但实际上它会保存一整个修改树。

让我们看个例子：当你做了三个修改，然后撤销其中两个，然后进行新的一个修改（或者更多个），这时便会创建一个新的分支。可以使用这样的图来表达：
```text
@ -> 新修改
|
| o -> 修改 3
| |
| o -> 修改 2 
|/
o -> 修改 1
|
o
```

第三与第二次修改已经被撤销了，`@`代表了你当前所在的位置。你可以回退到任一个修改上，这意味着你可以回退你之前做过的任意修改。

原生 Vim 下，在这些修改之中跳转还是有点困难的，但下个部分我们会介绍一个非常有用的插件来回到你之前的任何修改。通过该插件，你甚至可以在你的 undo 树中进行搜索。

> Vim help
>
>- :help undo-redo
>- :help undo-persistence
>- :help undo-tree
