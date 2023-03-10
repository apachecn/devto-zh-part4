# 揭开 JavaScript 中“常量”变量的神秘面纱

> 原文：<https://dev.to/sunnysingh/demystifying-const-variables-in-javascript-2jfo>

到目前为止，ES6 (ES2015)已经足够普及，许多开发人员都在用这种现代语法编写他们的 JavaScript。然而，我通常认为变量声明还有改进的空间。具体来说，当我看到一个`let`来声明一个被更新的对象，但是从来没有重新分配一个新值。

下面是一个令人不快的例子:

```
let cache = {};

cache.id = 'hello';

// We never reassign cache to a different value.
// It's a reference to the same object. 
```

Enter fullscreen mode Exit fullscreen mode

是的，那实际上可以是一个`const`，因为`cache`变量永远不会被重新赋值。

## 不变性 vs 再分配

许多人将`const`声明称为创建“不可变的”变量。不可变意味着值(包括对象的属性)永远不会被更新。

如果是这种情况，那么让我们使用`const` :
来尝试我们最初的例子

```
const cache = {};

cache.id = 'hello';

console.log(cache.id); // will output "hello" 
```

Enter fullscreen mode Exit fullscreen mode

如果`cache`现在是一个不可变的、不可更改的常量，那么试图向它添加一个`id`属性将不起作用，甚至会抛出一个错误。

但是，如果您尝试运行这个示例，它将会运行得非常好。将记录从更新的对象中检索的字符串“hello”。

好的，那么如果`const`没有声明某个东西是不可变的，它会做什么呢？嗯， **`const`防止一个变量被重新分配一个新值**。

下面是一些重新分配变量的例子:

```
let cache = {};

cache = { id: 'hello' }; // reassigning variable to a new object

let isSuccess = false;

isSuccess = true; // reassigning variable to false boolean 
```

Enter fullscreen mode Exit fullscreen mode

将上面的任何一个`let`语句改为`const`将抛出一个类型错误:“未捕获的类型错误:常量变量的赋值。”

设置一个对象的属性，或者更新一个现有的属性，是**而不是**重新分配。变量的值仍然是同一个对象，并且您正在更新该对象的属性。

## 使用的好处`const`

希望你现在明白了`const`和`let`的区别，但这可能仍然回避了一个主要问题:为什么我要足够在意使用`const`？

它主要归结于你的代码的质量，以及你或另一个开发者能从中推断出多少信息。例如，当我查看代码库中的一个文件时，我看到了这个:

```
let isSuccess = true; 
```

Enter fullscreen mode Exit fullscreen mode

好的，这是一个变量，声明某个操作是否成功，在这个例子中，因为值是`true`。然而，`let`还告诉我，这个变量可以在文件中的任何地方重新分配给`false`。

为了仔细检查，我在文件中搜索了`isSuccess`的所有实例。嗯，它没有被重新分配到任何地方，所以我知道这个变量的值仍然是`true`。

对我来说听起来像一个常量，如果这个变量实际上被声明为一个`const`，那么我就不需要在文件的其他地方寻找它。

现在，想象几个月后你重新阅读这段代码。在文件的下面，您添加了这个:

```
isSuccess = false; 
```

Enter fullscreen mode Exit fullscreen mode

哦哦，你刚才潜在地导致了一个错误，因为当你最初为`isSuccess`编写这个代码成为`true`的时候，你并没有期望任何人以后会改变它。甚至[你未来的自己](https://sunnysingh.io/blog/readable-code)。

这是一个极其常见的场景，尤其是在有多个开发人员的大型代码库中。最重要的是，这可以通过适当地使用`const`和`let`很容易地解决。

## 默认为`const`，必要时使用`let`

作为一个通用规则，我建议使用`const`来声明所有的新变量，当您遇到需要重新分配其中一个变量的时候，就将它改为`let`。

这里有一个更大的例子来说明如何正确地使用这两者:

```
const cache = {};
let user = {};
let isSuccess = false;

// The `cache` variable is never reassigned a new value.
cache.greeting = `Hello`;

// The `user` variable is not reassigned a new value right now.
user.id = 1;

// Here, we assign `user` and `isSuccess` new values.
user = {};
isSuccess = true; 
```

Enter fullscreen mode Exit fullscreen mode

用`const`而不是`var`和`let`来声明变量无疑是一种范式转变，但它可以给你的代码增加更多的信心。尝试一下，让我知道你的想法。

想了解如何进一步改进代码吗？你可能会喜欢我的[为你未来的自己写代码](https://sunnysingh.io/blog/readable-code)的文章。

[![sunnysingh image](img/84187a0d675232a4644ac89425e73371.png)](/sunnysingh) [## 为未来的自己编写代码

### 桑尼·辛格 2 月 2 日 194 分钟阅读

#programming #tips #learning #javascript](/sunnysingh/writing-code-for-your-future-self-3da2)