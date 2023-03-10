# 所有的 Ruby 模块

> 原文：<https://dev.to/tcelovsky/all-the-ruby-modules-912>

这篇文章的目的是简要介绍 Ruby 模块，并展示 Ruby 模块和 Ruby 类之间的区别。

Ruby 模块和 Ruby 类非常相似，主要区别在于模块不像类那样没有实例。我们用关键字
开始定义一个模块

```
module MyModule
 def say_hello
  puts “Hello, World!”
 end 
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，这个模块可以包含在一个类中，使类的实例能够调用模块中定义的实例方法:

```
class MyClass
 include MyModule
end

c = MyClass.new
c.say_hello 
```

Enter fullscreen mode Exit fullscreen mode

注意上面代码的第二行被剪掉了，这就是`MyModule`模块被包含在`MyClass`类中的原因。上面的结果将是`“Hello, World!”`输出，因为模块`MyModule`包含在类`MyClass`中，这导致`MyClass`类访问`MyModule`模块中的实例方法。

注意从类继承和包含模块之间的区别是很重要的。Ruby 类一次只能继承一个类，但是任何一个类中都可以包含多个 Ruby 模块。注意下面的类继承(第一行)和模型包含(第二行和第三行)的区别:

```
class MyClass < MySuperClass
 include MyModule1
 include MyModule2
end 
```

Enter fullscreen mode Exit fullscreen mode

此外，多个 Ruby 类可以包含同一个模块，因此，模块提供了在多个 Ruby 类之间共享相同实例方法的能力。当您正在编写一个多个类共享行为的程序时，这种能力非常有用。

通常最好的做法是使用名词作为类名，而使用形容词作为模块名。在上面的例子中没有遵循这个一般的指导，主要是为了便于理解和表达。

考虑到任何单个类中可以包含多个模块，我们可能会遇到两个不同的方法具有相同的定义。在这种情况下，目标是什么？当消息(例如方法定义)传递给对象时，对象将按以下顺序查找该方法:

1.  对象的类别
2.  对象类中包含的模块，包含顺序相反
3.  该类的超类
4.  包含在超类中的模块，包含顺序相反

将执行对象遇到的与传递给对象的消息匹配的第一个方法。这意味着如果方法`my_method`是在对象的类中定义的，那么该方法将被执行，即使在对象的类中包含的模块中定义了另一个方法`my_method`。如果同一个方法在同一个类中定义了两次，那么第二个定义(后面定义的那个)将优先。同样的规则也适用于模块。请记住，包含在对象类中的模块是以包含的相反顺序搜索的，因此后面包含的模型将首先被搜索。有趣的事实是，两次包含同一个模块来影响模块的搜索顺序实际上没有任何作用。这意味着在下面的第 4 行中包含`MyModule1`不会强制 Object 首先搜索它。对象仍将首先搜索`MyModule2`，然后继续搜索`MyModule1`。

```
class MyClass < MySuperClass
 include MyModule1
 include MyModule2
 include MyModule1
end 
```

Enter fullscreen mode Exit fullscreen mode

应该如何决定何时包含一个模块还是继承一个类？这方面没有统一的规则，但记住以下几点可能会有所帮助:

*   模块没有实例。因此，通常最好为实体或事物建立类，然后使用模块来封装这些实体或事物的行为。这也是为什么类名一般倾向于名词，而模块名倾向于形容词。
*   一个类只能从一个超类继承，但是一个类中可以包含多个模块。如果你的类有几个特征，最好把它们放在不同的模块中，因为你的类不能从多个超类中继承。