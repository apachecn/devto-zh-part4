# Javascript 中带有 Maybe 的函数类型安全

> 原文：<https://dev.to/devinholloway/functional-type-safety-in-javascript-with-maybe-55cg>

> 在编程语言(函数式编程语言更是如此)和类型理论中，选项类型或可能类型是表示可选值封装的多态类型；例如，它被用作函数的返回类型，这些函数在被应用时可能返回也可能不返回有意义的值。它由一个构造函数组成，这个构造函数要么是空的(通常命名为 None 或 Nothing)，要么封装了原始数据类型 A(通常只写 A 或某个 A)。
> - <cite>[百科](https://en.wikipedia.org/wiki/Option_type)</cite>

让我们来谈谈这意味着什么，为什么有用，以及如何在 Javascript 中利用这个概念。

Javascript 是一种非类型化语言，这使得它非常灵活，在某些情况下非常强大。但是伴随着权力而来的是巨大的责任。举个例子，一个函数被设计用来操作一个字符串:

```
const capitalize = a => a.charAt(0).toUpperCase() + a.slice(1)

capitalize('javascript') //=> "Javascript" 
```

现在用字符串替换任何其他数据类型:

```
capitalize(5) //=> a.charAt is not a function
capitalize(true) //=> a.charAt is not a function
capitalize(['javascript']) //=> a.charAt is not a function
capitalize(null) //=> Cannot read property 'charAt' of null
capitalize(undefined) //=> Cannot read property 'charAt' of undefined 
```

任何做过大量 Javascript 工作的人都会认识到，不匹配的数据类型和 null/undefined 是运行时错误的常见来源。当然，有各种方法可以编写更安全的函数，通常被称为防御性编程:

```
const capitalize = a => (typeof a === 'string') 
  ? a.charAt(0).toUpperCase() + a.slice(1) : '' 
```

虽然这是一个安全得多的版本，但它会增加许多代码代码，尤其是当您需要将这些类型的检查分散在整个代码库中时。此外，它迫使你以一种更命令式的方式思考(并因此而写作)，而不是像函数式编程提倡的那样以一种更有表现力的方式。

我们处理空/未定义或类型不匹配的方式取决于它们是否应该出现，以及数据是否可以控制。例如，如果我们想要大写一个人名字的每一部分，而中间名不是我们数据中的一个要求，我们可以预期当给一个函数时它是不可用的(或空的)。在这种情况下，我们最好跳过函数调用，让其余的代码继续执行。这是我们从`Maybe`数据类型中得到的好处之一。

A `Maybe`是一个[求和类型](https://en.wikipedia.org/wiki/Tagged_union)，它可以表示另外两种类型中的一种；一个`Just`或者`Nothing`(或者一个`Some` / `None`，取决于语言)。你可以把它想成一个多态关系，其中`Just`代表一个正确的或有效的值，而`Nothing`代表一个不正确的、无效的或缺少值(比如 null)。

`Just`和`Nothing`都充当原始数据的容器或包装器。这样做的意义在于，知道如何使用`Maybe`的函数也可以使用`Just`或`Nothing`，即使原始数据是无效的。这些包装器都有相同的 API，允许它们互换。

这与 Javascript 原语的工作方式没什么不同。当您执行像`'javascript'.toUpperCase()`这样的代码时，并不是字符串本身附加了`toUpperCase()`函数。毕竟，字符串是一个原语，这意味着它没有函数或属性。相反，是 String()构造函数具有`toUpperCase()`功能，Javascript 会在调用构造函数函数/属性时自动包装原语。

让我们看一些实际的代码。对于本文中的例子，我们将使用 [Crocks](https://crocks.dev) 库。

有多种方法可以构造一个`Maybe`数据类型，比如使用`Maybe`构造函数本身:

```
const Maybe = require('crocks/Maybe')

Maybe('javascript') //=> Just "javascript"
Maybe.of('functional') //=> Just "functional"
Maybe.of(null) //=> Just null 
```

`Maybe`构造函数将总是产生一个`Just`。建议直接使用`Just`和`Nothing`构造函数，只是为了可读性:

```
Maybe.Just() //=> Just undefined
Maybe.Just('javascript') //=> Just "javascript"
Maybe.Nothing() //=> Nothing
Maybe.Nothing('javascript') //=> Nothing 
```

你也可以析构`Just`和`Nothing`来收紧你的代码:

```
const Maybe = require('crocks/Maybe')
const {Just, Nothing} = Maybe

Just() //=> Just undefined
Nothing() //=> Nothing 
```

但是你的大多数`Maybe`类型将会从助手函数中产生。本文的重点将放在安全的助手函数上。

`safe`接受一个谓词函数，该函数返回一个布尔值和一个应用于谓词的值。如果谓词返回 true，我们得到一个`Just`，否则，得到一个`Nothing` :

```
const Maybe = require('crocks/Maybe')
const safe = require('crocks/Maybe/safe')

const isString = a => (typeof a === 'string') 

safe(isString, 'javascript') //=> Just "javascript"
safe(isString, 5) //=> Nothing
safe(isString, null) //=> Nothing 
```

Safe 被简化了，允许我们用一个谓词预先配置它，然后传入数据。为了简洁，我们也将切换到 Crock 的内置`isString`函数:

```
const Maybe = require('crocks/Maybe')
const safe = require('crocks/Maybe/safe')
const isString = require('crocks/predicates/isString')

const safeString = safe(isString)

safeString('javascript') //=> Just "javascript"
safeString(5) //=> Nothing 
```

A `Maybe`(因此`Just`和`Nothing`)实现了广泛的代数结构，其中之一是[函子](https://hackernoon.com/functors-in-javascript-20a647b8f39f)，允许我们[映射](https://crocks.dev/docs/crocks/Maybe.html#map) a `Maybe`。

函子的规则之一是，当我们将一个值映射到另一个值时，我们会得到相同的类型和结构。如果我们映射一个数组，我们将得到一个相同大小的数组(具有不同的值)。如果我们映射一个`Maybe`，我们将得到一个`Maybe`。我们只影响内部的原始数据。让我们回到最初的 caplitalize 函数，并将它映射到我们的`Maybe` s:

```
const safeString = safe(isString)
const capitalize = a => a.charAt(0).toUpperCase() + a.slice(1)

safeString('javascript').map(capitalize) //=> Just "Javascript"
safeString(5).map(capitalize) //=> Nothing
safeString(null).map(capitalize) //=> Nothing 
```

当我们映射一个有效的(`Just`)值时，映射将从我们的`Maybe`中打开原始数据，将它传递到映射器(`capitalize`)，并重新包装结果。当我们试图映射一个无效的(`Nothing`)值时，映射器将被忽略，只返回一个新的`Nothing`。

这里要指出的是，我们的`capitalize`函数只是一个普通的 Javascript 函数，没有任何类型检查或空检查。事实上，我们的代码中没有任何类型/空检查。在`Maybe`类型中，这些都被抽象掉了。传递一个`safeString`来大写保证是没有错误的。

我要指出的另一件事是，一个无效的值不一定仅仅是产生错误的值。例如，一个空字符串可以被安全地转换为大写，但是没有意义。如果我们重写我们的`safeString`函数来排除无效的空字符串(并将其重命名为`validString`)，我们可以避免执行资本化函数的性能代价。这在实施昂贵的操作(如打服务电话)时会变得更有价值。

最后，总有一天你会准备好打开原始数据并丢弃`Maybe`容器。这通常是在流程的末尾，比如在屏幕上呈现值，或者将它传递给服务方法。这可以通过`Maybe`的[选项](https://crocks.dev/docs/crocks/Maybe.html#option)功能:
来完成

```
safeString('javascript').map(capitalize).option('') //=> 'Javascript'
safeString(5).map(capitalize).option('') //=> ''
safeString(null).map(capitalize).option(null) //=> null 
```

`option`接受一个参数，一个默认值，在展开`Nothing`时使用。展开`Just`时，默认被忽略，原始数据被返回。我会警告不要过早解开你的数据。对原始数据可以做的任何事情都不能在包装后对相同的数据做。我已经展示了一个用 map 转换包装数据的例子，但是包装数据还有更多功能应用。

这在很大程度上是用`Maybe`对类型安全的介绍。有许多更有用的应用程序使用`Maybe`以及其他结构来帮助以一种富有表现力的方式编写无错误的代码。我将在未来写一篇关于`Either`的文章，这是一个允许你处理错误(而不仅仅是避免不安全代码的执行)和消除使用`try` / `catch`的结构。