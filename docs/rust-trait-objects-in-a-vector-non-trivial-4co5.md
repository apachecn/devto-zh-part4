# 向量中的 Rust Trait 对象-非平凡...

> 原文：<https://dev.to/magnusstrale/rust-trait-objects-in-a-vector-non-trivial-4co5>

学习 Rust 的一个(许多)挑战是取消学习许多面向对象的思想。我最近为了将 trait 对象放入 Vec 而进行的重构让这一点变得非常明显。

如果你不想阅读开悟之路，而是直接跳到答案，现在向下滚动到最后的代码片段。

基本上，我有一个 Vec <sphere>,需要将 Vec 概括成一个 Trait 形状。即类似 Vec <shape>的东西。我的第一个基于 OO 的错误是假设特征的行为类似于接口。这个错误立刻让编译器吐了一屏幕。</shape></sphere> 

```
// Original code
struct Sphere {
    transformation: Matrix, // FYI - implements Copy trait
    material: Material
}

fn do_stuff(objects: Vec<Sphere>) {
}

// New code - won't compile
trait Shape {
    fn material(&self) -> &Material;
    fn transformation(&self) -> Matrix;
}

struct Sphere {
    transformation: Matrix,
    material: Material
}

impl Shape for Sphere {
    fn material(&self) -> &Material {
        &self.material
    }
    fn transformation(&self) -> Matrix {
        self.transformation
    }
}

fn do_stuff(objects: Vec<dyn Shape>) {
} 
```

Enter fullscreen mode Exit fullscreen mode

编译器在这里很有帮助

```
error[E0277]: the size for values of type `(dyn Shape + 'static)` cannot be known at compilation time
  --> src\lib.rs:45:1
   |
45 | / fn do_stuff(objects: Vec<dyn Shape>) {
46 | | }
   | |_^ doesn't have a size known at compile-time
   |
   = help: the trait `std::marker::Sized` is not implemented for `(dyn Shape + 'static)`
   = note: to learn more, visit <https://doc.rust-lang.org/book/ch19-04-advanced-types.html#dynamically-sized-types-and-the-sized-trait>
   = note: required by `std::vec::Vec` 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止很明显——形状是一种特征，可以由任何数量的内存足迹差异很大的类型来实现，这对于 Rust 来说是不可以的。解决方案是装箱您的 Trait 对象，这将您的 Trait 对象放在堆上，并让您像处理常规大小的类型一样处理 Box。(我将对大小特征进行一点试验——可能是未来博客文章的主题，但让我先沿着这条路走下去)我是这样的:

```
fn do_stuff(objects: Vec<Box<dyn Shape>>) {
} 
```

Enter fullscreen mode Exit fullscreen mode

是的-它可以编译。一切都好吗？没有，因为有趣的部分来了。我想检查一个形状是否等于另一个形状，听起来微不足道，对吗？我们有 PartialEq，一切都应该是好的。让我们第一次尝试吧

```
// Will not compile
fn do_stuff(objects: Vec<Box<dyn Shape>>) {
    let obj1 = &objects[0];
    let obj2 = &objects[1];
    if obj1 == obj2 { println!("Equal"); }
} 
```

Enter fullscreen mode Exit fullscreen mode

有用的编译器错误显示

```
error[E0369]: binary operation `==` cannot be applied to type `&std::boxed::Box<dyn Shape>`
  --> src\lib.rs:48:13
   |
48 |     if obj1 == obj2 { println!("Equal"); }
   |        ---- ^^ ---- &std::boxed::Box<dyn Shape>
   |        |
   |        &std::boxed::Box<dyn Shape>
   |
   = note: an implementation of `std::cmp::PartialEq` might be missing for `&std::boxed::Box<dyn Shape>` 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们按照指示，并尝试实现盒子
的 PartialEq

```
impl PartialEq for Box<dyn Shape> {
    fn eq(&self, other: &Box<dyn Shape>) -> bool {
        // Double deref to get to the Shape, ref to avoid E0277 - 
        // "...size for values...unknown at compile time"
        let s = &**self; 
        let o = &**other;
        s == o
    }
}

// Also tweaked the trait definition to ensure that we have PartialEq trait on Shape
trait Shape: PartialEq {
    fn material(&self) -> &Material;
    fn transformation(&self) -> Matrix;
} 
```

Enter fullscreen mode Exit fullscreen mode

编译器说没有

```
error[E0038]: the trait `Shape` cannot be made into an object
  --> src\lib.rs:47:30
   |
47 |     fn eq(&self, other: &Box<dyn Shape>) -> bool {
   |                              ^^^^^^^^^ the trait `Shape` cannot be made into an object
   |
   = note: the trait cannot use `Self` as a type parameter in the supertraits or where-clauses 
```

Enter fullscreen mode Exit fullscreen mode

思考的时间...除非我使用 self 和 other 的实际类型，否则我无法真正实现 PartialEq。我可以委派盒子里的工作...情商对特质的作用。这意味着当调用 trait 方法时，我们知道实际的类型是什么&self(参见下面的 box_eq)。最后一步是计算出“other”的实际类型，如果它与&self 相同，则将 other 转换为该类型并调用 Eq。

这需要大量的实验，阅读 Rust 文档和谷歌搜索。这个 StackOverflow 问题最终让我走上了正轨。把所有的碎片放在一起，我完成了这个

```
#[derive(PartialEq)]
struct Sphere {
    transformation: Matrix,
    material: Material
}

trait Shape: Any {
    fn box_eq(&self, other: &dyn Any) -> bool;
    fn as_any(&self) -> &dyn Any;    
    fn material(&self) -> &Material;
    fn transformation(&self) -> Matrix;
}

impl Shape for Sphere {
    fn as_any(&self) -> &dyn Any {
        self
    }
    fn box_eq(&self, other: &dyn Any) -> bool {
        other.downcast_ref::<Self>().map_or(false, |a| self == a)
    }
    fn material(&self) -> &Material {
        &self.material
    }
    fn transformation(&self) -> Matrix {
        self.transformation
    }
}

impl PartialEq for Box<dyn Shape> {
    fn eq(&self, other: &Box<dyn Shape>) -> bool {
        self.box_eq(other.as_any())
    }
}

fn do_stuff(objects: Vec<Box<dyn Shape>>) {
    let obj1 = &objects[0];
    let obj2 = &objects[1];
    if obj1 == obj2 { println!("Equal"); }
} 
```

Enter fullscreen mode Exit fullscreen mode

重要的部分是:

*   box _ eq——委托给形状实现类型，它给了我们真实的自我类型。
*   任何——这是铁锈中最接近反射的东西。它允许我们使用 downcast_ref 进行运行时类型转换。这给了我们实际的类型，并允许调用 Eq。
*   as_any() -在 box_eq 中给出“其他”作为 any。

请注意，对于任何形状实现，as_any 和 box_eq 的实现在文本上都是相同的。把它变成一个宏是有意义的。

如果你面临类似的问题，我希望这篇文章能帮你节省一些时间。