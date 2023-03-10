# 进出兔子洞

> 原文：<https://dev.to/deciduously/in-and-out-of-rabbit-holes-5717>

# web assembly 带来更多乐趣

在过去的一周里，当我开始我的新的副业项目时，我陷入了以下的兔子洞，完全超出了必要的要求。我是这样逃出来的。

## 锈中动态调度

Rust 二进制文件可能会膨胀得非常快，减轻这种情况的一种方法是优先选择动态分派(即`Box<dyn Trait>`)而不是单态化(`fn<T: Trait>(obj: T) {}`)。简而言之，后一种通用函数语法将在二进制文件中为它所使用的每种类型创建一个完全独立的函数实现，并在编译时选择正确类型的版本。前者不会在编译时绑定这个函数调用，而是仅仅确保所指示的特征被实现。然后，它会在运行时*动态*绑定最终调用它的类型的方法调用。

我重写了我所有的泛型函数来接受 trait 对象，我的包变小了，YMMV。

事实证明，这不是一次无足轻重的重写。需要注意的一点是，虽然一个`Box<dyn Trait>`是`Sized`(因为一个`Box`是有大小的)，但是一个*特征对象*(`dyn Trait`部分)根据定义不能是。类型未知，我们只知道它实现了这个接口，方法调用将在运行时被动态调度。这意味着，如果你想自动操作特性对特性的机器，你需要做一些手工的准备，并准备好与借货员近距离接触。我仍然不确定我是否理解正确，但是它*可以*编译。

作为一个例子，我有两个相关的特征，`Drawable`和`Widget`。一个`Drawable`是知道如何在画布上绘制自己的类型，一个`Widget`是包含子`Widget`的 2d 网格的组织组件

当然，有些小部件也是`Drawable`的，所以最终会有一些东西被绘制到屏幕上。最终的想法是提供一组足够通用的`Widget`,在这个库(或其他任何东西)上构建的游戏永远不需要手动实现`Drawable`,它们可以像`Text`和`Button`和`Area`一样组合`Widgets`来处理所有细节，我正在使用我在它上面构建的游戏来驱动需要编写的小部件。

我能想到的最简单的方法就是让这个特性的实现者编写一个函数，返回其他类型，`MountedWidget` :

```
/// Trait representing things that can be drawn to the canvas
pub trait Drawable {
    /// Draw this game element with the given top left corner
    fn draw_at(&self, top_left: Point, w: WindowPtr) -> Result<Point>;
    /// Get the Region of the bounding box of this drawable
    fn get_region(&self, top_left: Point, w: WindowPtr) -> Result<Region>;
}

/// Trait representing sets of 0 or more Drawables
/// Each one can have variable number rows and elements in each row
pub trait Widget {
    /// Make this object into a Widget
    fn mount_widget(&self) -> MountedWidget; 
```

Enter fullscreen mode Exit fullscreen mode

`MountedWidget`提供了自己的`Drawable`实现，该实现知道如何有条不紊地在子网格中绘制，并且可以选择包含一个原始的`Drawable`本身:

```
/// A container struct for a widget
pub struct MountedWidget {
    children: Vec<Vec<Box<dyn Widget>>>,
    drawable: Option<Box<dyn Drawable>>,
} 
```

Enter fullscreen mode Exit fullscreen mode

一方面，我认为我应该能够进一步简化这一点，避免分配中间结构，但这种设置让我有所收获。不幸的是，正如所写的那样，每个窗口小部件在每一帧中都被重新创建和删除——很明显，应该首先安装所有的窗口小部件，并根据需要进行调整，但至少这是一个开始。

## 一箱一箱

我以前说过，现在我再说一遍:`cargo`是包装经理中的精英。其他人都错过了。

我希望利用它的一种方式是将我的画布挂载和绘图内容作为它自己的板条箱，并让增量编译单独缓存构建。事实证明，这真的很简单。这是一个包含三个模块的标准库在目录方面的样子:

```
$ tree
.
├── Cargo.toml
├── LICENSE
├── README.md
└── src
    ├── drawing.rs
    ├── game.rs
    └── lib.rs

1 directory, 6 files 
```

Enter fullscreen mode Exit fullscreen mode

要把你的“绘图”模块变成它自己的箱子，让它看起来像这样:

```
.
├── Cargo.toml
├── LICENSE
├── README.md
└── src
    ├── drawing
    │   ├── Cargo.toml
    │   ├── LICENSE
    │   ├── README.md
    │   └── src
    │       └── lib.rs
    ├── game.rs
    └── lib.rs 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！在`Cargo.toml`中，对于母箱，只需添加依赖关系:

```
[dependencies.drawing]
path = "src/drawing" 
```

Enter fullscreen mode Exit fullscreen mode

这再容易不过了，效率的提高带来了实实在在的变化，尤其是对于这些庞大的 WASM 建筑。你可以随意抓取这个目录，并把它放在你喜欢的任何地方(比如托管在一个 git repo 中)，你可以把你的`Cargo.toml`指向你需要的地方。

## 尺寸优化

RustWasm 书中的这个[链接](https://rustwasm.github.io/book/reference/code-size.html#optimizing-builds-for-code-size)有一些很好的提示。你需要安装 [Binaryen](https://github.com/WebAssembly/binaryen) toolkit 来获得全部好处——它可以在你编译的 WASM 输出上运行进一步的速度和大小优化，超过 LLVM 通过`rustc`所做的。你需要安装 [`cmake`](https://cmake.org/) ，这在所有主要的软件仓库中都有(`apt`、`homebrew`、`chocolatey`等)。)

```
$ git clone https://github.com/webassembly/binaryen
$ cmake . && make 
```

Enter fullscreen mode Exit fullscreen mode

这需要一点时间。有几个前端我们不会使用，见自述文件的用法。我只是将`wasm-opt`符号链接到我的用户路径:

```
$ ln -s /home/ben/code/extern/binaryen/bin/wasm-opt /home/ben/.local/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

然后，我编写了一个脚本来处理`wasm-opt`调用:

```
#!/bin/bash
PKGDIR='pkg'
BINARY='fivedice_bg'
WASM="$PKGDIR/$BINARY.wasm"

function wasm_size {
    wc -c $1
}

function echo_size {
    echo "$(eval wasm_size $1)"
}

function extract_size {
    wasm_size $1 | sed 's/^\([0-9]\+\).*/\1/'
}

# $1 = target $2 = focus $3 = level
function shrink {
    ARG='-O'
    if [ "$2" = "size" ]; then
        if [ "$3" = "aggro" ]; then ARG="${ARG}z"
        else ARG="${ARG}s"
        fi
    else
        if [ "$3" = "aggro" ]; then ARG="${ARG}3"
        fi
    fi COMMAND="wasm-opt $ARG -o $1  $WASM"
    echo $COMMAND
    eval $COMMAND
}

function choose_smaller {
    NORMAL='_normal'
    AGGRO='_aggressve'
    NORMAL_TARGET="${PKGDIR}/${BINARY}${NORMAL}.wasm"
    AGGRO_TARGET="${PKGDIR}/${BINARY}${AGGRO}.wasm"
    shrink $NORMAL_TARGET $2 $3
    NORMAL_SIZE="$(eval extract_size $NORMAL_TARGET)"
    shrink $AGGRO_TARGET $2 $3
    AGGRO_SIZE="$(eval extract_size $AGGRO_TARGET)"
    if [ $NORMAL_SIZE -lt $AGGRO_SIZE ]; then echo "Normal settings smaller, saving..."; mv $NORMAL_TARGET $WASM; rm $AGGRO_TARGET;
    else echo "Aggressive settings smaller, saving..."; mv $AGGRO_TARGET $WASM; rm $NORMAL_TARGET;
    fi
}

# parse args
for i in "$@"
do
case $i in
    -f=*|--focus=*)
    FOCUS="${i#*=}"
    shift
    ;;
    -l=*|--level=*)
    LEVEL="${i#*=}"
    shift
    ;;
    *)
    # unknown option
    ;;
esac
done
# last line is target, non-opt, no equals sign
if [ -n $1 ]; then TARGET=$1
fi echo_size $WASM
if [ -z $FOCUS ]; then FOCUS_STR='speed'
else FOCUS_STR=$FOCUS
fi echo "Shrinking, optimizing for ${FOCUS_STR}."
if [ "$LEVEL" = "aggro" ]; then echo "Using aggressive optimizations."
fi
if [ "$FOCUS" = "size" ]; then choose_smaller $1
else shrink $WASM $FOCUS $LEVEL
fi echo_size $WASM

exit 
```

Enter fullscreen mode Exit fullscreen mode

还有一个 Makefile 文件可以帮我调用它:

```
.PHONY: all clean help

RUSTCLEAN=cargo clean
RUST=wasm-pack build
PKGDIR=pkg
EXEC=fivedice_bg.wasm
OPT=./shrink-wasm.sh -f=speed -l=aggro

all: $(PKGDIR)/$(EXEC)
    $(OPT)

$(PKGDIR)/$(EXEC):
    $(RUST)

clean:
    $(RUSTCLEAN)

help:
    @echo "Usage: make {all|clean|help}" 1>&2 && false 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将适当的参数传递给`wasm-opt`,包括大小或速度，以及攻击性或正常。如果您选择大小，它会以积极或不积极的方式运行，并保存两者中较小的一个。要调整它，请在 makefile 的 OPT 行中设置选项。看起来我现在应该可以从这个设置中获得一些收益了。

这个项目在 bash 脚本中编写了`extract_size`函数。这个电话是我的第一个解决方案。然后，出于某种奇怪的原因，我决定尝试在没有调用或 subshell 的情况下使用字符串替换，比如参数匹配之类的。真是浪费了一个早上。我敢肯定有一个简单的解决办法摆在我面前，但我没有找到，即使我有，也不会使*有什么不同*。为什么我们要这样对待自己？

## 调试

与其说这是一个兔子洞，不如说是一条绕过它们的路。

使用[控制台 _ 错误 _ 恐慌 _ 钩子](https://github.com/rustwasm/console_error_panic_hook)。有了它，当你的模块死机时，你会得到实际有用的错误输出到浏览器控制台，而不仅仅是“无法执行”。这显然是一个进步。

另外，`wasm-pack build`默认运行发布版本，没有调试符号。调试时，使用`wasm-pack build --debug`或将`debug = true`添加到您的`Cargo.toml`中。现在你的错误实际上会有一个被触发的 Rust 函数的名字，而不是`webassembly[37]`或者其他一些无意义的东西。我很久没有意识到这一点，并认为调试 WASM 应用程序就是这样。It *不一定要那样*。

## 敬请期待

即使过了这么久....另一方面，*的事情做了*的事情，我的测试小部件的小网格被准确地画到了画布上，所以我把它记为成功的一周。接下来，Ben 试图提供一个过程化的宏风格的 DSL！这应该是一个*乱*，你不会想错过的。

*Gary Bendig 在 Unsplash 上拍摄的照片*