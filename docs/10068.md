# 反对！让我们来玩玩 JavaScript 中的原语

> 原文：<https://dev.to/martingaston/objection-let-s-play-with-primitives-in-javascript-4jj7>

我喜欢懒洋洋地度过周末，吃着与我体重相当的品客薯片，玩电子游戏，还真的钻研一些编程语言的基础知识，以了解正在发生的事情。我最近迷上了 JavaScript 中的方法和原语，因为我希望对面向对象和函数模式中的标准数据类型有更深的理解。

虽然你当然不需要担心太多这种事情——这实际上是语言在其单独的实现背后从你那里抽象出来的东西——但了解一下正在发生的事情还是很好的。

我将使用 JavaScript 来完成大部分工作，但也会使用 Ruby 和 Elixir 中的小部分内容，尽管您肯定不需要了解这两种语言。

让我们来玩一些 JavaScript:

```
const foo = 'foo'
typeof foo
// 'string' 
```

考虑一下不起眼的`string`，它是地球上最优秀的数据类型。弦是模范公民。他们整天努力工作，不问任何问题，只要他们有足够的现金，一年带一家人去度假几次，他们就很满足了。

在 JavaScript 领域，a `string`是它的六个原语之一，这有点像该语言的无限之石- `string`、`number`、`boolean`、`undefined`、`null`和`symbol`。JavaScript 中的原语是不可变的，尽管可以通过赋值被其他原语替换。原语是轻量级的，就像美味的 [Total 0%](https://uk.fage/yoghurts/fage-total) 数据类型一样，没有方法或属性——也没有随之而来的额外处理需求。

等一下，哇哇哇哇*非常感谢*，那你怎么能做*这个*？

```
foo.toUpperCase() // "FOO"
foo.length // 3 
```

嗯，除了`undefined`和`null`之外，每个原语还有一个互补的[原语包装对象](https://developer.mozilla.org/en-US/docs/Glossary/Primitive#Primitive_wrapper_objects_in_JavaScript)，可能不出所料，它包装了这些原语——所以这就是`String`、`Number`、`Boolean`和`Symbol`。

当你像凯尔·辛普森(❤️饰)一样聪明时，你可以[真正深入了解幕后发生的事情](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20&%20grammar/ch3.md)，但是本质上`String`对象(它仍然有自己的构造函数)将围绕给定的`string`原语创建一个对象——所以*不是*原语，它仍然可以用`valueOf`方法访问。我认为，这也大致类似于 Java 中原语的实现，但我肯定不是这方面的专家:我对 Java 了解最多的是 [FizzBuzz 企业版](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition)。

```
const bar = new String("bar")
typeof bar // 'object'
typeof bar.valueOf() // 'string'
bar.valueOf() === "bar" // true
bar === "bar" // false 
```

现在，当你试图访问一个原语上的方法或属性时，JavaScript 也会自动处理这种包装(通常也称为*装箱*或*自动装箱*)，然后在求值之后，它会立即自动将这个新的、临时的、一次性使用的对象扔给垃圾收集器。

```
const hello = "Hello!" // variable bound to string primitive
hello.shout = hello.valueOf().toUpperCase().concat("!!!!!") // create a String wrapper object and add a shout method, which is evaluated immediately and is then sent to the garbage collector
// "HELLO!!!!!"
hello.shout // creates a NEW String wrapper object, where shout doesn't exist, so returns undefined 
```

唉，可怜的`String`！我认识他。

你也不应该*曾经* **曾经**(可能)在现实生活中使用这些构造函数，尽管手动创建这些对象可能有意义——特别是对于重复循环之类的——会更有效，但是**反向**实际上是正确的。JavaScript 的实现已经解决了如何更有效地使用一次性包装对象的问题，这是一种节省时间和功能性的特殊混合，使得 JavaScript 处理起来忽冷忽热。

```
const { performance } = require('perf_hooks') // only needed for Node

function test_string(str, desc) {
  const t0 = performance.now()
  for (let i = 0; i < 10000000; i++) {
    str.length
    str.toLowerCase()
  }
  const t1 = performance.now()
  console.log(`${desc} took ${(t1 - t0) / 1000} seconds`)
}

test_string("Hello", "Wrapped string primitive");
test_string(new String("Hello"), "Instantiated String object");

// Wrapped string primitive took 0.011956994999200105 seconds
// Instantiated String object took 1.9173262799978257 seconds 
```

让我们把 JavaScript 放在一边，看看其他一些实现。我们的小`foo`在鲁比古老的禅宗花园过得怎么样？

```
foo = 'foo'
foo.class # String
foo.class.ancestors # [String, Comparable, Object, Kernel, BasicObject] 
```

在 Ruby 中，所有数据都由对象表示。这有助于 Ruby 成为一种优雅的面向对象语言，Ruby 编码人员可以在晚上轻松放松，而不必解压这些特别的白天恐惧。“Ruby 遵循 Smalltalk 语言的影响，为其所有类型提供方法和实例变量”，[说语言本身](https://www.ruby-lang.org/en/about/)。

通过追溯`String`对象的祖先，我们看到它——以及 Ruby 中的每个对象——都继承自`BasicObject`。

```
magic_number = 3
magic_number.class.ancestors # [Integer, Numeric, Comparable, Object, Kernel, BasicObject] 
```

因此，Ruby 中的所有数据类型都是对象，幕后没有包装/自动装箱，甚至 JavaScript 风格的原语概念也不存在。

因为一切都是以对象的形式开始的，这意味着你可以重新打开一个类并添加你自己的方法，我**依法要求**告诉你这可能相当危险:你应该总是*总是*总是**总是**确保你的代码是可维护的，把你的鼻子伸进标准库是在现实生活中导致彻底崩溃的一个好方法。话虽如此，还是*挺酷的*。

```
class String
  def shout
    self.upcase << "!!!!!"
  end
end

"hello".shout
# "HELLO!!!!!" 
```

在其他地方，在更多的函数式语言中，对象和类的概念通常是遥远的记忆——所以直接访问你的数据类型上的方法是不允许的。如果我们跟随我们古老的`foo`到 Elixir，我们可以看到我们现在不是访问属性和方法，而是将我们的数据类型作为参数传递给`String`模块中的函数。

```
foo = "foo"
is_bitstring(foo) # true
String.upcase(foo) # "FOO"
String.length(foo) # 3 
```

这只是 Elixir 中的一个短暂停留，但是很高兴看到我们的`foo`做得很好，尽管我们几乎认不出它们来自早期的 JavaScript 它们成长得太快了！