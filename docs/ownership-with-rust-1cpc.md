# 生锈的所有权

> 原文：<https://dev.to/thorning_m/ownership-with-rust-1cpc>

我最近开始学习 Rust 编程语言，我仍然是一个新手，但我认为尝试在这里解释它的一些特性是有益的。

所有权是 Rust 的关键特征之一。与其他语言不同，Rust 不使用垃圾收集器来释放未使用的内存，也不需要程序员手动管理。相反，Rust 使用一个所有权系统来管理其值，规则由编译器在编译时检查，这意味着您可以两全其美，因为您的程序在运行时不会被垃圾收集器拖慢，并且您不需要自己管理内存使用。

所有权的基本概念很简单；每个值都由一个变量拥有，一次只能由一个变量拥有，当变量超出范围时，该值将从内存中删除。

## 栈还是堆？

首先，讨论 Rust 处理的两种类型的值可能是个好主意。一些值有一个固定的大小，在编译时是已知的，在程序的整个生命周期中不会改变，例如 *u32* 。这些值可以存储在堆栈中，可以自由传递，而无需担心所有权问题。另一种类型的值是动态的，它的大小在编译时是未知的，在程序运行时可能会改变，比如一个*字符串*。第二种类型存储在堆上，指向它在内存中的位置的指针存储在堆栈上。堆上的数据可以改变大小，但它的指针将始终保持相同的大小。

所有权因为这些动态价值而存在。当指针留在堆栈中引用已经重新分配的内存时，其他语言会遇到问题；Rust 没有这个问题。当一个变量在堆上拥有一个值时，其他任何变量都不能拥有它。可以创建引用拥有该值的变量的指针，并且所有权可以移动到不同的变量。当拥有该值的变量超出范围时，该值将从堆中移除，以便可以重新分配内存。

为了说明这一点，请看看下面的两个例子，第一个使用了 *& str* (编译时大小已知)，第二个使用了 *String* (编译时大小未知)。

### 同& str

```
fn main() {
    let hello = "hello";
    println!("{} from the main function", hello);
    other(&hello);
    println!("{} again from the main function", hello);
}

fn other(greeting: &str) {
    println!("{} from the other function", greeting);
} 
```

> 主函数的问候另一个函数的问候主函数的问候

### 用字符串表示

```
fn main() {
    let hello = String::from("hello");
    println!("{} from the main function", hello);
    other(hello);
    println!("{} again from the main function", hello);
}

fn other(greeting: String) {
    println!("{} from the other function", greeting);
} 
```

> 错误[E0382]:移动值的借用:`hello`->src/main . RS:5:49 | 2 | let hello = String::from(" hello ")；| - move 发生是因为`hello`有类型`std::string::String`，它没有实现`Copy` trait 3 | println！(“{}来自主函数”，你好)；4 |其他(你好)；| -移动到此处的值 5 | println！(“{}又来了主函数”，你好)；| ^^^^^值搬后借到了这里

编译器告诉我们，我们已经将值的所有权从`main`函数中的`hello`变量转移到了`other`函数中的`greeting`变量，因此它不能被*第 5 行*的`main`函数再次使用。消息还说“*移动发生是因为`hello`有类型`std::string::String`，它没有实现`Copy`特征*”。我可能会在某个时候发布一个关于特征的帖子，所以我不会在这里深入讨论，但是编译器要说的是，这不是一个可以复制的类型，因此它被移动了。所有可以存储在堆栈上的类型都可以被复制，这就是所有权规则不适用于它们的原因；因为它们的大小是已知的，所以复制值的性能开销不值得担心，这就是所发生的情况。

## 克隆

这并不意味着我们不能复制堆上的值，克隆值是我们编译代码的一种方式:

```
fn main() {
    let hello = String::from("hello");
    println!("{} from the main function", hello);
    other(hello.clone());
    println!("{} again from the main function", hello);
}

fn other(greeting: String) {
    println!("{} from the other function", greeting);
} 
```

> 主函数的问候另一个函数的问候主函数的问候

你能看出区别吗？现在，在*的第 4 行*，我们用`hello`变量保存的值的克隆来调用`other`函数。显然，这不是编写程序的最有效的方式，而且如果你不知道将要被克隆的值的大小，这是很危险的。此外，如果你的函数以某种方式改变了值，那么这不是一个好的解决方案:

```
fn main() {
    let mut hello_from = Vec::new();
    hello_from.push("main");

    one(hello_from.clone());
    two(hello_from.clone());

    println!("hello from {:?}", hello_from)
}

fn one(mut hello_from: Vec<&str>) {
    hello_from.push("one");
}

fn two(mut hello_from: Vec<&str>) {
    hello_from.push("two");
} 
```

> 你好，来自["main"]

在这里，我们创建了一个向量，在这个向量中，我们希望收集传递给它的函数的名称。因为我们使用了 clone，所以每个函数都得到它正在变异的值的副本，这意味着当我们打印 vector 时，它只包含“main”。

## 移动

您可能得到的第一个解决方案是每次都返回变量:

```
fn main() {
    let mut hello_from = Vec::new();
    hello_from.push("main");
    hello_from = one(hello_from);
    hello_from = two(hello_from);
    println!("hello from {:?}", hello_from)
}

fn one(mut hello_from: Vec<&str>) -> Vec<&str> {
    hello_from.push("one");
    hello_from
}

fn two(mut hello_from: Vec<&str>) -> Vec<&str> {
    hello_from.push("two");
    hello_from
} 
```

> 你好来自["主"，"一"，"二"]

在这个例子中，我们将值的所有权转移到每个函数作用域中的一个新变量。这是完全正确的，但在我看来，它有其缺点。首先，它需要编写更多的代码，`one`和`two`函数现在都需要声明一个返回类型，我们需要在每次返回值*(第 4 行&第 5 行)*时重载`main`函数中的`hello_from`变量。其次，我认为当值的所有者停留在一个范围内时，更容易理解代码；借贷使这成为可能。

## 借款

```
fn main() {
    let mut hello_from = Vec::new();
    hello_from.push("main");

    let hello_ref = &mut hello_from;
    one(hello_ref);
    two(hello_ref);

    println!("hello from {:?}", hello_from)
}

fn one(hello_from: &mut Vec<&str>) {
    hello_from.push("one");
}

fn two(hello_from: &mut Vec<&str>) {
    hello_from.push("two");
} 
```

> 你好来自["主"，"一"，"二"]

这里我们创建了一个变量的可变引用，并将其传递给函数。因为它是一个可变的引用，我们可以将值推入，它们将被添加到 vector 中，就像我们已经传递了 vector 本身一样，但是值的所有权永远不会离开`main`函数中的`hello_from`变量。

## 一些注意事项

除了确保内存安全，Rust 还通过一些关于引用的规则保护我们免受竞争条件的影响。您可以创建任意多的不可变引用，但是不允许一次创建多个可变引用:

```
fn main() {
    let mut hello_from = Vec::new();
    hello_from.push("main");

    let hello_ref1 = &mut hello_from;
    let hello_ref2 = &mut hello_from;
    one(hello_ref1);
    two(hello_ref2);

    println!("hello from {:?}", hello_from)
}

fn one(hello_from: &mut Vec<&str>) {
    hello_from.push("one");
}

fn two(hello_from: &mut Vec<&str>) {
    hello_from.push("two");
} 
```

> 错误[E0499]:不能一次多次借用可变的`hello_from`->src/main . RS:6:26 | 5 | let hello _ re f1 =&mut hello _ from；| -第一个可变借位发生在这里 6 | let hello _ re F2 =&mut hello _ from；| ^^^^^^^^^^^^^^^第二次可变借位发生在这里 7 | one(hello _ re f1)；| -这里先借后用

当已经有一个或多个不可变引用时，也不允许创建可变引用:

```
fn main() {
    let mut hello_from = Vec::new();
    hello_from.push("main");

    let display_ref = &hello_from;
    let hello_ref = &mut hello_from;
    one(hello_ref);
    two(hello_ref);

    display(display_ref)
}

fn one(hello_from: &mut Vec<&str>) {
    hello_from.push("one");
}

fn two(hello_from: &mut Vec<&str>) {
    hello_from.push("two");
}

fn display(hello_from: &Vec<&str>) {
    println!("hello from {:?}", hello_from)
} 
```

> 错误[E0502]:无法将`hello_from`作为可变项借用，因为它也被作为不可变项借用->src/main . RS:6:25 | 5 | let display _ ref =&hello _ from；| -此处发生不可变借位 6 | let hello _ ref =&mut hello _ from；| ^^^^^^^^^^^^^^^可变借发生在这里...10 | display(display_ref) | -不可改变的借用稍后在这里使用

但是编译器可以判断出什么时候不能通过可变引用进行进一步的更改，并允许您再次开始创建不可变引用！

```
fn main() {
    let mut hello_from = Vec::new();
    hello_from.push("main");

    let hello_ref = &mut hello_from;
    one(hello_ref);
    two(hello_ref);

    let display_ref = &hello_from;
    display(display_ref)
}

fn one(hello_from: &mut Vec<&str>) {
    hello_from.push("one");
}

fn two(hello_from: &mut Vec<&str>) {
    hello_from.push("two");
}

fn display(hello_from: &Vec<&str>) {
    println!("hello from {:?}", hello_from)
} 
```

> 你好来自["主"，"一"，"二"]

* * *

这是我关于 Rust 的第一篇文章，我希望它是有用的(并且是正确的！)，一切都很好，我打算写一些，因为我发现这样做确实有助于增加我自己的理解，如果它们能同时帮助别人，那就更好了。如果你有任何意见，请在推特上告诉我。😃