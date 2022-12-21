# C#中的泛型扩展方法模糊性

> 原文：<https://dev.to/lauromoura/generic-extension-methods-ambiguity-in-c-4c7f>

作为我们在[EFL](https://www.enlightenment.org/develop/tutorials/csharp/start.md)的 C#绑定[工作的一部分，最近我在使用 C#的扩展方法时偶然发现了一个不太明显的问题。](http://expertisesolutions.com.br/)

在实现对 MVVM 工厂的支持时，会生成工厂的扩展方法，以便您可以将模型的属性绑定到将要创建的对象的属性。例如

```
var factory = new Efl.ItemFactory<ListItem>();
factory.Text().bind("modelProperty"); 
```

工厂将创建`ListItem`的实例，并在实例化它们时，将关联模型元素的`modelProperty`绑定到新创建的`ListItem`的`Text`属性，因此每当模型改变时，创建的小部件显示更新的值。

为了使这些属性可用，为每个“可包装”类(即可以放入工厂的类)中的每个属性生成了以下扩展方法:

```
/// Extensions for ListItem
static Bindable<string> Text<T>(this ItemFactory<T> fac) where T : ListItem
{
  ...
}
/// Extensions for GridItem
static Bindable<string> Text<T>(this ItemFactory<T> fac) where T : GridItem
{
  ...
} 
```

到目前为止，一切顺利。但是上面的代码有一个重要的限制。`where`约束[不是签名](https://blogs.msdn.microsoft.com/ericlippert/2009/12/10/constraints-are-not-part-of-the-signature/)的一部分。如果我们试图调用其中一个同名但有两个不同的`T`的扩展方法，就会产生歧义，编译会失败，并显示 [CS0121](https://docs.microsoft.com/en-us/dotnet/csharp/misc/cs0121) `The call is ambiguous between the following methods or properties:...`。

我们使用的解决方案(感谢上帝[栈溢出](https://stackoverflow.com/a/14624904/347889))是添加一个额外的默认参数，这个参数依赖于 T 和约束的类型。这将允许基于泛型类型参数选择唯一的方法。

使用上述方法，我们可以做如下事情:

```
/// Magic tag to select the unique method
class MagicTag<TBase, TInherited> : where TInherited : TBase
{ }

/// Extensions for ListItem
static Bindable<string> Text<T>(this ItemFactory<T> fac, MagicTag<ListItem, T> x=null) where T : ListItem
{
  ...
}
/// Extensions for GridItem
static Bindable<string> Text<T>(this ItemFactory<T> fac, MagicTag<GridItem, T> x=null) where T : GridItem
{
  ...
} 
```

这成功地为编译器提供了足够的信息，在大多数情况下，选择唯一正确的方法*。例外的情况是我们有两个同名的方法，而`TInherited` **实际上是从`TBase`派生出来的一个派生类**。但是在我们的例子中，代码生成器避免为继承的类生成重复的方法，绕过了这个问题。*

 *o/*