# 博文:第一个形:“乘”

> 原文：<https://dev.to/jonasbn/blogpost-first-kata-multiply-81o>

好的，正如我在[的前一篇文章](https://dev.to/jonasbn/blog-post-46gm)中承诺的，额外的锈帖可能正在准备中，我也提到了 Codewars.com 的[和我练习用的招式。](https://www.codewars.com/)

如果你对[Codewars.com](https://www.codewars.com/)感兴趣，这篇帖子(希望后续帖子)将包含**剧透**——现在你已经被警告了！

我写这些主要是为了我自己的教育和参考，因为如果我把它们记下来，似乎会更好，如果你觉得这有用，这是额外的收获——就这样吧。

第一个形:“[乘](https://www.codewars.com/kata/50654ddff44f800200000004)”。形问题被公式化为:

> 代码无法正常执行。试着找出原因。

原代码，看起来如下:

```
fn multiply(a:i32, b:i32) {
  a * b
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是我的解决方案，解决方案本身并不特别有趣，但我可以反思我从我的解决方案中学到的一些东西(以及可能导致工作解决方案的失败),我可以用我的方法来解决形。

```
fn main () {
    let c = multiply(8, 8);
    println!("{}", c);
}

fn multiply(a:i32, b:i32) -> i32 {
  a * b
} 
```

Enter fullscreen mode Exit fullscreen mode

主函数只是为了让您可以从命令行运行它，所以项目是使用`cargo`生成的，如下所示:

```
$ cargo new --bin multiply
     Created binary (application) `multiply` package 
```

Enter fullscreen mode Exit fullscreen mode

这在包含`main`函数的目录`multiply`中生成了`src/main.rs`文件。

第一行:`fn main() {`

仔细阅读[对 Rust 程序](https://doc.rust-lang.org/stable/book/ch01-02-hello-world.html)的剖析。

> main 函数是特殊的:它总是每个可执行 Rust 程序中运行的第一个代码

*   我们没有参数，因此有空括号`()`
*   在花括号`{`之间定义了函数*体*...`}`，我们只看到第一行中的第一个，但另一个紧随其后，

现在我们对 **Rust** 中的函数有了一个概念。如[防锈单](https://cheats.rs/) :
所述

```
fn f() {} 
```

Enter fullscreen mode Exit fullscreen mode

正如您已经注意到的，当使用`cargo`生成应用程序时，您会免费得到一个经典的“hello world”示例:

```
fn main() {
    println!("Hello, world!");
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们跳过`main`函数的内容，看看下一个函数:`multiply`

```
fn multiply(a:i32, b:i32) -> i32 {
  a * b
} 
```

Enter fullscreen mode Exit fullscreen mode

利用我们*新*获得的知识，我们可以读到这个函数被命名为“multiply”:

第 6 行:`fn multiply(a:i32, b:i32) -> i32 {`

它需要参数，因为括号被填充，最后我们观察到一些新的东西:`-> i32`。

先说参数。该函数采用参数:`a`和`b`，如果你仔细阅读代码主体，你会发现，`a`和`b`是我们的乘法操作数。

这两个参数用类型进行了注释:`i32`，这意味着大小为 32 的有符号整数。这里重要的一课是让我明白两件事。

1.  我们函数的参数说明
2.  和类型

正如我在关于学习 Rust 的帖子中所描述的，有很好的资源记录了 **Rust** ，所以如果你想知道关于`i32` [的一些细节，就去查一下](https://doc.rust-lang.org/nightly/std/primitive.i32.html)。

最后对于`-> i32`，它描述了函数的返回值。所以我们都接受类型为`ì32`的参数，并返回`i32`。

第 7 行:`a * b`

这一行使用我们的两个操作数，它们与参数匹配。

在这里我们可以观察到另一个**生锈** *的东西*:隐式返回值，我可以想象一些来自其他编程语言的人会发现这有点特别。隐式返回值是最后一个值。您不必编写隐式返回。

同样的功能也存在于 **Perl** 中，我必须承认我更喜欢显式返回，我是一个*可读性怪胎*，我不是一个隐式和*魔法*的超级粉丝。

所以`a * b`可以写成`return a * b;`，但是因为我专注于学习 **Rust** ，理解和使用惯用的 **Rust** 也是课程的一部分。我将在另一篇博文中回到惯用代码，因为这是我已经反思过的东西(*我需要从我的系统*)。

“乘法”的完整实现如下:

```
fn multiply(a:i32, b:i32) -> i32 {
  a * b
} 
```

Enter fullscreen mode Exit fullscreen mode

当你在[Codewars.com](https://www.codewars.com/)做卡式时，大多数卡式都有单元测试来测试你的解决方案。这个形的原始测试如下。

```
#[test]
fn returns_expected() {
  assert_eq!(multiply(3, 5), 15)
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以做 TDD，写很多测试来观察你的代码是否有效，我鼓励你这么做。

可以使用`cargo`
运行测试套件

```
$ cargo test Compiling multiply v0.1.0 (/Users/jonasbn/develop/github/rust-multiply-kata)
    Finished dev [unoptimized + debuginfo] target(s) in 0.45s
     Running target/debug/deps/multiply-b4e442fac2c38b72

running 1 test
test returns_expected ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out 
```

Enter fullscreen mode Exit fullscreen mode

在这篇文章中，我不会深入讨论测试，因为我们关注的是其他东西，基本的测试结构很简单，你应该能够根据你的需要进行调整，所以请继续阅读。

因为我们正在使用`cargo`，并且我们免费得到了`main`*函数，我们也可以进行手工测试。它只需要对`main`函数做一些小的调整，所以我们可以允许它接受参数，所以我们通过命令行提供参数。*

 *让我们首先使用一个“Hello World”的修改版本，改为同样流行的“greeting”示例。

```
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    if args.len() == 2 {

        let name = &args[1];

        println!("Greetings {} from {}", name, &args[0]);
    } else {
        eprintln!("Usage: greetings «name»");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

给你的 **Rust** 命令行应用程序获取字符串参数是非常基本的，[文档](https://doc.rust-lang.org/book/ch12-01-accepting-command-line-arguments.html)也非常清楚。如果你正在做一些更复杂的事情，我相信有一个箱子可以帮助你。

现在让我们把这个应用到我们的*乘数* :

```
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    if args.len() == 3 {

        let operand1: i32 = args[1].parse().unwrap();
        let operand2: i32 = args[2].parse().unwrap();

        let result = multiply(operand1, operand2);
        println!("{}", result);
    } else {
        eprintln!("Usage: multiply «operand1» «operand2»");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这两者之间有细微的差别，因为我们必须*将参数从字符串转换为整数。*

所以第 8 行和第 9 行是这样做的:`let operand1: i32 = args[1].parse().unwrap();`对于每个操作数。

好了，现在你可以测试你的 **Rust** 命令行应用程序了，如果需要的话。

但是让我们回到正确的软件开发上来，因为手工测试是令人厌倦的和琐碎的(*和容易出错的*)，所以让我们用更多的测试来扩展单元测试套件。

如前所示，一个测试套件看起来如下:

```
#[test]
fn returns_expected() {
  assert_eq!(multiply(3, 5), 15)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以很容易地用一些有趣的场景和极限情况来扩展它，因为一个单一的测试就应该捕捉到这样一个简单函数的本质:

```
#[test]
fn returns_expected() {
  assert_eq!(multiply(3, 5), 15);
  assert_eq!(multiply(3, 0), 0);
  assert_eq!(multiply(0, 5), 0);
  assert_eq!(multiply(-3, -5), 15);
  assert_eq!(multiply(-3, 5), -15);
  assert_eq!(multiply(3, -5), -15);
} 
```

Enter fullscreen mode Exit fullscreen mode

这太不可思议了，看起来好像我们的简单乘数正在如预期的那样工作，但是我想留给你们一个悬而未决的问题，基于我在另一个项目中遇到的一个问题，这个问题也适用于这里:

*   我们的返回值是类型`i32`对吗？
*   这两个操作数的类型都是`i32`对吗？

这意味着我们的计算结果会超过我们的返回值——意味着基本参数会使我们的乘数变得无用。

一个`i32`的最大值可以从**锈**T3】中提取

```
fn main() {
    println!("{}", i32::max_value());
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想用这个简单的例子来糊弄一下，那就去看看操场吧。

`ì32`的最大值是`2147483647`。

如果我们将`2147483647`乘以`2147483647`，我们得到:`4.611686e+18`，这个问题当然也与负的等价项相关(*，这在数学中有术语吗？*)。

如果您想检查它，只需将下面的测试添加到测试套件中，因为将我们的最大值乘以 2 会超过我们的返回类型的最大值。

```
assert_eq!(multiply(2147483647, 2), 4294967294); 
```

Enter fullscreen mode Exit fullscreen mode

现在就这样，我会做更多的记录，因为这是我整理笔记和形的好方法，也许其他人可以从我的笔记中受益，或者受到启发去学习 **Rust** 或者只是在【Codewars.com】T2 上解形。

我知道我跳过了很多东西，我希望你仍然能理解我写的东西，并喜欢它，但我的目标是在其他帖子中涵盖这些东西，在那里它们可能证明更相关或可以成为帖子的焦点。

玩得开心，

jonasbn*