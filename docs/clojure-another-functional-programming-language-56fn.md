# Clojure:另一种函数式编程语言

> 原文：<https://dev.to/ashe/clojure-another-functional-programming-language-56fn>

我一直都知道 Lisp 语系，但一直害怕深入其中。它总是神秘的，并以各种语言的形式出现。然而，在看了丹尼尔·希金博瑟姆的书[[clo jure for the brave and true]后，我现在认识到](https://www.braveclojure.com/clojure-for-the-brave-and-true/)[clo jure](https://clojure.org/)是一种简单、愉快和强大的语言，每个人都应该尝试。Clojure 是一种动态类型的语言，这意味着尽管它像 Haskell(我最喜欢的语言)一样具有功能性，但它是一种完全不同的编写体验。不是在开始实现时考虑类型和将数据流从一种类型转换成另一种类型，而是开始用 Lisp 方式考虑所有事情。

## 基础知识

直到我学习了足够多的 Clojure 并开始使用其他人制作的库，我才真正意识到这意味着什么。我已经开始使用 [Space](https://aas.sh/project/space) ，并且非常喜欢使用 Clojure 和一个叫做[试剂](https://github.com/reagent-project/reagent)的包，这是一种使用[脸书反应](https://reactjs.org/)的方法。为了让我展示 Clojure 有多酷，我需要快速浏览一下函数的样子。在[勇敢 Clojure](https://www.braveclojure.com/) 的帮助下，我现在可以告诉你 ***任何人*** 大概 2-3 周就能学会这个。语言本身很简单，虽然概念可能有点复杂，但要运行简单的程序，它远没有 Haskell 复杂。

Clojure 运行在 [JVM](https://en.wikipedia.org/wiki/Java_virtual_machine) 上，可以以类似于 [C++](https://isocpp.org/) 与 [C](https://en.wikipedia.org/wiki/C_%28programming_language%29) 交互的方式与 [Java](https://go.java/index.html?intcmp=gojava-banner-java-com) 交互(尽管它们的编写方式远不如 C++和 C 那么相似)。同样像 Java 一样， [ClojureScript](https://clojurescript.org/) 以与常规 Clojure 几乎相同的方式存在和编写，ClojureScript 可以与 [JavaScript](https://www.javascript.com/) 交互，就像 Clojure 如何与 Java 交互一样——是的，这意味着您*可能*不需要为您的 web 应用编写任何 `JS`代码！

我不想重写 [Brave Clojure](https://www.braveclojure.com/) (是的，我每次提到这本书都会插上一句，因为它绝对不可思议)，因为坦白说，没有什么可以改进的。这篇博文我只说明我觉得有必要的。

```
; Global, immutable variable  (def  the-number-one  1)  ; Print it  (print  the-number-one)  ; Function call example (returns 6)  (+  the-number-one  2  3)  ; Function definition  ; Note: The last line is what is returned  (defn  add-one-to-number  "This is a docstring, it should describe the function."  [num]  (+  num  the-number-one))  ; Declare a vector  (def  a-vector  [1  2  3])  ; Add one to everything in the vector and print it  ; Note the braces ARE NOT optional unlike Haskell where sometimes they can be  (print  (map  add-one-to-number  a-vector)) 
```

Enter fullscreen mode Exit fullscreen mode

Lisp 中的每个函数调用都使用大括号，没有逗号。你把函数名放在开头，然后*所有的*参数放在后面。如果你了解 Haskell，请注意[奉承](https://wiki.haskell.org/Currying)不是默认完成的(如果你想部分应用函数，使用函数`partial`)。虽然还有很多功能，但我不打算一一介绍，因为 [Brave Clojure](https://www.braveclojure.com/) 涵盖了你需要知道的一切。然而，关于`add-one-to-number`的参数，我需要注意一些事情。

首先，Clojure 是动态类型的，因此尽管我们在谈论数字是显而易见的，但只要`+`函数接受正确的类型，这个函数就能工作。我们不需要像在 Haskell 中那样指定一个变量或一组变量，因为它是动态的 T2。

第二，**整**行都是参数。如果你熟悉 Haskell 的绑定变量的[模式匹配](https://en.wikibooks.org/wiki/Haskell/Pattern_matching)，这是相似的。`[num]`代表一个`vector`，其中`num`是第一个元素。由于 Clojure 是一种动态语言，任何东西都可以是`nil`，所以如果 vector 是空的，只要`num`被检查为不是`nil`就不会出错。这里有更多的例子来说明这个功能:

```
; Add one to a number and print the result before returning  ; Note: docstrings are optional  ; Note: new-num is NOT a function but a variable and so there's no braces  (defn  add-one-to-number  [num]  (let  [new-num  (+  1  num)]  (print  new-num)  new-num))  ; Add two numbers  (defn  add-two-numbers  [a  b]  (+  a  b))  ; Print all elements given as a vector  ; Note: This will place elements into a vector for printing purposes  (defn  print-args  args  (print  args))  ; Function with optional arguments  ; Note: Optional arguments come after the & in their own vector  ; Note: This isn't a good idea as other arguments given are silently ignored  (defn  print-up-to-two-nums  [&  [a  b]]  (print  a)  (print  b))  (print-up-to-two-nums  1  2  3)  ; => prints 1 and 2, 3 is ignored but consumed 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，使用`let`绑定一个值，变量`new-num`只能在 let 调用的范围内访问。它不能用在 let 调用的大括号之外。对于一些人来说，这可能看起来难以理解，但是您很快就会习惯在编写 Lisp 代码时使用大量的大括号。

[![A dumb but funny lisp meme](img/72db8b177739989a8f96dc76257ca9e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GjM7PMor--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/aas-sh/image/upload/v1617292932/blog/2019/07/languages_meme_lisp_smdrnm.png)

现在让我们再来看看那个[试剂](https://github.com/reagent-project/reagent)库。

```
(defn  some-component  []  [:div  [:h3  "I am a component!"]  [:p.someclass  "I have "  [:strong  "bold"]  [:span  {:style  {:color  "red"}}  " and red"]  " text."]]) 
```

Enter fullscreen mode Exit fullscreen mode

不要害怕！我们来分解一下。首先，参数列表和函数名在同一行，所以`[]`表示这个函数没有参数。我们知道它是一个函数，因为`defn`与`def`相对，表明它是一个函数而不是变量。以冒号开头的东西，如`:div`、`:h3`或`:p.someclass`是`keywords`——它们有点特殊，但在某种意义上它们可以被视为类似于字符串，你可以打印它们，检查相等性等等。这就是事情变得有趣的地方，因为这是我第一次接触 Lisp 哲学:

> 代码作为数据，数据作为代码:可以使用数据结构来表示可执行代码的思想。

## 代码即数据:思维方式不同

如果你做过一些 web 开发，这个片段可能对你有一些意义。如果你仔细观察这个结构，你会发现`Vector`是嵌套的，并且包含各种不同的类型。试着在你的脑海中展平这个向量，你会发现它只是一个长长的变量列表，以`:div`开始，然后是另一个包含`:h3`和`"I am a component!"`的`Vector`，以此类推。

查看[试剂的](https://github.com/reagent-project/reagent)自述，可以看到[打嗝](https://github.com/weavejester/hiccup)用来表示 [HTML](https://www.w3schools.com/html/) 。Hiccup 将获取一个很长的数据，并将其转换成试剂可以使用的东西。如前所述，Reagent 允许您将 React 与 Clojure 一起使用，后者允许您创建 web 应用程序。通俗地说，那个片段让你不用写标准的 HTML 就能创建一个网站！让我们来更详细地了解一下:

```
; Vector of stuff. Imagine if you got this from some database or user interaction  (def  names  ["Ashley"  "Totoro"  "Kiki"])  ; Bullet point list of names  ; This function maps an anonymous function onto the above Vector  ; This anonymous nothing but returns another Vector with :li prepending the name  (defn  some-component  []  [:div  (when  (not  (empty?  names))  [:h1  "Cool people:"])  [:ul  (map  (fn  [name]  [:li  name]))  names]])  ; This is how you'd combine components together  (defn  your-web-page  []  [:div  [header]  [navbar]  [:article  [content]  [some-component]  [some-other-component]]]) 
```

Enter fullscreen mode Exit fullscreen mode

我试图把这个例子变得更复杂一些。我们在`names`中有一些非常酷的角色的数据。我们的目标是创建一种方法来动态地将这些名称呈现到页面上(在 Reagent 中，如果您使用一个使用`atom` s 的可变变量，页面实际上会发生变化),而不需要硬编码或做任何费力的事情。函数`When`有两个参数，第一个是条件，第二个是你希望这个函数调用在`true`时计算的任何值，而不是在`false`时的`nil`。只有在`names`中有一个或多个元素时，页面才会显示`Cool People:`。Hiccup 将忽略任何内部数据，因此如果`names`变成空的，元素将从 [DOM](https://www.w3schools.com/js/js_htmldom.asp) 中消失。

接下来，一个函数被`map` ped 到`names`中的每个名字上。该函数将元素转换成另一个带有`:li`的`Vector`(用于[列表项](https://www.w3schools.com/TAGS/tag_li.asp))。如果 vector 中没有元素，将不会出现类似于`"Cool People"`的消息。这就产生了一个能够响应数据变化的网页，就像任何其他 React 应用程序一样——但是具有 Clojure 等语言的优势。

最后，当使用函数调用时，我们将它们封装到`Vector`中，因为我们实际上并不在这里调用它们——虽然我们可以调用它们，但如果我们使用`Vector` s，Hiccup 会为我们处理它。虽然使用自己的组件的方式之间存在一些技术差异，但无论哪种方式都很容易。在`Vector`内部传递函数而不调用它们也很常见，这取决于你使用的框架。

这个兔子洞想挖多深就挖多深，这种用函数交换数据的哲学非常强大。我使用 Clojure 作为[空间](https://aas.sh/project/space)的 API 服务器以及前端，代码和数据是同一件事的想法与我使用的所有库非常一致。虽然 Haskell 的库喜欢定义自己的[单子](https://wiki.haskell.org/Monad)和[类型](https://wiki.haskell.org/Type)，但 Clojure 的库似乎都处理普通类型和`Keyword`类型，因此它们可以轻松地使用彼此的数据。记住一个`Keyword`就像一个`String`，所以没有特殊的构造机制或者任何传递它们的规则，它们可以在与`String`相同的地方使用，通常用于与你找到的库进行特殊的交互，正如你在上面的代码片段中看到的。

## 最后的想法

Clojure 很棒，我在一周半内学会了基础知识，这都要感谢勇敢的 Clojure。一开始，大括号可能会让你有些不知所措，但是一旦你克服了它，Lisp 代码就会变得非常好用。它是一种动态语言，所以它确实为了方便而牺牲了安全性——我认为编写 web 应用程序是 Clojure 的一个完全有效的用例，但我仍然会考虑使用 Haskell 的类型系统，这样编译器可以帮助我在错误发生之前避免错误。在 Clojure 中，特殊变量很难在所有数据都是普通数据的情况下出现，所以只有当你试图将变量用作函数时，或者当你将函数用作变量而接收它的对象无法处理它时，你才会真正遇到错误。没有静态类型，更容易完成任务，但是更难避免运行时错误。

Haskell 完全是纯的，将东西打印到屏幕上会有点困难，尤其是当你试图将打印消息注入其他函数时(你会用`IO Monad`等感染一切，我不会深入讨论，但你可以绕过它)。在 Clojure 中，单子不是真正的东西，语言本身也不是严格纯粹的，尽管纯粹的函数被高度鼓励和激励。这意味着你可以用同一个函数做多件事，比如我打印了一个数字，然后在上面的一个片段中返回它。同样， ***Clojure 很容易*** 和可爱的工作。我仍然喜欢 Haskell 和它的类型系统，我当然可以看到许多我肯定会在 Haskell 之上使用 Clojure 的有效情况。

### 感谢阅读。