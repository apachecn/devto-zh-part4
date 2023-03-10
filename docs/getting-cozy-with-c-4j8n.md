# 熟悉 C++

> 原文：<https://dev.to/deciduously/getting-cozy-with-c-4j8n>

# 吞服药丸

## 一些背景

C++是我自学的白鲸。这些年来，我尝试了很多次，试图通过一个大的 Stroustrup 圣经使它更进一步，但不可避免地很快就令人尴尬地熄灭了。只是*巨大的*，有点复杂。我一直对它心存敬畏，*希望*能够利用它的力量，但由于使用其他语言更容易，我从未抽出时间。我从来没有足够的自律。

我现在回到了学校，所以我利用这个机会，特别报名参加了以 C++为中心的课程。我认为一个好的结构化的学术指导可能只是迫使自己投入时间和精力的门票。我终于用它做了一个重要的为期一个月的最终项目，所以这是我第一次真正的脱离轨道的 C++测试，现在我对这门语言的了解比以前任何时候都多。

结果我想我甚至喜欢上了它，但是这是来自 Rust、JavaScript、C 和公司的觉醒。

## 这是什么

这是一篇非常初级的文章，看看我在完成这项家庭作业时学到的一些东西。我并没有宣称我已经发现了解决这些问题的*最佳*解决方案，这更多的是一个最终对我有用的日志。斯韦茨算法，*迪*算法。

我现在最熟悉的语言模拟是 Rust(或者可能是 C，我不确定——它们出于不同的原因而相似)，这也是我最近使用最多的语言，所以我或多或少地像对待 Rust 项目一样对待这个项目。很快我就能看出这些习语非常不同，但是你必须从某个地方开始。

对于上下文，该项目是一个战列舰 CLI 游戏。代码可以在 [GitHub](https://github.com/deciduously/volley) 上找到。

## 使用报表

我的第一个困惑来自于名称空间的礼节。我知道我不喜欢`using namespace std`，所以我决定使用范围级的语句:

```
std::string someFunc()
{
    using std::string;

    string myString = "";
} 
```

Enter fullscreen mode Exit fullscreen mode

这在全局范围内保持了它的明确性，但允许我在不牺牲清晰性的情况下将特定的东西放入一个函数中——你可以看到它来自哪里。

然后我对`#include`语句感到困惑——有时一个头文件包含在多层其他包含文件中，因为预处理器实际上只是将代码粘贴到解决这些问题的其他代码中。很难看出具体函数实际包含在哪里。

我被指到了[这篇文章](http://www.cplusplus.com/forum/articles/10627/)，值得一读。对我来说，最大的收获是，如果你只是使用一个指向特定对象的指针，你不需要实际包含它，你可以(并且可能应该)只是向前声明它。

## 调试

到目前为止，我主要是一个调试器。我知道如何使用 [`gdb`](https://www.gnu.org/software/gdb/) ,但从未见过比只是在某处添加一个`debug!()`输出更容易的。

我现在已经非常欣赏*`gdb`。我的程序从来都不够大。我需要的所有东西的快速纲要:*

 *1.  用`-g`标志编译。

2.  调用`gdb my_executable`

```
→ gdb build/volley 
GNU gdb (Gentoo 8.3 vanilla) 8.3
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
// blah blah blah
Reading symbols from build/volley...
(gdb) 
```

Enter fullscreen mode Exit fullscreen mode

你在`(gdb)`提示符下输入命令。

1.  中断您想要检查的功能:

```
(gdb) break runFiring
Breakpoint 1 at 0x40e913: file src/game.cpp, line 38. 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用命令在程序中导航:

a.`run` / `r`:运行加载的程序进行调试，直到下一个断点
b. `next` / `n`:逐行执行，**不**进入函数。
c. `step` / `s`:逐行执行，单步执行所有函数
d. `print` / `p`:打印一个变量的值
e. `examine` / `x`:检查一个变量的内存
f. `continue` / `c`:停止逐行执行，继续执行到下一个断点(或程序完成)
g. `kill` / `k`:在不退出的情况下，杀死正在调试的程序`gdb`

您可以随时添加断点，并使用`delete`删除断点。使用向上和向下箭头访问命令历史，留空并点击`Enter`只会重复上一个命令——这对于逐行执行非常有用。

还有很多很多，这里有一个很棒的 PDF 备忘单。我发现自己经常使用`info locals`，它向你展示了当前堆栈框架中的所有变量。

这比添加和删除 println 语句并重新编译要好得多。它更具探索性和互动性，效率提高了一百万倍。我也只是勉强知道如何使用它。

## 自己收拾干净后

现在有一种超快速的方法可以检查你的工作是否完成——内存泄漏法:valgrind。

这是另一个我不知道如何使用但已经从中获益匪浅的工具:

```
± |master U:13 ✗| → valgrind build/volley 
==7744== Memcheck, a memory error detector
==7744== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==7744== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==7744== Command: build/volley
==7744== 

            Battleship!!!

// ... etc - play a game

Game over!
==7744== 
==7744== HEAP SUMMARY:
==7744==     in use at exit: 672 bytes in 8 blocks
==7744==   total heap usage: 3,970 allocs, 3,962 frees, 177,854 bytes allocated
==7744== 
==7744== LEAK SUMMARY:
==7744==    definitely lost: 0 bytes in 0 blocks
==7744==    indirectly lost: 0 bytes in 0 blocks
==7744==      possibly lost: 0 bytes in 0 blocks
==7744==    still reachable: 672 bytes in 8 blocks
==7744==         suppressed: 0 bytes in 0 blocks
==7744== Rerun with --leak-check=full to see details of leaked memory
==7744== 
==7744== For lists of detected and suppressed errors, rerun with: -s
==7744== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0) 
```

Enter fullscreen mode Exit fullscreen mode

等等，堆在出口处使用吗？啊，当然——我写了我的析构函数，但是从来没有真正在顶级实例上调用过`delete`!快速编辑后:

```
$ valgrind build/volley 
==8122== Memcheck, a memory error detector
==8122== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==8122== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==8122== Command: build/volley
==8122== 

            Battleship!!!

// ... etc - play a game

Game over!
==8122== 
==8122== HEAP SUMMARY:
==8122==     in use at exit: 0 bytes in 0 blocks
==8122==   total heap usage: 3,993 allocs, 3,993 frees, 178,686 bytes allocated
==8122== 
==8122== All heap blocks were freed -- no leaks are possible
==8122== 
==8122== For lists of detected and suppressed errors, rerun with: -s
==8122== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0) 
```

Enter fullscreen mode Exit fullscreen mode

现在总共有 672 个字节。呜呜呜。我所需要的只是轻推一下，让我在没有选项的情况下运行它，这款工具还能为你做更多的事情。

## 结构相等

我的一个问题是`std::find()`。这用于定位向量中的元素。显然，这样一个函数将比较元素是否相等。在 Rust 中，您需要在一个结构上派生或手工实现`PartialEq`特征，以便支持该行为。C++没有这个功能，但是你仍然需要能够为结构定义等式。

结构基本上等同于类，但是它们的成员在默认情况下是公共的。这是我从课本上知道的，但从来没有使用过。

在没有提供定义的情况下，您会从`clang` :
中得到这个有点模糊的错误

```
usr/lib/gcc/x86_64-pc-linux-gnu/9.1.0/include/g++-v9/bits/predefined_ops.h:241:17: error: invalid operands to binary expression ('Cell' and 'const Cell')
        { return *__it == _M_value; } 
```

Enter fullscreen mode Exit fullscreen mode

发生这种情况是因为`std::find()`试图在两个结构上使用`==`，但是我们还没有定义如何去做。我认为问题在于它期望通过引用传递，而不是通过值传递。

您可以通过重载`==`操作符并特别传递一个`const`引用:
来允许对您定义的结构进行相等性检查

```
// A single cell on the board
typedef struct Cell
{
    int row;
    char col;
    bool operator==(const Cell &other) const
    {
        return row == other.col && col == other.col;
    }
} Cell; 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很像一份手写的`impl PartialEq block`(来自[的文档](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html)，它也使用了本质上生锈的`const &` :

```
struct Book {
    isbn: i32,
    format: BookFormat,
}

impl PartialEq for Book {
    fn eq(&self, other: &Self) -> bool {
        self.isbn == other.isbn
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 常常数

这就引出了下一点——到处撒`const`*。这是 Rust 为我做好的准备。我本质上是用它作为`mut`的反义词。这是我的一个班级标题:* 

```
class Board
{
    int dimension;
    std::vector<Cell> receivedShots;
    std::vector<Ship> ships;

public:
    Board(int boardSize = BOARD_SIZE);
    bool doesFit(ShipPlacement sp) const;
    char getCharAt(Cell c, bool showShips) const;
    Cell getRandomCell() const;
    Cell promptCell(const std::string &promptStr) const;
    void pushShip(Ship s);
    std::vector<Cell> getAllShots() const;
    bool receiveFire(Cell target);
    int size() const;
    lines toLineStrings(bool showShips) const;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这件事太荒谬了。当我开始编码的时候，我并没有意识到它有多大的适用性，并且因为害怕不理解它而对使用它犹豫不决。现在我的经验是，默认情况下将它添加到任何方法中，只有当我确定我不能拥有它时才将其移除。

## 我的天，到处都是小溪

C++非常倾向于流抽象。当我想漂亮地打印一些数据时，我很快就遇到了这个问题。在 Rust 中，我会选择`impl Display`，在 OOP 中，我会超越`toString()`或其他什么。

在 C++中，你实际上重载了`<<`流插入操作符。举个简单的例子:

```
enum Direction
{
    Left,
    Down
};

std::ostream &operator<<(std::ostream &stream, const Direction &d)
{
    if (d == Direction::Left)
        return stream << "Left";
    else
        return stream << "Down";
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以在一个流中弹出它，不需要调用任何东西:

```
std::cout << "Direction: " << direction << "!" << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

起初这种模式对我来说并不明显，但几天后我会感觉更加自然。

## 重载构造函数

我从未使用过这样的语言，所以它对我来说仍然是新奇和简洁的。在 Rust 中，你使用了特征，这有点不实用。在 C++中，我可以定义三个构造函数:

```
class ShipClass
{
  // ..
public:
    ShipClass();
    ShipClass(char c);
    ShipClass(ShipClassType sc);
 // ..
} 
```

Enter fullscreen mode Exit fullscreen mode

这是获得灵活的 API 的一个非常简单的方法。

## 坎坷

并非一切都是快乐的。我通常已经预料到了 C++给我的所有先验知识，但是有一些突出的东西我仍然不确定如何学会喜欢。

### 建立工具/包管理

对我来说，C++就是该死的西部。

我还没有接触过像 [CMake](https://cmake.org/) 和[自动工具](https://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)这样的东西，但是这些工具的存在本身就说明了很多问题。仅仅使用外部库真的很难，所以项目通常不会这么做。有很多重新发明的轮子，因为包管理是如此彻底的混乱。对于没有受过训练的人来说，这不是一个健康的生态系统，但语言本身足够强大，可能可以弥补这一点。尽管有这个缺点，但它也是一个巨大的生态系统，所以我希望能够探索和利用它，但如果它如此复杂，我不会打扰。

还有像 [`boost`](https://www.boost.org/) 这样的东西，它们本身就是它们自己的野兽。我认为，要对 C++生态系统的能力和质量做出一个合理的、有根据的声明，还需要几年时间。在此之前，这是一个新手关机。

我已经给[写了一篇关于`make`的帖子](https://dev.to/deciduously/how-to-make-a-makefile-1dep)，在此不再赘述。我在那篇文章中介绍的第二个例子就是我用来构建这个项目的 Makefile。

这是课程中的第一门 C++课程，我想以后会涉及到它，但对于这门课，教授基本上说“我不在乎你如何构建你的代码，只要确保我可以重新创建它，如果你不知道该怎么做，这里有一个下载 Visual Studio 的链接”。

我可能应该找个时间学习 Visual Studio，但我认为一次学习一件事情更容易，所以我只是坚持使用我常用的文本编辑器和通过 CLI 编译。通过多年对 Linux 的修补，我已经知道了如何使用 make。我不知道最好的解决方法是什么。似乎在专业环境中，CI/CD 会运行所有的编译器。

### 异常情况

这并不难理解，与 JS 或 Python 中的异常没有什么不同:

```
try
{
    row = stoi(originStr.substr(1, originStr.size() - 1));
}
catch (const std::invalid_argument &ia)
{
    std::cerr << "Please enter a number as your second term." << endl;
    // ..
} 
```

Enter fullscreen mode Exit fullscreen mode

我想我只是被打字水平的东西宠坏了。我不喜欢这样，它看起来像意大利面条式的代码和许多不必要的冗长来捕捉整个应用程序的错误。我也没有真正开始摆弄模板，所以这可能是一个熟悉的问题。它的工作方式似乎和我对 Python 或 JS 的期望差不多。

### 类 vs 结构

这不是问题，但对我来说仍然没有意义。在 Rust 中，一切都是一个`struct`，如果需要，你可以提供一个`impl Struct`块来定义一个构造函数和/或方法，或者只是不定义普通数据。

C++有结构和类，但它们几乎完全相同。唯一的区别是默认的可见性:结构是公共的，类是私有的，但是通过显式注释，它们在其他方面是功能等效的。我尝试对普通数据使用结构，对其他数据使用类，但是界限很模糊。如果我有一个结构体，它只是包装了一个枚举，但是有一堆不同的 getters，比如一个`char`和一个`string`和一个`int`，那是类还是结构体？现在我有一个 struct，它只包含一行和一列，还定义了一些构造函数和一个等式方法。这和上课没什么区别。我不知道哪一个是正确的，或者这有什么关系。当我定义一个新的时候，我只是做了一个直觉的决定，没有再去想它，这似乎没有什么不同。

追问:这是你会用`union`做的事情吗？我仍然不知道什么时候我会想要一个，除非我空间特别有限。

## 结论

我很高兴我终于撕掉了创可贴，用 C++做了一些更实质性的东西，但这座山的浩瀚从未在一开始就如此明显。

马太·亨利在 Unsplash 上拍摄的照片**