# 管道:先传入

> 原文：<https://dev.to/cedretaber/pipeline-pass-it-in-first-58hh>

Ruby 的主干中引入了一个管道操作符，它有一个有争议的论点。关于这个“管道运营商”本身，似乎已经说了各种各样的话。

我在@mame 的博客中推荐[这篇文章，因为它组织得非常好，也很容易理解。](https://mamememo.blogspot.com/2019/06/a-brief-history-of-pipeline-operator.html)

注意:Matz 说这个操作符的名字和字符将被改变。
[功能#15799 注-29](https://bugs.ruby-lang.org/issues/15799#note-29)

## 做第一个，还是做最后一个

Isabelle/ML 和 F#中的原始管道操作符表现为“将右侧表达式的结果应用于左侧表达式的结果”。如@mame 的文章所述，这可以结合基于 ML 的语言的特点，即函数是自动柯里化的，函数应用的语法优先级高于运算符。它有“将左侧表达式的结果作为最后一个参数传递给右侧函数调用”的意思，可以产生各种好处。

此外，对于像 F#和 OCaml 这样的语言，这个操作符可以定义为用户定义的操作符，而不需要专门的构造。

另一方面，Elixir 的管道操作符表现为“将左边表达式的结果作为第一个参数传递给右边的函数调用”。这种“第一个或最后一个”的区别是有实际原因的，但我不会在这里详细介绍。问题是，如果我们把左边的值作为右边函数调用的第一个参数，我们需要把它作为一个语言特性包含进去。

事实上，像 Elixir 和 Clojure 这样的语言通过使用宏来执行相同的行为，但是改变 AST 的需求仍然是相同的。原因在于，在函数自然生成的环境中，缺少最后一个参数的函数应用本身是有效的表达式，但是缺少第一个参数的函数应用是无效的表达式。

由于这个原因，原本只是一个函数的管道操作符变成了一种特殊的语法。

## 各种语言的管道操作符

Ruby 的管道操作符有更不同的含义，表现为“左边表达式的结果是接收者，调用右边作为方法”。换句话说，这个操作符本质上是低优先级的`.`和`::`。从最初的 F#或其他函数式语言的角度来看，这似乎是非常奇怪的行为，但是 Ruby 提到的 Elixir 的管道操作符也与 F#的非常不同。

嗯，我以 F#、Elixir 和 Ruby 的管道操作符为例，但它们并不是唯一拥有管道操作符的语言。除了 Elixir 之外，还有其他语言的管道操作符表现为“作为第一个参数传递”。

### Clojure:线程宏

是的，它是一个宏。不过既然有类似的用法，我就介绍一下。

[Clojure 线程宏](https://clojure.org/guides/threading_macros)

```
(defn  transform  [person]  (update  (assoc  person  :hair-color  :gray)  :age  inc))  (transform  {:name  "Socrates",  :age  39})  ;; => {:name "Socrates", :age 40, :hair-color :gray} 
```

这个函数接收一个 map，将`:hair-color`放入其中，并增加它的`:age`。`assoc`函数具有签名`(assoc map key val)`，并在第一个参数中添加了一个键-值对。`update`函数有签名`(update map key f)`并更新第一个参数的值。

因为多个函数是嵌套的，所以很难阅读。当像这样对代码进行修改时，你也必须努力。

用 Elixir 编写的等价过程是这样的:

```
def transform(person), do:
  Map.update!(Map.put(person, :hair_color, :gray), :age, & &1 + 1) 
```

这也很难读懂。

使用线程宏时，代码如下:

```
(defn  transform*  [person]  (->  person  (assoc  :hair-color  :gray)  (update  :age  inc))) 
```

让我们用管道操作符重写使用 Elixir。

```
def transform(person), do:
  person
  |> Map.put(:hair_color, :gray)
  |> Map.update!(:age, & &1 + 1) 
```

与管道操作符不同，Clojure 的线程宏只在表单序列的开头使用一次。将目标值赋予宏的第一个参数，随后是任意数量的函数，这些函数将要处理的值作为第一个参数。然后，宏将重新排列表单，并将每个表单的结果插入到下一个表单的第一个参数中。

这个宏叫做线程优先宏。Clojure 中还有其他类似的宏，一个是作为最后一个参数传递的 thread-last 宏`->>`，另一个是 thread-as 宏`as->`，它可以传递到任何地方。你可以用一些适合你想做的事情。

为什么同时有“作为第一个参数”宏和“作为最后一个参数”宏？原因是 Clojure 的内置函数有一个约定，处理序列的函数以序列作为最后一个参数，处理集合等数据的函数以该数据作为第一个参数。

所以为了方便起见，Clojure 必须准备两个宏。

再说一遍，Clojure 的线程宏只是宏，并不是语言内置的特殊功能。它不使用特殊的语法，而是仅使用语言的本地特性来构建。这不仅限于 Clojure，其他一些类似 Lisp 的语言也有这样的宏。Lisp 的简单性和可扩展性使得这样做成为可能。

### BuckleScript/Reason:管道优先

BuckleScript 是一个将 OCaml 转换成 JavaScript 的编译器，是对 OCaml 编译器的修改。从这个意义上说，它几乎和 OCaml 是同一种语言，但是它也包含了自己的扩展，比如这里介绍的 pipe first 操作符。

Reason 是一种编译成 BuckleScript 的语言，语法接近 JavaScript。Reason 是结合 BuckleScript 做的，从 Reason 语言转换成 BuckleScript，再从那里转换成 JavaScript。

这些语言几乎与 OCaml 兼容，并且使用与 OCaml 相同的类型系统。它是一种高度实用的语言，具有很强的类型推断和类型安全，并且还允许破坏性的更改。

因为它最初是 OCaml，所以它还包含一个管道操作符，该操作符的作用是“将右侧表达式的结果应用到左侧表达式的结果”。这是从 F#导入回 OCaml 的代码。

另一方面，还有一种类型的管道操作符，它将一个值作为 BuckleScript/Reason 中的第一个参数进行传递。那就是“管道第一运营商”。

BuckleScript: [管先](https://bucklescript.github.io/docs/en/pipe-first)
原因:[管先](https://reasonml.github.io/docs/en/pipe-first)

正如官方网站所说的“管道语法”，这不仅仅是一个操作符，而是一个语法。

```
let transform person =
  updateAge (setHairColor person `gray) ((+) 1) 
```

先用管道改写这个:

```
let transform person =
  person
  |. setHairColor `gray
  |. updateAge ((+) 1) 
```

BuckleScript 的管道第一是`|.`。

接下来，我们来看看 Reason 中的示例代码:

```
let transform = person =>
  updateAge(setHairColor(person, `gray), (+)(1)); 
```

它是这样改写的:

```
let transform = person =>
  person
    ->setHairColor(`gray)
    ->updateAge((+)(1)); 
```

理智的管道首先是`->`。

和 Elixir 的一样，所以特性没什么特别的。在这里，我将解释为什么将作为第一个参数传递的管道操作符类型添加到已经有作为最后一个参数传递的管道操作符类型的语言中。

如上所述，BuckleScript/Reason 是一种翻译成 JavaScript 的语言。作为一个设计概念，它强调与 JavaScript 的联系。他们需要在 BuckleScript/Reason 代码中提供使用 JavaScript 中定义的函数的方法。

然而，BuckleScript/Reason 和 JavaScript 有完全不同的类型系统，所以不能原样引入类型定义。因此，通过在 BuckleScript/Reason 代码中定义 JavaScript 函数类型，您将 JavaScript 函数引入了 OCaml 世界。

```
function transform(person) {
    return updateAge(setHairColor(person, "gray"), n => n + 1);
} 
```

```
type person

external transform: person -> person = "transform" 
```

如果只是函数的话还好，如果是方法的话就有点棘手了。你可以把一个方法想象成一个函数，它接收隐式的`this`作为参数:

```
external map : 'a array -> ('a -> 'b) -> 'b array = "map" [@@bs.send]
external filter : 'a array -> ('a -> 'b) -> 'b array = "filter" [@@bs.send] 
```

`[@@bs.send]`是 BuckleScript 中引入的注释，如果这个被附加到`external`上，那么这个函数就被视为对第一个参数表达式的方法调用，并执行 interop。

```
let person2 = transform person1

let arr = map [|1;2;3|] (fun n -> n + 1) 
```

每个转换如下:

```
var person2 = transform(person1);

var arr = [1,2,3].map(function(n) { return n + 1 }); 
```

不带`[@@bs.send]`的`transform`函数被转换为普通函数，带`[@@bs.send]`的 map 函数被转换为方法调用，方法调用的第一个参数是接收者。

这里的问题是，被转换为方法调用的函数接收作为其第一个参数的值，即“主”参数。当然，这与现有的 OCaml 管道操作符不兼容。

```
(* Hmmm *)
let result = [|1; 2; 3|]
  |> (fun arr -> map arr (fun a -> a + 1))
  |> (fun arr -> filter arr (fun a -> a mod 2 = 0)) 
```

因此，管道首先被引入。如果用这个，可以写成:

```
let result = [|1; 2; 3|]
  |. map (fun a -> a + 1)
  |. filter (fun a -> a mod 2 = 0) 
```

这就像一个方法链。这看起来很自然，第一次引入这个管道是为了模仿方法链。BuckleScript/Reason 的管道操作符是由于需要使用具有方法调用的 JavaScript 而引入的。

### d:ufc

D 语言有一个特性叫做统一函数调用语法。这就是它所说的:“你可以用方法调用的外观来编写函数调用”。

这是什么意思？这是原码:

```
Person transform(Person person)
{
    return updateAge(setHairColor(person, "gray"), n => n + 1);
} 
```

我们也可以这样写:

```
Person transform(Person person)
{
    return person
        .setHairColor("gray")
        .updateAge(n => n + 1);
} 
```

D 语言的 UFCS 是一个特性，你可以把函数调用`func(a, b, c)`写成`a.func(b, c)`。你可以像对待一个方法一样对待一个普通的函数(成员函数)。

[功能:UFCS](https://dlang.org/spec/function.html#pseudo-member)
[DLang 游](https://tour.dlang.org/tour/en/gems/uniform-function-call-syntax-ufcs)

当与 D 语言的模板结合使用时，这个 UFCS 就发挥作用了。比如 D 语言的集合库，就是为一个叫“range”的抽象对象做的。这个“范围”不继承特定的类或接口，所以集合操作是作为函数而不是方法提供的。

但是，对集合的操作通常希望是链式的。

```
writeln(filter!(x => x % 2 == 0)(map!(x => x + 1)([1,2,3]))); 
```

与 UFCS:

```
[1,2,3]
    .map!(x => x + 1)
    .filter!(x => x % 2 == 0)
    .writeln; 
```

这个特性是为了使函数调用看起来像方法调用，但在它的根本上，有一个想法是试图通过重新安排参数和函数以及管道操作符的顺序来提高可读性。

## 结论

我收集了提供管道操作符的语言，其行为与 Elixir 相同，“将左侧表达式的结果作为右侧函数调用的第一个参数传递”。有趣的是，我在这里介绍的所有语言都与面向对象语言密切相关。BuckleScript/Reason 和 D 语言显然把这个特性用于模仿方法调用。

“主要”论元是作为第一个论元还是最后一个论元，取决于语言的性质和文化。我们不应该希望来自不同语言的所有管道操作符都表现相同。事实上，可以两者兼得的语言，比如 Clojure 和 BuckleScript/Reason，为了方便起见，提供了两种类型的管道操作符。

我认为 Ruby 的管道操作符是非常独特的，它调用方法时左边是接收者，右边是方法名和参数。尝试新事物是令人兴奋的，所以自己尝试一下，感受一下它是什么样子的！