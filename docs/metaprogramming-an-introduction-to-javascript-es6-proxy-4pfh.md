# 元编程:JavaScript(ES6)代理简介

> 原文：<https://dev.to/atapas/metaprogramming-an-introduction-to-javascript-es6-proxy-4pfh>

元编程的概念并不新鲜。有很多编程语言像，Lisp，Scala，Clojure，Rust，Haskell 等等已经开始使用它了。 *JavaScript* 其实也不落后！

在我们继续深入之前，让我们了解一下，什么是元编程？

### 元编程

元编程不亚于一个*魔法*！说真的，写一个程序来读取、修改、分析甚至生成一个*程序*怎么样？听起来是不是很神奇很强大？
[![magic.gif](img/d79cd78131f1d16bd5a3c44693c0e922.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HuxU9DhR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1559815386583/RGcMHgRZZ.gif) 
*图片提供:吉菲*

维基百科将*元编程*定义为，

> 元编程是一种编程技术，在这种技术中，计算机程序能够将其他程序视为自己的数据。

所以基本上，它是处理另一个程序的元数据的程序，并且能够做许多有用的事情。

### 会见代理

> 代理包装对象并通过陷阱拦截它们的行为

在 JavaScript 中，我们可以用多种方式进行元编程，使用 T2 代理对象是其中重要的一种。代理对象是一个 ES6 概念，用于定义基本操作的自定义行为(例如，属性查找、赋值、枚举、函数调用等)。

以下是一些你需要记住和使用的有用术语:

*   目标:代理虚拟化的一个*对象*。
*   handler:包含*陷阱*的占位符对象。
*   trap:提供对*目标*对象的属性访问的*方法*。

如果你没有从上面的描述中得到很多，这是非常好的。通过代码和例子我们会很容易理解。

# 代码时间

下面是创建代理对象的语法:

```
let p = new Proxy(target, handler); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们以一个`employee`对象为例，试着打印它的一些属性:

```
const employee = {
    firstName: 'Tapas',
    lastName: 'Adhikary'
};

console.group('employee');
    console.log(employee.firstName);
    console.log(employee.lastName);
    console.log(employee.org);
    console.log(employee.fullName);
console.groupEnd() 
```

Enter fullscreen mode Exit fullscreen mode

我们知道预期的输出是，

```
employee
  Tapas
  Adhikary
  undefined
  undefined 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用`Proxy`对象来改变这个处理`employee`的程序，并为它提供一些行为:

*   步骤 1:创建一个使用了`Trap`的`Handler`

我们将使用一个名为`get`的陷阱，这是一个获取属性值的陷阱。这里是我们的处理程序:

```
let handler = {
    get: function(target, fieldName) {        

        if(fieldName === 'fullName' ) {
            return `${target.firstName}  ${target.lastName}`;
        }

        return fieldName in target ?
            target[fieldName] :
                `No such property as, '${fieldName}'!`

    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

上面的`handler`有助于为 fullName 属性创建值。它还增加了一个更好的错误消息，以防我们处理一个丢失的属性。

*   步骤 2:创建一个`Proxy`对象

因为我们有了作为`employee`对象的*目标*和作为*处理程序*，我们将能够创建一个作为
的`Proxy`对象

```
let p = new Proxy(employee, handler); 
```

Enter fullscreen mode Exit fullscreen mode

*   步骤 3:访问`Proxy`对象的属性

```
console.group('proxy');
    console.log(p.firstName);
    console.log(p.lastName);
    console.log(p.org);
    console.log(p.fullName);
console.groupEnd() 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到输出为，

```
proxy
  Tapas
  Adhikary
  No such property as, 'org'!
  Tapas Adhikary 
```

Enter fullscreen mode Exit fullscreen mode

注意我们如何神奇地让*改变了`employee`对象的东西。*

 *在前面的例子中，我们使用了一个名为*的`trap`来获取*。以下是可用陷阱的列表:

*   应用
*   建造
*   定义属性
*   删除属性
*   得到
*   getOwnPropertyDescriptor
*   getPrototypeOf
*   有
*   可扩展
*   拥有钥匙
*   预防高血压
*   设置
*   setPrototypeOf

关于这些的更多信息可以在这里找到， [Proxy - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

### 用于验证值的代理

让我们创建一个处理程序(我们可以将其命名为，validator):

```
const validator = {
    set: function(obj, prop, value) {
        if (prop === 'age') {
            if(!Number.isInteger(value)) {
                throw new TypeError('Age is always an Integer, Please Correct it!');
            }
            if(value < 0) {
                throw new TypeError('This is insane, a negative age?');
            }
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们可以创建一个`Proxy`对象为:

```
let p = new Proxy(employee, validator); 
```

Enter fullscreen mode Exit fullscreen mode

如果有，

```
p.age = 'I am testing the blunder'; 
```

Enter fullscreen mode Exit fullscreen mode

输出将是一个`TypeError` as，

```
TypeError: Age is always an Integer, Please Correct it!
    at Object.set (E:\Projects\KOSS\metaprogramming\js-mtprog\proxy\userSetProxy.js:28:23)
    at Object.<anonymous> (E:\Projects\KOSS\metaprogramming\js-mtprog\proxy\userSetProxy.js:40:7)
    at Module._compile (module.js:652:30)
    at Object.Module._extensions..js (module.js:663:10)
    at Module.load (module.js:565:32)
    at tryModuleLoad (module.js:505:12)
    at Function.Module._load (module.js:497:3)
    at Function.Module.runMain (module.js:693:10)
    at startup (bootstrap_node.js:188:16)
    at bootstrap_node.js:609:3 
```

Enter fullscreen mode Exit fullscreen mode

同样，试着这样做！

```
p.age = -1; 
```

Enter fullscreen mode Exit fullscreen mode

### 用例

`Proxy Object`是一个非常强大的概念。有几个可以使用这个概念的用例。以下是一些例子:

*   保护 *ID* 字段不被对象删除(trap: deleteProperty)
*   跟踪属性访问(trap: get，set)
*   数据绑定(陷阱:设置)
*   可撤销的引用
*   操纵`in`操作符行为

...还有很多很多。

### 最后一注

希望你喜欢`Proxy Object`这个概念。试试吧，很好玩的！请随意访问来自[我的 Github Repo](https://github.com/atapas/js-mtprog/tree/master/proxy) 的示例。

“代理”并不是基于 JavaScript 的*元编程*的唯一概念，还有其他类似的，*反射*。很快就要到了。

这篇文章最初发表在我的博客上。*