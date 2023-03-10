# 理解有趣的对话(arrowFunction)。原型

> 原文：<https://dev.to/oathkeeper/a-fun-conversation-to-understand-arrowfunction-prototype-4n8>

我写这篇文章是为了记录我和/u/senocular 在 reddit 上的一次对话。我问的是箭头函数在词汇上绑定“this”是否不利？

后来，我好奇的发现 arrow 函数并不像普通函数那样在原型属性中有构造函数。

听起来你想问一件事，但实际上可能是两件事...？首先:

> ...如果箭头函数有自己的构造函数...

这是一个好问题。答案是否定的。虽然箭头函数是函数(显然)，并且继承自函数类型，但是它们没有自己的构造函数——至少没有一个特定于箭头函数的构造函数。在可用的函数构造函数中，有:

*   功能

*   生成器功能

*   异步功能

*   asynccgeneratorfunction

Function 实际上是唯一一个全局可访问的函数，其他函数只能通过用 function 关键字创建的各自函数类型的函数实例来访问。

```
console.log(Function) // Function () {}
console.log(GeneratorFunction) // Error, not defined
console.log(AsyncFunction) // Error, not defined
console.log(AsyncGeneratorFunction) // Error, not defined 
```

Enter fullscreen mode Exit fullscreen mode

但是

```
console.log(function * () {}).constructor) // GeneratorFunction () {}
console.log(async function () {}).constructor) // AsyncFunction () {}
console.log(async function * () {}).constructor) // AsyncGeneratorFunction () {} 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，箭头函数尽管具有独特的行为和语法，但仍被视为普通函数。

```
console.log((() => {}).constructor) // Function () {} 
```

Enter fullscreen mode Exit fullscreen mode

箭头功能的变化也与其各自的功能类型相关

```
console.log((async () => {}).constructor) // AsyncFunction () {} 
```

Enter fullscreen mode Exit fullscreen mode

注意:生成器不支持箭头功能，尽管在第 1 阶段有一个建议。

但是如果用函数构造器创建函数，它会有正常的函数行为，而不是箭头函数行为。

```
(() => this).call('value') // global (undefined in strict)
new Function('return this').call('value') // 'value' 
```

Enter fullscreen mode Exit fullscreen mode

你可以认为这是函数构造器的一个限制。它只是不支持创建箭头函数，就像它不支持创建闭包一样。

```
{
  let outer = 'value'
  new Function('return outer')() // Error, outer not defined
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，所有这些都可能与

> ...比如在原型中有多少正常的函数？

这要看你说的原型是什么意思了。你指的是函数的原型还是函数的原型属性？

作为对象，函数有指向它们各自类型的原型——它们继承自什么，或者更具体地说，它们类型的原型属性。例如，一个普通的函数实例从 Function.prototype 继承而来，这是它获取 call 和 apply 等方法的地方。从上面我们还可以看到，其他函数变体有自己的函数类型，如 GeneratorFunction 和 AsyncFunction，它们也有自己的原型属性。

```
console.log(Object.getPrototypeOf(function * () {}) === GeneratorFunction.prototype) // true 
```

Enter fullscreen mode Exit fullscreen mode

在这个意义上，箭头函数也有引用它们各自函数类型的原型。

```
console.log(Object.getPrototypeOf(() => {}) === Function.prototype) // true 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这仍然是函数，而不是像 ArrowFunction 一样独特的东西，因为 arrow 函数没有自己特定的构造函数类型。

另一方面，arrow 函数本身没有自己的 prototype 属性，这意味着它们不能作为构造函数使用，也不能自己表示一个类型。

```
const arrowFn = () => {}
console.log(arrowFn.prototype) // undefined
new arrowFn() // Error, not a constructor 
```

Enter fullscreen mode Exit fullscreen mode

事实上，大多数来自 ES6+的函数变体没有自己的原型属性，也不能成为构造函数。

```
const obj = { method () {} }
console.log(obj.method.prototype) // undefined
new obj.method() // Error, not a constructor

const asyncFn = async function () {}
console.log(asyncFn.prototype) // undefined
new asyncFn() // Error, not a constructor 
```

Enter fullscreen mode Exit fullscreen mode

例外是类函数，因为它们明确地表示构造函数(但是和上面的对象方法一样，类方法不能是构造函数)，以及生成器函数，它们是规范中的一个错误的结果，规范允许它们是 ES6 中的构造函数。这个错误在 ES7 中被修复了，虽然生成器不再是构造器，但是它们的原型属性仍然被保留了下来

```
const genFn = function * () {}
console.log(genFn.prototype) // Object {}
new genFn() // Error, not a constructor 
```

Enter fullscreen mode Exit fullscreen mode

归根结底，箭头函数只是普通函数的变体。它们有相同的构造函数，但是那些构造函数不能创建它们，箭头函数本身也不能是构造函数。