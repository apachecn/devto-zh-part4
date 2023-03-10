# 如何制作 Makefile

> 原文：<https://dev.to/deciduously/how-to-make-a-makefile-1dep>

# 什么叫制造？

这篇文章将通过两个小例子探索 GNU Make 的基础知识。这是一个令人惊讶的通用构建工具，虽然有点过时，但因为它无处不在，所以至少应该基本熟悉它是如何工作的。

警告:据我所知，这主要与 Mac 和 Linux 用户有关。除了启动 ide 并让它处理事情，或者在可能的情况下使用 WSL 作为支撑之外，我不太了解在 Windows 上构建工具或开发。我知道你可以通过 [GnuWin32](http://gnuwin32.sourceforge.net/packages/make.htm) 得到`make`。我不知道它的效果如何，也不知道是否有人使用它。

简而言之，`make`是一个读取 *Makefile* 并将源文件转换成可执行文件的工具。它不关心使用什么编译器来做这些，它只关心构建流程编排。

如果你以前编译过源码包，你可能熟悉下面的命令:

```
$ ./configure
$ make
$ sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

大量的*nix 包是以 C 或 C++源代码的形式发布的，并且会像这样构建。第一行运行一个单独的程序来为您配置 Makefile，这在依赖系统库的大型项目中是必要的。最后一行通常假设管理员权限，因此它可以将刚刚构建的可执行文件复制到系统路径中。不过，我们并不需要这些来开始`make`。只有中间线就够了。名字很贴切，不是吗？

在这篇文章中，我将介绍两个不同目标的例子。如果你不知道你在看什么，语法可能看起来晦涩难懂(至少对我来说是这样)，但是一旦你知道非常基本的规则，它们就非常简单了。

# 例一-下载文件

我们先做简单的。这个 Makefile 只存在于将 Clojure 的构建工具`boot`下载到用户当前目录。这个工具以一个 shim 的形式存在，它下载一个 jar 文件来处理剩下的部分，而且这个 shim 非常小，所以有时把它放在项目目录本身而不是系统路径中会很方便。

```
.PHONY: deps help

SHELL        = /bin/bash
export PATH := bin:$(PATH)

deps: bin/boot

bin/boot:
    (mkdir -p bin/                                                                              && \
    curl -fsSLo bin/boot https://github.com/boot-clj/boot-bin/releases/download/latest/boot.sh  && \
    chmod 755 bin/boot)

help:
    @echo "Usage: make {deps|help}" 1>&2 && false 
```

Enter fullscreen mode Exit fullscreen mode

我们会从头开始。

```
.PHONY deps help

SHELL       = /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们宣布*假目标*。为了解释这一点，我们需要谈谈什么是`make`的核心:规则。

Make 用于将源变成目标。为了做到这一点，我们为它提供了一些规则，用于理解哪些资源以及如何将它们提供给编译器以获得正确的目标。最后，我们应该已经生成了所有需要的目标——编译后的源代码。

记住这一点，规则很容易被发现。每个规则都以要创建的目标的名称开始，后跟一个冒号。冒号后面是这个目标所依赖的任何目标*，下面缩进的是一系列命令，或者食谱，用来从它的依赖项构建目标。当你用一个目标调用`make`时，它会特别指定那个目标，但是当你自己调用它时，它只是开始评估它看到的第一个不以`.`开头的规则(像`.PHONY`)。*

接下来，我们定义 shell 可执行文件的位置，

`$()`语法是一个 Make 变量。Make 很简洁，因为它自动将它在环境中找到的每个变量都公开为 make 变量，所以我们可以只使用来自`bash`的`$PATH`和`$(PATH)`。为了定义你自己的名字，你只需要赋值给名字，省略掉括号，就像我们在第一行中做的那样——这是对`$(SHELL)`变量的赋值。

值得注意的是，我们对它使用了`:=`赋值语法。这明确定义了一个简单扩展的 T2 赋值。这个变量将被读取一次，仅此而已——它里面的任何其他变量在赋值时都会被立即扩展一次。

递归扩展的变量无论何时被替换，都会扩展内部的任何内容。这很强大，但也可能导致无限循环和执行缓慢等问题，因此注意这种差异很重要。

需要注意的是，这只适用于该流程及其任何子流程——这不是永久的，它不能改变父流程。不过，如果你在`make`内部构建，并且不弄乱你的全局 env，这仍然是有用的！

然后我们开始我们的第一条规则。在这种情况下，默认规则称为`deps`，这是我们的一个虚假目标。不会创建名为“deps”的文件。

```
deps: bin/boot 
```

Enter fullscreen mode Exit fullscreen mode

在目标名称之后，您会发现一个冒号，然后是一列*依赖项*。这些是在评估此规则之前必须完成的目标。在为这个目标执行命令块之前，Make 将确保每个目标都存在，如果找到它们，就评估它们的规则。在这种情况下，依赖项是目标“bin/boot”。没有与此规则相关联的命令，它所做的只是调用另一个规则。

```
bin/boot:
    (mkdir -p bin/                                                                              && \
    curl -fsSLo bin/boot https://github.com/boot-clj/boot-bin/releases/download/latest/boot.sh  && \
    chmod 755 bin/boot) 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个虚假的目标，并包括一个斜线，这只是意味着一个目录名。这个目标，或者评估这个规则的结果，将会在我们添加到路径中的那个目录中结束。

这条规则没有任何依赖关系——它们都与目标名称出现在同一行。它确实有命令——这个规则将创建一个目录，执行`curl`从 GitHub 下载文件，并执行`chmod`使下载的文件可执行。

因此，运行`make`将找到`make deps`规则，它本身是空的，但是有`bin/boot`作为依赖项。Make 将意识到`bin/boot`尚不存在并执行该规则，这将相应地创建文件。

尝试运行它，然后再次运行它:

```
$ make
(mkdir -p bin/                                                                              && \
curl -fsSLo bin/boot https://github.com/boot-clj/boot-bin/releases/download/latest/boot.sh  && \
chmod 755 bin/boot)

$ make
make: Nothing to be done for 'deps'. 
```

Enter fullscreen mode Exit fullscreen mode

在第一次评估这个规则之后，名为`boot`的文件已经存在于名为`./bin`的目录中。目标找到了，所以`make`没有做额外的工作。这种便利的性质被称为*等幂*。重复调用和一次调用的效果一样:`f(x);`和`f(x); f(x);`是等价的。

整洁！让我们看一些更典型的东西。

## 例子二:构建一些 C++

这是一个更复杂的问题。这个 makefile 是我在考虑它之前放入一个全新的 C++项目目录中的。它更能说明 makefiles 在野外可能是什么样子，但是范围仍然很小。

它期望一个包含一堆`.cpp`(和`.h`)文件的`src`目录，并将创建一个名为`build`的目录，包含所有的`.o`目标文件和可执行文件，无论你给它取什么名字。然后，您可以运行该可执行文件。

```
.PHONY: all clean help

CXX=clang++ -std=c++11
FLAGS=-Wall -Wextra -Werror -pedantic -c -g

BUILDDIR=build
SOURCEDIR=src
EXEC=YOUR_EXECUTABLE_NAME_HERE
SOURCES:=$(wildcard $(SOURCEDIR)/*.cpp)
OBJ:=$(patsubst $(SOURCEDIR)/%.cpp,$(BUILDDIR)/%.o,$(SOURCES))

all: dir $(BUILDDIR)/$(EXEC)

dir:
    mkdir -p $(BUILDDIR)

$(BUILDDIR)/$(EXEC): $(OBJ)
        $(CXX) $^ -o $@

$(OBJ): $(BUILDDIR)/%.o : $(SOURCEDIR)/%.cpp
        $(CXX) $(FLAGS) $< -o $@

clean:
        rm -rf $(BUILDDIR)/*.o $(BUILDDIR)/$(EXEC)

help:
        @echo "Usage: make {all|clean|help}" 1>&2 && false 
```

Enter fullscreen mode Exit fullscreen mode

在最上面，我们又有了我们的假目标——这些目标并没有创建真正的文件，它们只是作为一个参数被调用。

接下来，我们通过给变量`$(CXX)`和`$(FLAGS)` :
赋值，将它指向我们的 C++编译器

```
CXX=clang++ -std=c++11
FLAGS=-Wall -Wextra -Werror -pedantic -c -g 
```

Enter fullscreen mode Exit fullscreen mode

这些不是特别的名字，你可以随便叫它们。我们将在规则中直接引用它们。

C++编译分两个阶段进行。首先，我们将所有独立的`*.cpp/*.h`对编译成它们自己的`.o`目标文件，在一个独立的步骤中，我们将它们链接成一个可执行文件。我们传递给编译器的标志只有在从源代码构建对象时才有意义——链接已经编译好的对象不需要它们！通过这种方式，我们可以在规则评估中使用或不使用这组标志来调用编译器。我喜欢让我的编译器尽可能的严格——这些标志把所有的警告都变成了阻止成功编译的错误，并且启用了全套的检查。`-c`标志指示它不要进入链接阶段，以一个`.o`文件结束，`-g`标志生成源代码级调试信息。

一个更好的 makefile 会有多个构建配置。这也是一个初学者工具包。

接下来的三个作业只是配置所有东西的名称:

```
BUILDDIR=build
SOURCEDIR=src
EXEC=YOUR_EXECUTABLE_NAME_HERE 
```

Enter fullscreen mode Exit fullscreen mode

我认为输出的`build`和源文件的`src`是有意义的，但是你可以在那里调整它们，`$(EXEC)`将是最终编译的二进制文件。

在此之下，我们定义了源在哪里，以及对象应该被称为什么:

```
SOURCES:=$(wildcard $(SOURCEDIR)/*.cpp)
OBJ:=$(patsubst $(SOURCEDIR)/%.cpp,$(BUILDDIR)/%.o,$(SOURCES)) 
```

Enter fullscreen mode Exit fullscreen mode

`$(SOURCES)`变量是用 [`wildcard`](https://www.gnu.org/software/make/manual/html_node/Wildcard-Function.html#Wildcard-Function) 函数构建的。这个变量收集在`src/`中带有`.cpp`扩展名的任何东西。

接下来我们用 [`patsubst`](https://www.gnu.org/software/make/manual/html_node/Text-Functions.html#Text-Functions) 。其语法是*模式*、*替换*、*文本*。图样和替换中的`%`字符是一样的，另一部分被调换了。例如，这种替换将“game.cpp”变成了“game.o”。对于文本，我们传入刚刚定义的`$(SOURCES)`变量——因此`$(OBJ)`变量将包含对应于`make`找到的每个`src/*.cpp`文件名的`build/*.o`文件名。

查看[快速参考](https://www.gnu.org/software/make/manual/html_node/Quick-Reference.html)了解可用内容的完整介绍。

我使用了简单的扩展变量赋值。当你知道这样做会得到你需要的结果时，这是一个好主意，特别是在使用像`wildcard`这样的函数时——递归扩展这些函数会(但并不总是)导致显著的速度变慢。

配置好所有变量后，我们就可以开始定义规则了。第一条规则是我们的默认行为，这条规则叫做`all` :

```
all: dir $(BUILDDIR)/$(EXEC) 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的一个假目标，所以没有相应的输出文件“all”。同样，像第一个例子中的`deps`一样，这个规则没有命令，只有依赖关系。这个有两个依赖项，`dir`和`$(BUILDDIR)/$(EXEC)`。它将按照找到它们的顺序执行它们，所以让我们先跳到`dir`:

```
dir:
    mkdir -p $(BUILDDIR) 
```

Enter fullscreen mode Exit fullscreen mode

这个没有依赖项，所以它会立即执行这个命令。这很简单——它只是确保`build`目录存在。一旦完成，我们可以评估`$(BUILDDIR)/$(EXEC)` :

```
$(BUILDDIR)/$(EXEC): $(OBJ)
        $(CXX) $^ -o $@ 
```

Enter fullscreen mode Exit fullscreen mode

这条规则开始变得有点奇怪了。目标本身与第一个例子中的`bin/boot`没有什么不同，只是使用 make 变量来构建它。如果您已经将`$(EXEC)`设置为`my_cool_program`，则该目标被命名为`build/my_cool_program`。它依赖于另一个 make 变量`$(OBJ)`，我们刚刚把它定义为一个对应于每个源文件的目标文件。这将首先解决，所以在查看命令之前，让我们先看一下该规则:

```
$(OBJ): $(BUILDDIR)/%.o : $(SOURCEDIR)/%.cpp
        $(CXX) $(FLAGS) $< -o $@ 
```

Enter fullscreen mode Exit fullscreen mode

哇，这里有*两组*依赖关系！怎么回事，本。

这就是所谓的*静态模式规则*。当我们有一个目标列表时，我们就用这个。总目标`$(OBJ)`，由我们将要创建的每一个目标文件组成。在第一个冒号之后，我们需要明确定义每个单独的对象如何依赖于特定的源。我们再次看到用于模式匹配的`%`，这与上面的`patsubst`调用没有什么不同。每一个都将与相应的具有相同的名称”。cpp”文件，但扩展名翻转为”。o”。

该规则的命令块将针对每个匹配的源/目标对执行。我们使用我们在顶部定义的 make 变量来调用编译器，并传入我们所有的标志，包括在链接阶段之前发出停止信号的`-c`标志，只输出目标文件。

然后我们使用一些自动变量来填充适当的命令。`$<`对应于我们正在处理的依赖项的名称，`$@`对应于目标的名称。完全展开后，这个`$(CXX) $(FLAGS) $< -o $@`命令看起来会像`clang++ -std=c++11 -Wall -Wextra -Werror -pedantic -c -g src/someClass.cpp -o build/someClass.o`。

太棒了。一旦此规则完成，每个"。cpp“文件有相应的”。o”文件放在`build/`目录中，正是我们定义的`$(OBJ)`。准备就绪后，`make`将跳回到调用规则，并使用`$(CXX) $^ -o $@`命令将我们的对象链接在一起。

这是相似的，但是我们省略了我们的标志。我们还使用不同的自动变量。`$^`对应于`$(OBJ)`所代表的整个列表。您也可以使用`$+`，它完全包含了每个列表成员- `$^`省略了任何重复的成员。`$@`部分和之前一样——代表目标。这可能会运行类似于`clang++ --std=c++11 build/someClassOne.o build/someClassTwo.o build/someClassThree.o build/main.o -o build/my_cool_project`的命令。

一旦完成，您就可以在`build/my_cool_project`准备好编译好的可执行文件了。谢谢，`make`！

这个 makefile 还提供了`clean` :

```
clean:
        rm -rf $(BUILDDIR)/*.o $(BUILDDIR)/$(EXEC) 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个没有依赖关系的虚假目标，它只是运行`rm`来清除所有的目标文件和可执行文件。这样，当您再次运行`make`时，它将不得不重新构建所有内容。否则，它将只构建自上次构建项目以来已经更改的任何文件。

我们只是触及了表面，但希望这有助于揭开这些文件的神秘面纱。

挑战:编写自己的`make install`规则，将新创建的目标从`build`复制到一个更冷的地方！

杰森·布里斯科在 Unsplash 上的照片