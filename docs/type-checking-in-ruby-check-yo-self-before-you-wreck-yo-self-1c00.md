# Ruby 中的类型检查——在毁掉自己之前检查一下自己

> 原文：<https://dev.to/appsignal/type-checking-in-ruby-check-yo-self-before-you-wreck-yo-self-1c00>

让我们以一个有趣的小猜谜游戏开始这篇文章:你认为在 Ruby 应用程序中 AppSignal 跟踪的最常见的错误是什么？

假设你们中的许多人用`NoMethodError`回答了这个问题，这是一个异常，它是由在对象上调用不存在的方法引起的。偶尔，这可能是由方法名中的拼写错误引起的，但更多情况下，这是对错误类型的对象调用方法的结果，这通常是意外的`nil`。作为 Ruby 开发人员，我们能做些什么来减少这种错误的发生呢？

## 救援类型？

除了文本编辑器或编程语言的选择之外，很少有话题能比类型系统的讨论更快地引发激烈的争论。我们没有时间在这里讨论细节，但是克里斯·史密斯的文章[“在讨论类型系统之前要知道什么”](https://blog.steveklabnik.com/posts/2010-07-17-what-to-know-before-debating-type-systems)在这方面做得很好。

从广义上讲，类型系统可以分为两大类——静态和动态。虽然前者发生在时间之前(通过编译器或单独的工具)，但动态类型检查发生在运行时，如果实际类型与开发人员的预期不符，它可能会导致异常。

这两种哲学的支持者都有很强的观点，但遗憾的是，也有许多误解:静态类型不需要大量的类型注释——许多现代编译器可以自己找出类型，这一过程被称为“类型推断”。另一方面，动态类型的语言似乎并没有比静态类型的语言表现出更高的缺陷率。

### 鸭子打字

Ruby 本身是一种动态类型检查语言，遵循“鸭式输入”方法:

> 如果它走路像鸭子，叫起来像鸭子，那它一定是鸭子。

这意味着，Ruby 开发人员一般不会太担心对象的类型，而是它是否响应某些“消息”(或方法)。

那么，你可能会问，为什么要在 Ruby 中费心使用静态类型呢？虽然它肯定不是让您的代码神奇地摆脱 bug 的灵丹妙药，但它确实提供了某些好处:

*   正确性:静态类型擅长防止**某些类别**的错误，就像前面提到的`NoMethodError`。
*   工具:通常，在开发过程中拥有可用的静态类型信息会带来更好的工具选择(例如，ide 中的重构支持，等等)。)
*   文档:许多静态类型语言都有很好的内置文档工具。Haskell 的 [Hoogle](https://hoogle.haskell.org/) 很好地利用了这一点，提供了一个搜索引擎，可以通过函数的类型签名来查找函数。
*   性能:编译器可以获得的信息越多，可以应用的性能优化就越多。

这个列表并不详尽，人们可以找到大多数这些观点的反例，但肯定有一个真理的核心。

### 渐进式检查

近年来，一种通常被称为“渐进类型检查”的方法已经侵入了各种动态类型检查语言:从 JS 的 [TypeScript](https://www.typescriptlang.org/) 到 PHP 的 [Hack](https://hacklang.org/) 和 Python 的 [mypy](http://mypy-lang.org/) 。这些方法的共同点是，它们不需要一种全有或全无的方法，而是允许开发人员在他们认为合适的时候逐渐向变量和表达式添加类型信息。这对于现有的大型代码库尤其有用，可以静态地检查系统中最关键的部分，而剩下的部分则不进行类型化，在运行时进行检查。我们将在本文余下部分探索的所有 Ruby 类型检查解决方案都遵循相同的方法。

## 选项

在了解了为什么 Ruby 开发人员可能想要将静态类型检查添加到他们的开发工作流中之后，是时候探索一些目前流行的选项来实现这一点了。然而，重要的是要注意，向 Ruby 添加静态类型检查的想法并不新鲜。来自马里兰大学的研究人员早在 2009 年就致力于名为 Diamondback Ruby (Druby)的 Ruby 扩展，塔夫茨大学编程语言小组在 2013 年发布了一篇名为 [The Ruby Type Checker](https://www.cs.tufts.edu/~jfoster/papers/oops13.pdf) 的论文，最终导致了 [RDL 项目](https://github.com/tupl-tufts/rdl)，该项目作为一个库提供了类型检查和按合同设计的功能。

### 冰糕

由 Stripe 开发的 [Sorbet](https://sorbet.org/) 是目前最受关注的 Ruby 类型检查解决方案，尤其是因为像 Shopify、GitLab、Kickstarter 和比特币基地这样的大公司都是封闭测试阶段的早期采用者。它最初是在去年的 Ruby Kaigi 期间[宣布](https://twitter.com/darkdimius/status/1002049138366730240)的，并于今年 6 月 20 日首次公开发布。果汁冰糕是用现代 C++编写的，尽管[马茨偏爱](https://twitter.com/amrabdelwahab/status/1141981078984548353)(引用:“我讨厌类型注释”)，他还是选择了基于类型注释的方法。关于 Sorbet 的一个特别有趣的事情是，它选择了静态和动态类型检查的组合，因为 Ruby 极其动态的特性和元编程能力对于静态类型系统来说是具有挑战性的。

```
# typed: true
class Test
  extend T::Sig

  sig {params(x: Integer).returns(String)}
  def to_s(x)
    x.to_s
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了启用类型检查，我们首先需要添加`# typed: true`魔法注释，并用`T::Sig`模块扩展我们的类。用`sig`方法:
指定实际的类型注释

```
sig {params(x: Integer).returns(String)} 
```

Enter fullscreen mode Exit fullscreen mode

它指定该方法接受一个名为`x`的类型为`Integer`的参数，并返回一个`String`。试图用错误的参数类型调用这个方法会导致错误:

```
Test.new.to_s("42")
# Expected Integer but found String("42") for argument x 
```

Enter fullscreen mode Exit fullscreen mode

除了这些基本的检查，果汁冰糕还有很多锦囊妙计。例如，它可以把我们从可怕的`nil` :
中拯救出来

```
users = T::Array[User].new
user = users.first
user.username

# Method username does not exist on NilClass component of T.nilable(User) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段定义了一个空的`User`对象数组，当我们试图访问第一个元素(它将返回`nil`)时，Sorbet 正确地警告我们在`NilClass`上没有名为`username`的方法可用。但是，如果我们确定某个值永远不可能是`nil`，我们可以用`T.must`让冰糕知道这个:

```
users = T::Array[User].new
user = T.must(users.first)
user.username 
```

Enter fullscreen mode Exit fullscreen mode

虽然上面的代码现在将进行类型检查，但它可能会导致运行时异常，所以要小心使用这个特性。

Sorbet 可以为我们做的还有很多:[死代码检测](https://sorbet.run/#%23%20typed%3A%20true%0Aif%20Random.rand%0A%20%20%20%20foo%20%3D%201%0Aelse%0A%20%20%20%20foo%20%3D%202%0Aend)、[类型锁定](https://sorbet.run/#%23%20typed%3A%20true%0Aa%20%3D%205%0Aa%20%3D%20%3Asym%0A%0Ab%20%3D%20T.let('str'%2C%20String)%20%23%20Pinned%20to%20a%20String%0Ab%20%3D%20%3Asym)(本质上是将变量提交给某种类型，例如，一旦它被赋值为字符串，就永远不能被赋值为整数)，或者定义[接口的能力](https://sorbet.run/#%23%20typed%3A%20true%0Amodule%20Fooable%0A%20%20extend%20T%3A%3ASig%0A%20%20extend%20T%3A%3AHelpers%0A%20%20abstract!%0A%20%20sig%20%7Babstract.void%7D%0A%20%20def%20foo%0A%20%20end%0Aend%0A%0Aclass%20GoodFooable%0A%20%20include%20Fooable%0A%0A%20%20def%20foo%0A%20%20end%0Aend%0A%0Aclass%20BadFooable%0A%20%20include%20Fooable%0Aend)。

此外，Sorbet 还可以处理“Ruby Interface”文件(`rbi`)，这些文件保存在当前工作目录下的`sorbet/`文件夹中。这允许我们为一个项目使用的所有 gems 生成接口定义，这可以帮助我们发现更多的类型错误。

Sorbet 的内容比我们在一篇文章中所能涵盖的要多得多(例如，不同的严格级别或元编程插件)，但是它的[文档](https://sorbet.org/docs/overview)已经非常好了，并且对 PRs 开放。

### 陡峭

最广为人知的冰糕替代品是松本刚太郎的[酱](https://github.com/soutaro/steep)。它不使用注释，也不自行进行任何类型推断。而是完全依赖于`sig`目录下的`.rbi`文件。

让我们从下面这个简单的 Ruby 类开始:

```
class User
  attr_reader :first_name, :last_name, :address

  def initialize(first_name, last_name, address)
    @first_name = first_name
    @last_name = last_name
    @address = address
  end

  def full_name
    "#{first_name}  #{last_name}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以用下面的命令搭建一个初始的`user.rbi`文件:

```
$ steep scaffold user.rb > sig/user.rbi 
```

Enter fullscreen mode Exit fullscreen mode

这将产生以下文件，该文件旨在作为起点(由所有类型都被指定为`any`这一事实来说明，这不提供安全性):

```
class User
  @first_name: any
  @last_name: any
  @address: any
  def initialize: (any, any, any) -> any
  def full_name: () -> String
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我们试图在此时输入 check，我们会遇到一些错误:

```
$ steep check
user.rb:11:7: NoMethodError: type=::User, method=first_name (first_name)
user.rb:11:21: NoMethodError: type=::User, method=last_name (last_name) 
```

Enter fullscreen mode Exit fullscreen mode

我们看到这些的原因是，Steep 需要一个特殊的注释来知道哪些方法是通过`attr_reader` s 定义的，所以让我们加上:

```
# @dynamic first_name, last_name, address
attr_reader :first_name, :last_name, :address 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们需要向生成的`.rbi`文件中添加方法的定义。当我们这样做的时候，让我们也把签名从`any`改为实际类型:

```
class User
  @first_name: String
  @last_name: String
  @address: Address
  def initialize: (String, String, Address) -> any
  def first_name: () -> String
  def last_name: () -> String
  def address: () -> Address
  def full_name: () -> String
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，一切都像预期的那样工作，并且`steep check`没有返回任何错误。

除了我们到目前为止所看到的，Steep 还支持泛型(例如`Hash<Symbol, String>`)和联合类型，这代表了在几个类型之间的非此即彼的选择。例如，用户的`top_post`方法可以返回用户写的排名最高的帖子，或者如果他们还没有贡献任何东西，则返回`nil`。这通过联合类型`(Post | nil)`来表示，相应的签名如下所示:

```
def top_post: () -> (Post | nil) 
```

Enter fullscreen mode Exit fullscreen mode

虽然 Steep 的功能肯定比 Sorbet 少，但它仍然是一个有用的工具，而且似乎更符合 Matz 所设想的 Ruby 3 中的类型检查。

### 红宝石型剖面仪

来自 Cookpad 的 Yusuke Endoh(在 Ruby 开发人员圈子里被称为“mame”)正在开发一个所谓的 1 级类型检查器，称为 [Ruby Type Profiler](https://github.com/mame/ruby-type-profiler) 。与这里介绍的其他解决方案不同，它不需要签名文件或类型注释，而是试图在解析 Ruby 程序时尽可能多地推断它。虽然它捕捉到的潜在问题比浸泡或冰糕少得多，但它不会给开发者带来额外的成本。

## 总结

虽然没人能预测未来，但 Ruby 中的类型检查似乎已经成为了一种趋势。目前，正在努力标准化用于`.rbi`文件的“Ruby 签名语言”(可能由 Ruby Type Profiler 搭建),因此开发人员可以使用他们喜欢的任何工具。Steep 已经允许库作者将类型信息与他们的 gem 一起发布，Sorbet 也有类似的机制，其形式为 [sorbet-typed](https://github.com/sorbet/sorbet-typed) ，这是受 TypeScript 定义的 DefinitelyTyped 存储库的启发。如果您有兴趣帮助塑造 Ruby 类型检查的未来，现在是参与的好时机！