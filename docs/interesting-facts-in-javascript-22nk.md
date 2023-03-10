# JavaScript 中有趣的事实

> 原文：<https://dev.to/shafikshaon/interesting-facts-in-javascript-22nk>

使用 JavaScript 有很多有趣的地方。尽管工程师们日复一日地与它联系在一起，但这门语言的一些部分仍未被探索。我将重点介绍一些你可能不会想到的关于 JavaScript 的东西。

# 楠是一个数字

`NaN`(不是数)是被数。还有，`NaN`不等于自身。其实`NaN`一事无成。验证任何事情的唯一方法是由`isNaN()`决定`NaN`与否。

```
> typeof(NaN)
"number"

> NaN === NaN
false 
```

Enter fullscreen mode Exit fullscreen mode

# null 是一个对象

`null`是一个物体。听起来很奇怪！对吗？但这是事实。

```
> typeof(null)
"object" 
```

Enter fullscreen mode Exit fullscreen mode

万一，`null`没有值。所以，`null`应该不是`Object`的实例。

```
> null instanceof Object
false 
```

Enter fullscreen mode Exit fullscreen mode

# 未定义可以定义

`undefined`不是 JavaScript 中的保留关键字。你可以给它赋值。它没有通过任何语法错误。但是，你不能在任何地方赋值，它返回 undefined。

```
> var some_var;
undefined
> some_var == undefined
true
> undefined = 'i am undefined' 
```

Enter fullscreen mode Exit fullscreen mode

# 0.1 + 0.2 不等于 0.3

在 JavaScript 中，`0.1 +0.2 == 0.3`返回 false。事实是，javascript 如何将浮点数存储为二进制。

```
> 0.1 + 0.2
0.30000000000000004
> 0.1 + 0.2 == 0.3
false 
```

Enter fullscreen mode Exit fullscreen mode

# Math.max()比 Math.min()小

`Math.max() > Math.min()`返回`false`这个事实听起来不对，但实际上很有道理。

```
> Math.max() > Math.min()
false 
```

Enter fullscreen mode Exit fullscreen mode

如果没有参数通过`min()`或`max()`传递，那么它返回以下值。

```
> Math.max()
-Infinity
> Math.min()
Infinity 
```

Enter fullscreen mode Exit fullscreen mode

# 018 减 045 等于 3

在 JavaScript 中，前缀`0`会将任何数字转换成八进制。但是，`8`在八进制中并不使用，任何包含`8`的数字都会被默默地转换成常规的十进制数。

```
> 018 - 045
-19 
```

Enter fullscreen mode Exit fullscreen mode

因此，`018 — 017`事实上等价于十进制表达式`18 — 37`，因为`045`是八进制，而`018`是十进制。

# 功能可以自行执行

只需创建一个函数，然后立即调用它，就像我们调用其他函数一样，使用`()`语法

```
> (function()  { console.log('I am self executing');  })();
I am self executing 
```

Enter fullscreen mode Exit fullscreen mode

# 括号位置关系重大

return 语句“看不到”它要返回的内容，所以它不返回任何内容。实际上，JavaScript 把`;`放在 return 之后。

```
> function foo() {
   return
   {
      foo: 'bar'
   }
}
> foo(); 
undefined

> function foo() {
   return {
      foo: 'bar'
   }
}
> foo(); 
{foo: "bar"} 
```

Enter fullscreen mode Exit fullscreen mode

# 缺少参数默认值

在 JavaScript 中，可以通过以下方式设置参数默认值。

```
> function missingParamerCheck(name, age){
    var name = name || 'John Doe'
    var age = age
    console.log(name)
    console.log(age)
}
> missingParamerCheck('', 23)
John Doe
23
> missingParamerCheck('Mike', 18)
Mike
18 
```

Enter fullscreen mode Exit fullscreen mode

# 没有整数数据类型

在 JavaScript 中，没有`int`(整数)数据类型。所有号码都是`Number`型。实际上，它在内存级别中存储了`int`号的浮点值。

# sort()函数自动类型转换

函数自动将值转换成字符串，这就是为什么会发生一些奇怪的事情。

```
> [1,5,20,10].sort()
(4) [1, 10, 20, 5] 
```

Enter fullscreen mode Exit fullscreen mode

但是，可以通过比较来解决。

```
> [1,5,20,10].sort(function(a, b){return a - b});
(4) [1, 10, 20, 5] 
```

Enter fullscreen mode Exit fullscreen mode

# 数组和对象的总和

```
> !+[]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]
9
> {} + []
0
> [] + {}
"[object Object]"
> [] + []
""
> {} + {}
"[object Object][object Object]"
> {} + [] == [] + {}
true 
```

Enter fullscreen mode Exit fullscreen mode

理想情况下，您发现了一些新信息，或者可能显示出对这些 JavaScript 珍珠的新内容的理解有所提高。你还知道哪些未经探索/前所未有的 JavaScript 亮点？请在评论中分享它们。
这个帖子也可以在[这里](https://shafik.xyz/posts/interesting-facts-in-javascript/)