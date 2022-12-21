# 确保接口不会被意外实现的简单方法

> 原文：<https://dev.to/dannypsnl/a-simple-way-to-ensure-interface-won-t-be-implemented-accidently-24p1>

示例代码相当简单:

```
type Car interface {
    impl() Car
    Move()
}

type Animal interface {
    impl() Animal
    Walk()
} 
```

现在，让我们创建一个结构类型:

```
type Duck struct {
    Animal
} 
```

现在再加上`func (d *Duck) Move()`，就不是车祸了！想嵌入两个接口怎么办？

```
type Duck struct {
    Car
    Animal
} 
```

编译器会拒绝代码，因为:`Duck.impl is ambiguous`，在 Go 定义中不能有两个同名的方法。所以我们可以用它来创建类似 Rust 中的`impl Trait for Type`的东西，虽然这是一种变通方法，但不管怎样，我和 Go 一起工作，所以当我们需要它时，我必须找出如何确保这一点。

如果你想知道我们想要什么东西的话，只需取式有`Move()`的方法就可以了？然后只需定义:

```
type Movable interface {
    Move()
} 
```

要点是正确地减少接口中的概念，并且不要创建具有通用名称的接口，除非你知道你在做什么，但是如果你需要一个变通方法，这就是。

既然 Go 不会写:`func foo(bar A + B + C)`、`A`、`B`、`C`都是`interface`，那么我建议写:

```
func foo(bar interface{
    A
    B
    C
}) 
```

> 注意:换行是必须的，因为 Go 编译器很蠢。您可能只想为此函数定义一个私有接口。

可悲的是我们不能使用`A | B | C`，必须为此做运行时断言。

更糟糕的是，因为你嵌入了接口，Go 不会检查你是否提供了方法的实现，你必须手动完成。