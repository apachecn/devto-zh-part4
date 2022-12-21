# 借用检查器和内存管理

> 原文：<https://dev.to/strottos/learn-rust-the-hard-bits-3d26>

# 简介

我已经断断续续玩了大约 12 个月的 Rust，我学到的一件事是我喜欢 Rust。至于我是否能证明 Rust 是我在日常工作中乐于使用的产品，那就是另一回事了(不过我认为这非常接近)。但是我希望有一天，我是说真的希望。

所以为什么 Rust 很棒，值得你花时间。嗯，最近微软说他们对它感兴趣(见[此处](https://msrc-blog.microsoft.com/2019/07/22/why-rust-for-safe-systems-programming/))，它在 AWS 和 Azure 的幕后使用(见[AWS 的这个链接](https://github.com/firecracker-microvm/firecracker)和[Azure 的这个链接](https://github.com/Azure/iotedge))，它可以做绿色线程([https://tokio.rs/](https://tokio.rs/))，性能令人难以置信(可与 C/C++相比，来自各种互联网资源和经验)。本质上它本身就值得几篇文章，谷歌“为什么开发者爱 Rust”。

所以，为什么我说我不能证明离开像 Java、JavaScript 或 Python 这样的高级语言是正确的。几个原因:

*   没有拔拳，很难上手。一个问题是，我们真的能找到开发人员来开发和维护现有的 Rust，而不用支付一大笔钱，因为他们很稀缺？我希望将来会有，但是现在可能更容易找到 Java 或 JavaScript 开发人员。
*   在我看来，单元测试并不好。他们可能并没有像哲学上所说的“如果编译成功，就可能成功”那样关注这个问题。老实说，即使这是真的(事实并非如此，即使对于 Rust 来说也是如此)，我仍然想正确地进行单元测试。这并不可怕，它能做到，事实上它有相当不错的基础。但是当我将它与 JavaScript 或 Python 进行比较时...它严重缺少像样的嘲讽库。作为附录，我发现[这个](https://docs.rs/crate/mockall/0.2.1)自从写了这个，也许这符合我的要求，我还没有完全检查。
*   对于云技术，我们确实还处于起步阶段。我没怎么玩过这个，但快速搜索一下谷歌就会发现情况确实如此。虽然试图让 Java/Node/Python 的东西在 AWS 或 Azure 上运行，但周围有大量的东西(通常由供应商创建)，但 Rust，它只是还没有引起人们的足够重视。我再次希望并认为在未来几年里会更好。我注意到我们现在可以在 Rust 中编写无服务器的东西，这很好，所以这种东西肯定会有发展。
*   绿色线程仍然需要努力。与 Go here 相比，虽然我更喜欢 Rust，但它给了我非常好的绿色线程。在 Rust 中，同样的事情更难实现。我有点喜欢 Rust 没有把这种东西编码到它的运行时中，Rust 可以用 Tokio 机箱做到这一点，这很好，但很难。我仍然推荐使用 Tokio，有一些严肃的东西是用它写的，但对于胆小的人来说，它不是。我相信这将随着时间的推移而显著改善，甚至不一定是从现在开始那么久，这不是一个真正公平的抱怨，因为核心团队现在只是得到未来和异步/等待的正确排序，但我仍然想要它。然而，普通的多线程是非常棒的，这在很大程度上是本文的结果。

所以我在想我能做些什么来帮助解决上述问题。我是说我想用这个东西，它太棒了。对于任何低级系统编程的东西，我会毫不犹豫地使用它，但我想每天都用它来构建云中的微服务。我决定也许我可以写一系列关于学习 Rust 的挑战部分的文章作为教程，给人们一个起点。现在有很多关于这类事情的文章，我将在这里引用[这个真正优秀的系列](https://www.snoyman.com/blog/2018/10/introducing-rust-crash-course)，但是这个引用 Haskell 相似性太多了，我不喜欢。我不保证我的真的更好，但我正试图将它瞄准一个稍微低一点的水平，理想情况下，我想将它瞄准那些没有编程经验的人，但合理地说，没有一点经验是不太可能的，然而我想那些有一些基本编程知识的人可以从这些文章中有所收获。假设您大概知道一些语言中的函数、模块、字符串、整数和内存是什么(不一定是 Rust ),但是只要有基本的掌握就足以让这篇文章有意义。

我觉得 Rust 最初的主要绊脚石是借用检查器和内存管理，所以这将是第一篇文章的主题。这在很多方面都是非常不幸的，因为借用检查器是关于 Rust 的最好的东西。我给那些刚接触 Rust 的人的建议是，不要害怕它:它爱你，它希望你写出没有错误的代码，它希望帮助你。虽然 Rust 有一个困难而陡峭的学习曲线，但它给出了非常有用的编译时错误(正如我在本文中试图说服的那样)。我和那些因为借款检查器而放弃 Rust 的人谈过话，如果这是真的，那么这篇文章就是为你写的。请在读完这篇文章后再试一次，如果你仍然有不明白的地方，请稍后问我。

我还注意到，阅读有关铁锈的知识比以前好多了。它确实有着超级难用的名声，但 Rust 社区很棒，他们倾听问题并努力改进，他们做到了(例如，见[这篇关于非词法生存期的文章](https://doc.rust-lang.org/edition-guide/rust-2018/ownership-and-lifetimes/non-lexical-lifetimes.html))。虽然绿色线程还没有我希望的那么好，但我知道他们正在超级努力地改进它，我很肯定不久就会这样。

这将是一系列关于 Rust 的困难部分的文章，但这并不意味着详尽无遗，我不会试图取代 Rust 的书，我也永远不会，Rust 的书很棒，但它是一个更好的参考，而不是一个教程。我强烈建议你也试着去学习锈书，你会需要它的。如果这篇文章令人困惑，你可以阅读铁锈书的前三章(简单的章节:)。

虽然生锈很难，但它是可行的，我保证，我会尽我所能握住你的手(当然是比喻)，引导你通过最初的阶段。这篇文章中没有什么是火箭科学，甚至没有那么难，但是 Rust 中有很多东西需要学习，甚至对于有经验的程序员来说，你可能需要从头开始学习。这些文章应该让你在这里做好准备，并且应该有你需要的一切(一旦它们都写好了，但是对于有经验的程序员来说，仅仅这一篇就足够了)。

不过，归根结底，如果你在寻找一种简单的编程语言，Rust 绝对不是。我相信 Rust 是低级 C/C++和高级语言如 Python 或 Java 中复杂程序的强有力的竞争者。如果你想要一个快速简单的程序和/或快速原型，Rust 不是你所追求的。

建议您尝试运行下面的程序，并亲自进行试验。如果你不想安装 Rust，试试 Rust 游乐场。还会有类似`warning: unused variable: i`的编译器警告，这在我写的产品中是不允许的，在教程中我不想陷入这种困境，但基本上如果我使用它，这就会消失。它告诉我，如果我去掉这个变量，程序不变，它试图再次帮助我。你应该总是修复编译器警告，总是，总是，总是。

最后，我并不假装自己是铁锈专家，但我可能已经解决了足够多的基本问题，现在已经有点超越初学者了。我很想从任何农村人那里得到一些反馈，关于他们的想法，请告诉我。

# 借用检查器

这篇文章的主要内容都是关于借书检查。我们将在以后的文章中讨论更多的事情，但是现在我们需要首先完全掌握这个问题。一旦你理解了借书检查器，你就相当好地理解了 Rust 和其他许多以前可能没有的东西会在这之后到位。我们也将介绍一些语法，但主要是关于学习 Rust 的最大障碍的理论文章(在我看来)。

## 所有权

好了，我们开始吧。第一个话题是所有权，而且永远都是。重要的是，学习和消化这是如何工作的。一旦你这么做了，我保证你就可以用 Rust 做很酷的事情了。

内存管理基本上是这里的主题。如果你以前用 C 或 C++编程过，你可能知道内存管理会带来的痛苦。如果你用更高级的语言编程，你可能知道的更少，因为它会为你处理它，但这是以垃圾收集器为代价的。

你可能从来没有关心过垃圾收集器，它会让你的速度变慢，而且它只解决了释放内存的问题，Rust 做的更多，但是我稍后会讲到。但是真正的垃圾收集器是很棒的，我更喜欢它而不是 C/C++，在 C/c++中，内存管理只会妨碍你的工作。Rust 本质上介于两者之间，结果在很多方面变得更好。

如果你以前没有用 C/C++编程过，那么如果你想以动态的方式保留内存(现在我需要一个 10 个元素的列表，但很快我就需要 12 个，诸如此类)，你需要保留内存，并在完成后释放它。例如，要在堆上为一个整数保留内存(基本上是动态内存存储，现在你真的不需要担心这个)我需要告诉 C++如下创建它(不要担心 C++的语法，本文不需要):

```
int* i = new int;
*i = 1; 
```

Enter fullscreen mode Exit fullscreen mode

当我完成后，我需要做:

```
delete i; 
```

Enter fullscreen mode Exit fullscreen mode

如果我忘记了后者或者做了两次，那么不好的事情就会发生，特别是在后一种情况下。就这么简单，标准说如果我释放两次，基本上什么事都可能发生。我在 Linux 上试着释放了两次，什么都没发生，在 Mac 上我得到了如下结果(你的结果可能会有所不同):

```
➜  ./a.out
a.out(67865,0x1047a05c0) malloc: *** error for object 0x7fa4e8c02ac0: pointer being freed was not allocated
a.out(67865,0x1047a05c0) malloc: *** set a breakpoint in malloc_error_break to debug
[1]    67865 abort      ./a.out 
```

Enter fullscreen mode Exit fullscreen mode

在 C++中，你需要考虑不同类型的内存(堆栈和堆对那些熟悉的人来说)，在 Rust 中就不那么需要了。栈和堆仍然存在，但老实说，首先你不需要担心这么多。如果你越陷越深，也许会，但老实说，我还不在乎。也没有垃圾收集，但它和有垃圾收集时一样安全，更安全。我无法从 Rust 获得上述运行时错误，我已经试过了(即使在不安全的 Rust 中，我们在这里没有触及，我也无法获得它。远离不安全的铁锈首先，有龙)。我得到了编译器错误，但没有运行时错误，当然编译器错误更好，因为我更快地得到它们，我知道我一定破坏了什么。

原因与所有权有关，在 Rust 中，所有东西都必须被某个东西“拥有”。一旦它不再属于某个东西，我们的记忆就会被清除。就好像编译器去给我加了 C++里的`delete`语句，(那有多酷，我不需要记住)。但是被某物“拥有”是什么意思。

这里举个很简单的例子:

```
fn main() {
    let i: u32 = 32;
} 
```

Enter fullscreen mode Exit fullscreen mode

那是一个最终毫无意义的 Rust 程序。这将变量`i`声明为类型`u32`(无符号 32 位整数，Rust 的类型系统比 C/C++的*方式*更合理)并将其设置为值`32`。Rust 的编译器注意到，当该函数返回时，`i`不再需要，因此它执行任何必要的清理(从技术上讲，这在 C/C++中也可以工作，因为它在堆栈上，但如果它在 Rust 的堆上，这也可以工作)。本质上你不需要担心内存管理，Rust 已经帮你搞定了。

同样，我们可以考虑下面的程序

```
fn main() {                        // Scope of `main` starts here
    let i: u32 = 32;
    {                              // A new inner scope starts here
        let j: u32 = i + 5;
        println!("j is: {}", j);
    }                              // The inner scope ends here and `j` is dropped
}                                  // The `main` scope ends here and `i` is dropped 
```

Enter fullscreen mode Exit fullscreen mode

运行这个程序会打印出行`j is: 37`。我们不会彻底解释`println`语句，但本质上它打印给定的字符串，每次我们在该字符串中有一个`{}`时，我们可以替换一个变量值。这里的`{}`被替换为`j`的值。

这里我们在中间用括号创建了另一个作用域。如果你不熟悉其他 C 语言中类似的作用域，基本上作用域是由这些花括号创建的，左花括号`{`和右花括号`}`之间的任何东西都是作用域。在这种情况下，我在第 1 行和第 7 行之间创建了一个`main`函数的作用域，这个作用域是在第 3 行定义了结束于第 6 行的`i`之后创建的。这个内部作用域中的所有东西都可以访问它上面作用域中的变量，但是`main`作用域本身不能访问中间的东西。例如，内部范围有`i`和`j`，而`main`范围只有`i`。像这样的内部作用域在 Rust 中并不少见(在这个意义上有点像 JavaScript)。我们可以用这样的作用域来限制我们需要变量的时间。你也可以通过在内部作用域中调用相同的名字来隐藏变量，例如这里的。

现在回到代码示例，和之前一样，一旦 main 返回，我们的`i`就会超出范围。然而，现在我们有了另一个内部大括号范围。在这个范围内，我们创建了一个新的`u32`变量`j`，我们还可以访问`i`，这样我们就可以将`j`设置为等于`i + 5`，或者实际上等于`37`。现在，一旦我们到达内部作用域的末尾，也就是说，一旦程序越过了第 6 行的右花括号，我们就不能再访问`j`。例如考虑下面的程序:

```
fn main() {
    let i: u32 = 32;
    {
        let j: u32 = i + 5;
    }
    println!("j is: {}", j);
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们编译它时，我们得到:

```
➜ rustc simple_rust_program.rs
error[E0425]: cannot find value `j` in this scope
 --> simple_rust_program.rs:6:26
  |
6 |     println!("j is: {}", j);
  |                          ^ help: a local variable with a similar name exists: `i`

error: aborting due to previous error

For more information about this error, try `rustc --explain E0425`. 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们花点时间来看看这个错误输出有多棒。这正好告诉了我需要知道的东西，`j`在第 6 行并不存在。它甚至告诉我一个命令来更全面地解释错误，它认为也许我指的是`i`。我确切地知道它在抱怨什么，它真的试图帮我解决它。我真的很喜欢这个借书检查器，它真的很有帮助(我建议试着记住这一点)。

我们已经讨论了所有权的含义，这很简单，有什么大不了的？让我们进入下一个话题，在这个话题中我们会传递一些东西。

## 转移所有权

当然有时我们想要传递变量，例如在函数调用中，我们可能想要传递一个变量作为参数。现在我们有三种方法(这三种方法在 Rust 中经常出现)来实现这一点，转移所有权，通过引用传递或者通过可变引用传递。我们现在讨论所有权转移(其他两个将在下面的借款部分讨论)。

从语法上来说，转让所有权真的很容易，正如你所期望的那样。下面的程序显示了被所有权传递的东西:

```
fn take_ownership(a: String) {
    println!("I have a: {}", a);                 // and a is now owned by take_ownership
}

fn main() {
    let a: String = "Test String".to_string();   // a is owned by main
    take_ownership(a);                           // Now we pass a to take_ownership
    //println!("I try to print a: {}", a);
    // If I uncomment this line above then I get an error because this scope no longer owns a
} 
```

Enter fullscreen mode Exit fullscreen mode

简单解释一下，`.to_string()`创建了一个我们可以从字符串文字中改变出来的字符串。详见本书第 8.2 节。

所以如果你像这样传递所有权，它就脱离了原始函数，任何时候只有一个东西可以拥有一个变量。当然，除非你把它传回来。我可以把上面的修改如下:

```
fn take_ownership(a: String) -> String {
    println!("I have a: {}", a);
    return a;
}

fn main() {
    let a: String = "Test String".to_string();
    let a = take_ownership(a);
    println!("a: {}", a);            // We've passed a back so we're OK now
} 
```

Enter fullscreen mode Exit fullscreen mode

作为一个题外话，上面的程序并没有以一种类似 Rust 的方式返回。等效地，这个函数可以写成:

```
fn take_ownership(a: String) -> String {
    println!("I have a: {}", a);
    a
} 
```

Enter fullscreen mode Exit fullscreen mode

这完全是一回事。因为最后一行没有分号 Rust 知道这是一个返回。有一个解释，涉及到语句和表达式，但我不想在这里深究，只是要注意，如果你看到这个，这只是一个返回。

很简单，对吧。不，不完全是。我们很快会谈到一个复杂的问题，但我们现在首先讨论可变性。

## 易变性

下一步是讨论可变性，或者你可能更喜欢考虑非常数变量或者你可以改变的变量(或者仅仅是变量，当然我可以改变一个变量或者它不变，随它去)。本质上，默认情况下，Rust 中的所有东西都是不可变的，也就是说，它是不变的，不能改变它的值。如果我有下面的程序:

```
fn main() {
    let i: u32 = 1;
    i = 2;
    println!("i is: {}", i);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我尝试，我会得到如下结果:

```
error[E0384]: cannot assign twice to immutable variable `i`
 --> src/main.rs:3:5
  |
2 |     let i: u32 = 1;
  |         -
  |         |
  |         first assignment to `i`
  |         help: make this binding mutable: `mut i`
3 |     i = 2;
  |     ^^^^^ cannot assign twice to immutable variable

error: aborting due to previous error

For more information about this error, try `rustc --explain E0384`. 
```

Enter fullscreen mode Exit fullscreen mode

很清楚问题出在哪里，对吧？但是我该怎么做呢？实际上非常简单，我们可以通过简单地添加关键字`mut`来创建一个可变变量(一个可以改变的变量),如下面的代码所示。事实上，它在错误中告诉了我该怎么做。

```
fn main() {
    let mut i: u32 = 1;        // Added mut, now I can change it
    i = 2;
    println!("i is: {}", i);
} 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，我可以把不可变变量的所有权传递给可变变量，这绝对没问题。例如:

```
fn take_ownership_and_pass_back(mut a: String) -> String {
    a = a + "2";
    a
}

fn main() {
    let a: String = "TEST".to_string();
    let a = take_ownership_and_pass_back(a);
    println!("a: {}", a);
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我把`a`从不可变改为可变，这样就可以了。如果我运行这个，我会得到一行`a: TEST2`。但是`a`是不可改变的吗？Rust 不能保证你不会故意把它改成 mutable，但是你必须试着去改变它。它不是试图告诉你该做什么，而是试图让你停止改变你从未想过的事情。一般来说，不可变变量是一个很好的东西，这也是为什么它们是默认的。真正的要点是，我可以限制某个东西需要可变的时间，从而限制可以改变值的地方(并根据函数式编程创建 bug，这是我们不希望的)。

另一个有趣的代码片段如下:

```
fn main() {
    let mut s = "TEST1".to_string();
    let t = s;
    // Can't do the following as s is now moved
    //println!("s={}", s);
    s = "TEST2".to_string();
    println!("s={}, t={}", s, t);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了一个值为`"TEST1"`的可变字符串`s`。接下来我们让`t`获得这个字符串的所有权，此时我不能再从`s`中读取，因为它已经把它的值转移到别处，并且是空的。然而，我可以重新分配给`s`，因为它是可变的，然后从它那里读取，因为它现在已经初始化了，这是绝对没问题的，如上面运行所示。我得到输出`s=TEST2, t=TEST1`。

这里还有另一个我们不允许做的常见问题，让我们看看下面的内容。

```
fn main() {
    let x = "TEST".to_string();
    for i in 0..10 {
        let y = x + &i.to_string();
        println!("{}", y);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们尝试循环 10 次，并尝试通过连接整数`"TEST"`和`i`构建一个字符串。不幸的是，我们有一个问题，你能看出为什么不编译吗？

如果我们试图编译它，我们会得到下面的错误:

```
error[E0382]: use of moved value: `x`
 --> src/main.rs:4:17
  |
2 |     let x = "TEST".to_string();
  |         - move occurs because `x` has type `std::string::String`, which does not implement the `Copy` trait
3 |     for i in 0..10 {
4 |         let y = x + &i.to_string();
  |                 ^ value moved here, in previous iteration of loop

error: aborting due to previous error

For more information about this error, try `rustc --explain E0382`. 
```

Enter fullscreen mode Exit fullscreen mode

因为`x`在第一个循环中被移动，然后在这个循环结束时移出了作用域，`x`在一个循环后被丢弃，因此我们在第二个循环中无法访问`x`。编译器认识到这一点，并抛出上述错误。有各种各样的方法可以绕过这一点，包括以下最初令人惊讶的(尽管事后认为合理)方法:

```
fn main() {
    let mut x = "TEST".to_string();
    for i in 0..10 {
        let y = x + &i.to_string();
        println!("{}", y);
        x = "TEST".to_string();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在试图把复制和克隆作为解决这类问题的一种方法。使用后面的引用在这里也可以。

## 复制和克隆

我之前说过，你可以转移一个变量的所有权，我向你展示了怎么做。我也告诉过你有个小问题，就是这个。让我们来看看下面的例子:

```
fn main() {
    let i: u32 = 123;

    {
        let mut j = i;     // <-- If i didn't satisfy copy this would be a move

        j = 124;

        println!("Not finished and j is: {}", j);
    }

    println!("Finished and i is: {}", i);
    // ^^ And if i didn't satisfy copy and hence moved above I wouldn't be able to read i here
} 
```

Enter fullscreen mode Exit fullscreen mode

按照我之前提到的逻辑，当`j`被创建时，这里的`i`应该被移动到内部块的`j`中，但是我不能再在最终的 println 中访问`i`，因为它已经不在了。然而我运行它，它编译成功，我得到:

```
Not finished and j is: 124
Finished and i is: 123 
```

Enter fullscreen mode Exit fullscreen mode

所以我可以看到我已经将`i`的值复制到了`j`中，并没有如我所想的那样移动任何东西，并且我可以愉快地继续使用未改变的`i`。Rust 帮助了我，因为它知道`u32`满足了`Copy`的特征。见[此处](https://doc.rust-lang.org/std/marker/trait.Copy.html)了解更多关于`Copy`特征的细节，但我会简单解释一下这意味着什么。如果你遇到过其他语言的接口，traits 有点像接口，现在读“interface”而不是 trait，这就足够了。本质上，特质是可以满足的东西，然后事物可以在此基础上采取相应的行动。在这种情况下，任何满足`Copy`特征的东西，编译器都知道它的值被复制而不是被移动。

另一方面，如果我把`i`变成一个字符串`s`，如下面的代码所示:

```
fn main() {
    let s: String = "123".to_string();

    {
        let mut t = s;

        t = "124".to_string();

        println!("Not finished and t is: {}", t);
    }

    println!("Finished and s is: {}", s);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我得到如下错误:

```
error[E0382]: borrow of moved value: `s`
  --> src/main.rs:12:39
   |
2  |     let s: String = "123".to_string();
   |         - move occurs because `s` has type `std::string::String`, which does not implement the `Copy` trait
...
5  |         let mut t = s;
   |                     - value moved here
...
12 |     println!("Finished and s is: {}", s);
   |                                       ^ value borrowed here after move

error: aborting due to previous error

For more information about this error, try `rustc --explain E0382`. 
```

Enter fullscreen mode Exit fullscreen mode

再来看看惊人的输出(`gcc`或者`clang`吃了你的心)。这确切地告诉了我我需要知道的，`s`在第 2 行这里被创建，在第 5 行这里被移动，但是现在我试图在第 12 行使用它，但是我不被允许。

所以误差很大，但是我现在做什么呢？你可以做几件事。如果你对这个副本满意，你可以简单地添加一个`.clone()`到这个行，改变`t`的值，比如:

```
 t = "124".to_string().clone(); 
```

Enter fullscreen mode Exit fullscreen mode

现在 Rust 复制它，因为我已经告诉它。无论被克隆的是什么，都需要满足`Clone`特征，大多数东西倾向于但不总是这样。克隆意味着该变量的值的整个内存被复制到其他地方的内存中，如果某个东西可能特别大，你可能不希望它满足这一特性，所以你不能意外地这样做。

但基本上这意味着每当我们像这样传递一个`u32`时，它复制数据而不是移动数据。所以通过复制它，我得到了一个全新的变量，它和原来的变量完全无关。Rust 并不是唯一这样做的语言，我见过一些这样做的语言，比如 Python。默认情况下，通常是固定大小的小东西(主要是布尔值和数字)。如果你曾经发现像这样的东西在看起来不应该的时候工作了，那么很可能只是 Rust 复制了它并试图帮助你。当然，如果发生这种情况，你改变了第二个变量，第一个是不变的，需要小心。

现在，我已经介绍了你开始拥有所有权所需要知道的大部分内容。但是我们不会走得很远，除非它是一个非常简单的程序(在这种情况下，我告诉过你不要使用 Rust)。有时我们只是想借一些我们现在就要看的东西。

## 借款

现在我们知道了如何移动和复制内容，但是如果我不想移动或复制它呢？我想访问它，我想看看里面有什么，但它不是我的，或者我想改变一些东西，但它不是我的。你有如下几个选择:

*   如果我只想读取它，我可以创建一个对它的“引用”，我可以拥有尽可能多的引用(只要不存在来自下一个的“可变引用”)。
*   如果我也想更新它，我可以创建一个“可变引用”,但是我只能有一个，句号。如果我有一个“可变引用”，编译器将阻止我创建任何其他可变或不可变的“引用”(默认情况下“引用”是不可变的，因为实际上一切都在 Rust 中)。
*   我可以为它创建“智能指针”。我将在以后的文章中讨论这一点，这可能是最后的努力，但有时是必要的。

那么什么是引用和可变引用呢？我没有传递所有权，而是传递了一个引用(如果你熟悉的话，有点像 C 语言中的指针)。引用本质上持有一个内存地址，它指向原始地址所在的位置。当你获取一个变量`b`的引用`a`时，你不去管`a`的所有权，而是对`b`说“你可以指向这里，看一看`a`，或者甚至你可以指向`a`，如果它是一个我获取的可变引用，就修改它，但是`a`仍然属于我”。我们现在来看一个例子。

对于上面的代码示例，我真的希望能够在另一个范围内改变它，而不需要获得所有权，可能是在另一个函数或其他东西中。嗯，我可以这样做，这很有效:

```
fn main() {
    let mut s: String = "123".to_string();

    {
        let t = &mut s;

        *t = "124".to_string();

        println!("Not finished and t is: {}", t);
    }

    println!("Finished and s is: {}", s);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我运行这个，我会得到我想要的:

```
Not finished and t is: 124
Finished and s is: 124 
```

Enter fullscreen mode Exit fullscreen mode

那么我在这里做了什么？首先，我不得不把原来的字符串改成可变的，因为我要改变它，以前我不需要这样做，因为我不想改变原来的字符串。我还通过做`let t = &mut s;`创建了一个可变引用`t`。这个`&`是我获取引用的语法，`mut`暗示我想要一个可变的引用，我可以改变它。如果我只是写了`let t = &s;`，那么`t`将只是一个参考，我只能阅读它。当我想对原始地址执行读或写(如果它是可变的)时，我需要取消引用它，我通过写`*t`来这样做，这里的`*`意味着这个地址指向的值。如果我像你想的那样写了`t = "124".to_string()`，我会得到下面的错误:

```
 --> src/main.rs:7:13
  |
7 |         t = "124".to_string();
  |             ^^^^^^^^^^^^^^^^^ expected mutable reference, found struct `std::string::String`
  |
  = note: expected type `&mut std::string::String`
             found type `std::string::String`
help: consider dereferencing here to assign to the mutable borrowed piece of memory
  |
7 |         *t = "124".to_string();
  |         ^^

error: aborting due to previous error

For more information about this error, try `rustc --explain E0308`. 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，我将不再滔滔不绝地谈论 Rust 的错误报告，但是看，告诉我如何修复它。

所以我们现在从上面理解了解引用，任何以前看过 Rust 的人可能会想“我在大多数情况下从来不这么做”。很对，我刚才骗了你，对不起。事实是，谢天谢地，Rust 很聪明。在明确的情况下，Rust 编译器知道我明确打算取消引用的地方，并自动添加取消引用操作符。所以我把`*`放在`*t = "124".to_string();`行中去引用`t`，因为正如我们上面看到的，Rust 不知道我不想直接赋值给`t`。但是在我打印变量的下一行，我可以看到我不需要去引用，这样做没有害处，但是如果我不这样做，编译器知道“他显然是故意的”。事实上，据我所知，大多数 Rust 开发者也不会费心把`*`放到这条线上。

我之前说过我只能接受一个可变引用，如果我有一个，就不能接受任何其他引用。在创建了不可变引用之后，让我们尝试创建另一个引用，看看会发生什么。我们会得到类似下面的错误:

```
error[E0502]: cannot borrow `s` as immutable because it is also borrowed as mutable
 --> src/main.rs:6:17
  |
5 |         let t = &mut s;
  |                 ------ mutable borrow occurs here
6 |         let u = &s;
  |                 ^^ immutable borrow occurs here
7 |
8 |         *t = "124".to_string();
  |         -- mutable borrow later used here

error: aborting due to previous error

For more information about this error, try `rustc --explain E0502`. 
```

Enter fullscreen mode Exit fullscreen mode

这是因为正如我之前暗示的，如果我在一个可变引用存在的情况下试图创建任何其他引用，Rust 将会阻止我。原因是强制执行安全的多线程编程，竞争条件一点也不好玩，通过强制执行这些规则，它们在很大程度上被消除了。因此，如果一个线程有一个可变引用，可能会改变内容，如果我尝试在另一个线程中读取它，可能会出现数据竞争情况。所以 Rust 绝对保证了这里的安全性，它确保你不能在多线程环境中以理解这类文章为代价把事情弄糟。一旦你解决了本文中的问题，剩下的理论就简单多了。

由于我刚才解释的关于引用的规则，通过创建内部作用域来限制作用域是 Rust 代码中常见的事情。一旦范围发生变化，事情就变得清晰了，这是解决你将会遇到的一些问题的好方法。为改变事物创造一个非常小的范围是一种常见的模式。

类似地，如果我试图在引用存在后创建一个可变引用(我必须使用它，否则 Rust 足够聪明，知道没有问题):

```
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  --> src/main.rs:6:17
   |
5  |         let u = &s;
   |                 -- immutable borrow occurs here
6  |         let t = &mut s;
   |                 ^^^^^^ mutable borrow occurs here
...
11 |         println!("Not finished and u is: {}", u);
   |                                               - immutable borrow later used here

error: aborting due to previous error

For more information about this error, try `rustc --explain E0502`. 
```

Enter fullscreen mode Exit fullscreen mode

在我早期与 Rust 打交道时，我经常想知道，如果我引用了一个可变变量，而这个可变变量开始改变，这东西怎么会安全呢？我当时应该测试一下，但是我只是感到困惑。我的意思是，考虑以下程序:

```
fn main() {
    let mut x = "123".to_string();
    let y = &x;
    x += "456";
    println!("{}", y);
} 
```

Enter fullscreen mode Exit fullscreen mode

我不能在引用后面加上对`x`的可变引用，因为那很危险。但是如果`x`仍然可以改变值，那么这又有什么不同呢。这也会导致编译失败，因为当这些引用存在时，Rust 甚至会阻止所有者进行更改。我不确定这是否得到了很好的证明，我当然很久都没有意识到这一点，但这就是它如何确保多线程安全的。如果我编译上面的程序，我会得到:

```
error[E0502]: cannot borrow `x` as mutable because it is also borrowed as immutable
 --> src/main.rs:4:5
  |
3 |     let y = &x;
  |             -- immutable borrow occurs here
4 |     x += "456";
  |     ^^^^^^^^^^ mutable borrow occurs here
5 |     println!("{}", y);
  |                    - immutable borrow later used here

error: aborting due to previous error

For more information about this error, try `rustc --explain E0502`. 
```

Enter fullscreen mode Exit fullscreen mode

类似地，以下程序失败:

```
fn main() {
    let mut x = "123".to_string();
    let y = &mut x;
    x += "456";
    println!("{}", y);
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我创建了可变引用，我就只能在它存在的时候用它来改变事情，即使拥有者也不能再改变它了。这次编译器错误:

```
error[E0499]: cannot borrow `x` as mutable more than once at a time
 --> src/main.rs:4:5
  |
3 |     let y = &mut x;
  |             ------ first mutable borrow occurs here
4 |     x += "456";
  |     ^ second mutable borrow occurs here
5 |     println!("{}", y);
  |                    - first borrow later used here

error: aborting due to previous error

For more information about this error, try `rustc --explain E0499`. 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，铁锈将确保我们的安全:

*   Rust 将确保如果不止一个东西可以读取一个变量，那么没有任何东西可以改变这个变量。
*   Rust 将确保如果某个东西能够改变一个变量(可变变量或可变引用),那么只有那个东西能够读写那个变量。
*   上述两种情况的反例是，如果 Rust 足够聪明，知道它无论如何都是安全的(这种情况偶尔会发生)。例如，当我试图在同一个作用域中使用一个引用和一个可变引用时，直到我使用了这两个引用才发现这不是问题。
*   这些检查都是在编译器级别强制执行的，本文中的任何内容都不是在运行时完成的，因此如果您做了一些有潜在危险的事情，您可以更快地知道。

这就涵盖了借款检查的基本内容。简单对吗？当然，还有更多，我将在本系列的后续文章中介绍。希望你现在可以读到《T1》这本书的第 10 章，并且希望你可以读到第 10 章，而不会发现太有挑战性的内容。在本系列接下来的几篇文章中，我将尝试介绍结构、选项、线程、智能指针、生存期和闭包，以及如何使用借用检查器处理这些事情。敬请关注。