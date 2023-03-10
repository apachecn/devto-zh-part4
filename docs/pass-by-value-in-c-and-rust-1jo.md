# C++和 Rust 中的按值传递

> 原文：<https://dev.to/deciduously/pass-by-value-in-c-and-rust-1jo>

C++和 Rust 经常被拿来互相比较。它们在功能和灵活性方面占据了相似的空间——它们都没有垃圾收集器，因此可以适应资源受限的领域，但它们都提供了比 C 语言更丰富的高级工具，从而提高了安全性和正确性。

然而，在这两种环境中编写程序的体验可能会非常不同。Rust 初学者很快就会遇到这种差异，这就是按值传递参数时会发生的情况。Rust 处理这种情况的方式与 C++不同，值得探究原因。

## C++

在 C++中，按值传递会将对象的一个**副本**传递给函数。这对于像整数这样的基元来说很好——我的 5 和你的 5 是一样的。事实上，它们在内存中是不同的值，这与它们的用途无关，因为 5 的含义与上下文或状态无关。不过，很多其他事情都是如此。当一个对象在 C++中被复制时，它的*复制构造函数*被调用。这些有一个原型，看起来像这样:

```
classname (const classname &obj); 
```

Enter fullscreen mode Exit fullscreen mode

当一个对象作为参数传递给一个方法时，这个构造函数用于将该对象复制到函数体中。检查参数列表开头的关键字“const”。这意味着我们不能使用这个构造函数对初始对象进行任何修改。相反，它只是创建一个新的副本，这是在任何函数中使用的。举例来说，这里有一个简单的类，只有一个数据成员、一个默认的构造函数、一个 getter 和 setter:

```
class CoolObject
{
    int coolValue;

public:
    CoolObject()
    {
        coolValue = 5;
    }
    int getCoolValue() const
    {
        return coolValue;
    }
    void setCoolValue(int val)
    {
        coolValue = val;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将编写一个函数，通过值获取这些对象中的一个，并将其设置为 10:

```
#include <iostream>  
void setCoolValueToTen(CoolObject co)
{
    using std::cout;
    cout << "Current: " << co.getCoolValue() << " | Setting...\n";
    co.setCoolValue(10);
    cout << "New: " << co.getCoolValue() << "\n";
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们做两个这样的，在一个上使用这个函数，你会期望它能坚持，对吗？

```
int main()
{
    using std::cout;
    CoolObject co1;
    CoolObject co2;
    cout << "co1: " << co1.getCoolValue() << " | co2: " << co2.getCoolValue() << "\n";
    setCoolValueToTen(co2);
    cout << "co1: " << co1.getCoolValue() << " | co2: " << co2.getCoolValue();
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

相反，我们得到以下结果:

```
co1: 5 | co2: 5
Current: 5 | Setting...
New: 10
co1: 5 | co2: 5 
```

Enter fullscreen mode Exit fullscreen mode

setCoolValueToTen()函数中的代码运行在它自己的副本上，当它被传入时，它由 co2 制成并与 CO2 相同，但与 CO2 完全不同。在这个本地实例上调用 setter 对 co2 没有影响，因为它不再参与。

如果您通过值传递，那么您的所有更改都将停留在这个新的本地副本中，并且永远不会返回到您想要的目标。参考原文解决了这个问题:

```
void reallySetCoolValueToTen(CoolObject &co) // Just take a reference - rest is identical!
{
    using std::cout;
    cout << "Current: " << co.getCoolValue() << " | Setting...\n";
    co.setCoolValue(10);
    cout << "New: " << co.getCoolValue() << "\n";
}

int main()
{
    using std::cout;
    CoolObject co1;
    CoolObject co2;
    cout << "co1: " << co1.getCoolValue() << " | co2: " << co2.getCoolValue() << "\n";
    setCoolValueToTen(co2);
    cout << "co1: " << co1.getCoolValue() << " | co2: " << co2.getCoolValue() << "\n";
    reallySetCoolValueToTen(co2);
    cout << "co1: " << co1.getCoolValue() << " | co2: " << co2.getCoolValue() << "\n";
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个调用按预期工作:

```
co1: 5 | co2: 5
Current: 5 | Setting...
New: 10
co1: 5 | co2: 5
Current: 5 | Setting...
New: 10
co1: 5 | co2: 10 
```

Enter fullscreen mode Exit fullscreen mode

## 生锈

让我们尝试在 Rust 中重新实现这个小程序。这是我们的`CoolObject` :

```
struct CoolObject {
    cool_value: i32,
}

impl CoolObject {
    fn get_cool_value(&self) -> i32 {
        self.cool_value
    }
    fn set_cool_value(&mut self, val: i32) {
        self.cool_value = val;
    }
}

impl Default for CoolObject {
    fn default() -> Self {
        Self { cool_value: 5 }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个函数将值设置为 10，按值取参数:

```
fn set_cool_value_to_ten(mut co: CoolObject) {
    println!("Current: {} | Setting...", co.get_cool_value());
    co.set_cool_value(10);
    println!("New: {}", co.get_cool_value());
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经开始看到一个问题——我们不能像在 C++中那样，不先询问就改变值。如果我没有将那个`mut`包含在参数列表中，`set_cool_value()`调用将会抱怨:“不能将`co`作为可变的借用，因为它没有被声明为可变的”。我们需要明确地告诉编译器我们打算改变对象。

让我们试着模仿 C++版本的第一步:

```
fn main() {
    let co1 = CoolObject::default();
    let co2 = CoolObject::default();
    println!("co1: {} | co2: {}", co1.get_cool_value(), co2.get_cool_value());
    set_cool_value_to_ten(co2);
    println!("co1: {} | co2: {}", co1.get_cool_value(), co2.get_cool_value());
} 
```

Enter fullscreen mode Exit fullscreen mode

试图编译这段代码将会导致如下错误:

```
error[E0382]: borrow of moved value: `co2`
  --> src/main.rs:34:57
   |
31 |     let co2 = CoolObject::new();
   |         --- move occurs because `co2` has type `CoolObject`, which does not implement the `Copy` trait
32 |     println!("co1: {} | co2: {}", co1.get_cool_value(), co2.get_cool_value());
33 |     set_cool_value_to_ten(co2);
   |                           --- value moved here
34 |     println!("co1: {} | co2: {}", co1.get_cool_value(), co2.get_cool_value());
   |                                                         ^^^ value borrowed here after move

error: aborting due to previous error 
```

Enter fullscreen mode Exit fullscreen mode

这就是问题所在。当在 C++中传递值时，编译器会假设你知道你在做什么，并为你调用一个复制构造函数，即使这实际上没有意义。如果你还没有手工定义一个复制构造函数，没关系，编译器会为你生成一个并调用它。毕竟你已经传值了，s 这一定是你想要的！

铁锈给刹车打气。当你通过值传递时，它实际上**移动了原始值的所有权**。它不是复制原始对象，而是从外部引入对象——但是需要注意的是，调用范围不再拥有这个值，而是新函数拥有。当`set_cool_value_to_ten()`到达其主体的末尾时，该值超出范围！掉了。当我们试图在下一行再次引用`co2`时，我们不能——它不再属于我们了。

在 Rust 中，任何值都只有一个所有者。你可以借用尽可能多的不可变引用，当我们调用`get_cool_value(&self)`时就是这样做的，或者我们可以有一个单一的可变引用，比如用`really_set_cool_value_to_ten(co: &mut CoolObject)`，但是如果没有借用，比如用`set_cool_value_to_ten(mut co: CoolObject)`，你知道这个值的所有权将会移动。

这避免了 C++中常见的传值错误，即您认为您在处理一个对象，但实际上您只是在处理一个副本。C++只会默默地努力让事情运转起来，可能和你不是一路人。铁锈是非常明显的。它甚至明确地告诉你，如果你的对象确实实现了`Copy`特征，它会试图复制这个值——但是当然，这仍然不能解决这个问题。与 C++一样，解决方案是引用原始值，而不是移动值。在 C++中，你说“引用”，但是在 Rust 中，你称之为“可变借用”:

```
fn really_set_cool_value_to_ten(co: &mut CoolObject) {
    println!("Current: {} | Setting...", co.get_cool_value());
    co.set_cool_value(10);
    println!("New: {}", co.get_cool_value());
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要将`co2`本身声明为可变的:

```
fn main() {
    let co1 = CoolObject::default();
    let mut co2 = CoolObject::default(); // right here
    println!("co1: {} | co2: {}", co1.get_cool_value(), co2.get_cool_value());
    really_set_cool_value_to_ten(&mut co2); // and pass a mutable reference
    println!("co1: {} | co2: {}", co1.get_cool_value(), co2.get_cool_value());
} 
```

Enter fullscreen mode Exit fullscreen mode

这说明了我更喜欢使用 Rust 而不是 C++的原因之一。在 C++中，程序员只需要知道所有这些关于语言如何操作的细节，而编译器对它采取的隐式动作没有任何疑虑。你没有得到任何帮助来通读你的代码以找出你在哪里犯了这个错误，即使完全意识到这个问题也不足以在 100%的情况下避免它。另一方面，Rust 不会让你要求愚蠢的事情。在这种情况下，编译器能够用简单的英语告诉我为什么我的代码不正确，以及如何修复它。

*Natalia Y 在 Unsplash 上拍摄的照片*