# 为什么超类的实例变量没有在子类中被覆盖

> 原文：<https://dev.to/njnareshjoshi/why-instance-variable-of-super-class-is-not-overridden-in-sub-class-105c>

当我们在父类和子类中都创建了一个同名的变量，并试图使用包含子类对象的父类引用来访问它时，我们会得到什么？

为了理解这一点，让我们考虑下面的例子，我们在`Parent`和`Child`类中声明了一个同名的变量`x`。

```
class Parent {
    // Declaring instance variable by name `x`
    String x = "Parent`s Instance Variable";

    public void print() {
        System.out.println(x);
    }
}

class Child extends Parent {

    // Hiding Parent class's variable `x` by defining a variable in the child class with the same name.
    String x = "Child`s Instance Variable";

    @Override
    public void print() {
        System.out.print(x);

        // If we still want to access variable from super class, we do that by using `super.x`
        System.out.print(", " + super.x + "\n");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们尝试使用下面的代码访问`x`，那么`System.out.println(parent.x)`将会显示什么

```
Parent parent = new Child();
System.out.println(parent.x) // Output -- Parent`s Instance Variable 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，我们会说`Child`类会覆盖`Parent`类中声明的变量，而`parent.x`会给我们`Child's`对象持有的任何东西。因为当我们对方法做同样的操作时，发生的是同样的事情。

但实际上不是，而且`parent.x`会给我们在`Parent`类中声明的父实例变量赋值，但是为什么呢？

因为 Java 中的变量不遵循多态性，重写只适用于方法，不适用于变量。并且当子类中的实例变量与父类中的实例变量同名时，则从引用类型中选择该实例变量。

在 Java 中，当我们在子类中定义一个变量，而这个变量的名字已经用来定义父类中的变量时，子类的变量会隐藏父类的变量，即使它们的类型不同。这个概念被称为**变量隐藏。**

换句话说，当子类和父类都有一个同名的变量时，子类的变量隐藏了父类的变量。你可以在文章[什么是 Java 中的变量隐藏和隐藏](https://programmingmitra.com/2018/02/what-is-variable-shadowing-and-hiding.html)中阅读更多关于变量隐藏的内容。

## 变量隐藏并不等同于方法覆盖

虽然变量隐藏看起来像重写一个类似于方法重写的变量，但它不是，重写只适用于方法，而隐藏适用于变量。

在**方法覆盖**的情况下，覆盖方法完全替换继承的方法，因此当我们试图通过持有子对象从父引用访问方法时，来自子类的方法被调用。你可以在[上阅读更多关于重写以及被重写的方法如何完全取代继承的方法的内容关于方法重载 Vs 方法重写的一切](https://programmingmitra.com/2017/05/everything-about-method-overloading-vs-method-overriding.html)，[为什么我们应该遵循方法重写规则](https://programmingmitra.com/2017/12/why-we-should-follow-method-overriding-rules.html)。

但是在**变量隐藏**中，子类隐藏继承的变量而不是替换，这基本上意味着子类的对象包含两个变量，但是子变量隐藏父变量。因此，当我们试图从子类中访问变量时，它将从子类中被访问。

而如果我简化第[节例 8.3.1.1-3。隐藏](http://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#d5e10238) [Java 语言规范](http://docs.oracle.com/javase/specs/jls/se7/html/index.html)的实例变量:

> 当我们在一个`Child`类中声明了一个与`Parent`类中的一个实例变量同名的变量，例如`x`,那么
> 
> 1.  子类的对象包含两个变量(一个继承自`Parent`类，另一个在`Child`本身中声明),但是子类变量隐藏了父类的变量。
> 2.  因为`Child`类中`x`的声明隐藏了`Parent`类中`x`的定义，所以在`Child`类的声明中，简单名称`x`总是引用`Child`类中声明的字段。如果`Child`类的方法中的代码想要引用`Parent`类的变量`x`，那么这可以通过`super.x`来完成。
>     
>     
> 3.  如果我们试图访问`Parent`和`Child`类之外的变量，那么从引用类型中选择实例变量。因此，下面代码中的表达式`parent2.x`给出了属于父类的变量值，即使它持有`Child`的对象，但是`((Child) parent2).x`从`Child`类中访问该值，因为我们对`Child`进行了相同的引用。

[![Why Instance Variable Of Super Class Is Not Overridden In Sub Class due to variable shadowing](img/03866dc7012e62ba4d24dc9e6036b8aa.png "Why Instance Variable Of Super Class Is Not Overridden In Sub Class due to variable shadowing")](////2.bp.blogspot.com/-ri37eZ4bbVk/W-WFc9in3sI/AAAAAAAAR9I/brSieunxW483qLpJU9dWPVo-q5SuBsupQCK4BGAYYCw/s1600/variable-shadowing.jpeg)

## 为什么变量隐藏是这样设计的

**所以我们知道实例变量是从引用类型中选择的，而不是实例类型，多态不适用于变量**但真正的问题是为什么？为什么变量被设计成遵循隐藏而不是重写。

因为如果我们在子类中改变变量的类型，变量重写可能会破坏从父类继承的方法。

我们知道每个子类都从其父类继承变量和方法(状态和行为)。想象一下，如果 Java 允许变量覆盖，我们在子类中将变量的类型从`int`改为`Object`。它将破坏任何使用该变量的方法，因为子节点从父节点继承了这些方法，编译器将在`child`类中给出错误。

例如:

```
class Parent {
    int x;
    public int increment() {
        return ++x;
    }
    public int getX() {
        return x;
    }
}

class Child extends Parent {
    Object x;
    // Child is inherting increment(), getX() from Parent and both methods returns an int 
    // But in child class type of x is Object, so increment(), getX() will fail to compile. 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`Child.x`覆盖`Parent.x`，`increment()`和`getX()`如何工作？在子类中，这些方法将试图返回错误类型的字段的值！

如前所述，如果 Java 允许变量覆盖，那么子变量就不能替代父变量，这就违反了 Liskov 可替代性原则(LSP)。

## 为什么从引用类型中选择实例变量而不是实例

正如在[JVM 如何处理方法重载和内部重写](https://programmingmitra.com/2017/05/how-does-jvm-handle-method-overriding-internally.html)中所解释的，在编译时，重写方法调用仅从引用类被处理，但是所有被重写的方法在运行时使用 vtable 被重写方法替换，这种现象被称为运行时多态性。

类似地，在编译时，变量访问也被从引用类型中处理，但是正如我们所讨论的，变量不遵循覆盖或运行时多态性，所以它们在运行时不会被子类变量替换，并且仍然引用引用类型。

一般来说，没有人会建议隐藏字段，因为这会使代码难以阅读并造成混乱。如果我们总是遵循一般准则来创建 POJO 并通过将它们声明为私有来封装我们的字段，并根据需要提供 getter/setter，这样变量在该类之外是不可见的，子类也不能访问它们，就不会有这种混乱。

你可以在这个 [Github 库](https://github.com/njnareshjoshi/exercises/blob/master/src/org/programming/mitra/exercises/VariableShadowingExample.java)上找到完整的代码，请随时提供你的宝贵反馈。