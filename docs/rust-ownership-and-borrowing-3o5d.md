# 信任所有权和借款

> 原文：<https://dev.to/saiumesh/rust-ownership-and-borrowing-3o5d>

在我们谈论 Rust 所有权和借用之前，我想让你知道 [techempower](https://www.techempower.com/benchmarks/#section=data-r18&hw=ph&test=query) 上周发布了第 18 轮结果， **Rust** 在 5 个类别中的 4 个类别中明显占据主导地位，这表明了 Rust 这些年来变得多么强大。

Rust 是一种非常强大的编程语言，但是学习 Rust 并不容易。它有一个陡峭的学习曲线。在 Rust 中，你会发现最难学的东西可能是**所有权和借款**。今天我们将试着学习它的基础知识。

**本文旨在学习 it 基础知识，因此在生产中小心使用这些代码**。

Rust 既没有手动内存管理，也没有垃圾收集器。那么下一个明显的问题就是，Rust 是如何管理内存的💭💭。Rust 最独特的一点来了，所有权和借款。让我们通过一些例子来看看它是如何工作的。

在我们看到任何黑色和蓝色文本之前，我们需要了解两种类型的变量:

1.  **静态分配的**类型
2.  **堆分配**类型 ex:数组(Rust 中的向量)

对于任何静态分配的类型，信任所有权和借用都不适用，但对于堆分配却适用。让我们来看看一些例子。

```
 fn do_something(input: i32) -> i32 {
    input * 2
}

fn main() {
    let number: i32 = 1;
    let result = do_something(number);
    println!("result is {} for number {}", result, number)
} 
```

[在操场上跑](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=a661ed1ed4e2edd0e382883f64659fae)

如果您运行上面的程序，您将得到下面的输出，正如预期的那样。

```
result is 2 for number 1 
```

现在让我们将上面例子中的***【i32】***替换为 ***字符串(堆分配)*** 。

```
fn do_something(input: String) -> String {
    // do something with string
    input
}

fn main() {
    let name: String = String::from("Hello!!");
    let result = do_something(name);
    println!("result is {} for string {}", result, name)
} 
```

[在操场上跑](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=d16d1be6b9b126a5067dd2234bc8fed6)

一旦你运行上述程序，你会遇到第一个 O & B 问题如下。

```
--> src\main.rs:11:52
   |
9  |     let name: String = String::from("Hello!!");
   |         ---- move occurs because `name` has type `std::string::String`, which does not implement the `Copy` trait
10 |     let result = do_something(name);
   |                               ---- value moved here
11 |     println!("result is {} for string {}", result, name)
   |                                                    ^^^^ value borrowed here after move 
```

任何堆分配的变量都有它的作用域，通常这个作用域是它的函数的生存期(ownsership)。但是，如果您将任何堆变量传递给另一个函数，那么不仅是该变量，而且它的作用域也将连同它的生存期一起传递给那个函数(ownsership)。这意味着，一旦 main()被传递给 do_something()，它就不能访问 String 类型的名称。一旦 do_something()执行完毕，字符串 memory 类型的输入变量将被删除。Rust 就是这样维护内存管理的，不需要手动管理。

现在，为了解决上述问题，我们将发送字符串的引用，而不是实际的变量。

```
fn do_something(input: &String) -> String {
    // do something with string
    input.to_string()
}

fn main() {
    let name: String = String::from("Hello!!");
    let result = do_something(&name);
    println!("result is {} for string {}", result, name)
} 
```

[在操场上跑](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=f87567843586206219d2bfe6e7922774)

如果您运行以上程序，您将得到以下输出。

```
result is Hello!! for name Hello!! 
```

rust 中的任何变量都是不可变的，除非另有说明。现在让我们尝试改变输入，看看会发生什么。

```
 fn do_something(input: &String) {
    input.push_str("world!!")
}

fn main() {
    let name: String = String::from("Hello!!");
    do_something(&name);
    println!("result is {} for name", name)
} 
```

[在操场上跑步](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=996ce4ac1573d7b7a3202fe6f9faf82a)

如果您运行以上程序，您将得到以下输出。

```
error[E0596]: cannot borrow `*input` as mutable, as it is behind a `&` reference
 --> src\main.rs:3:5
  |
2 | fn do_something(input: &String) {
  |                        ------- help: consider changing this to be a mutable reference: `&mut std::string::String`
3 |     input.push_str("World!!")
  |     ^^^^^ `input` is a `&` reference, so the data it refers to cannot be borrowed as mutable 
```

因为借用的变量是不可变的，所以我们不能改变它。为了解决这个问题，我们需要让原始变量和传递变量都变得可变。

```
fn do_something(input: &mut String) {
    input.push_str(" World!!")
}

fn main() {
    let mut name: String = String::from("Hello!!");
    do_something(&mut name);
    println!("result is {} for name", name)
} 
```

[在操场上跑](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=ff04818317a5b426e0b026a22f66962b)

如果您运行以上程序，您将得到以下输出。

```
result is Hello!! World!! for name 
```

我希望你喜欢这篇文章。如果您有任何疑问或帮助，可以在 twitter 上联系我。

在下一篇文章中，我们将讨论如何在线程间创建可共享的变量。

感谢阅读。祝你愉快！