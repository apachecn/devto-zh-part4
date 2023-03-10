# 什么是编程语言？

> 原文：<https://dev.to/renegadecoder94/what-is-a-programming-language-58ih>

当我们大多数人想到编程语言时，我们会想到像 Python、C#和 JavaScript 这样的流行语言，但是我们在哪里划线呢？在这篇文章中，我想通过挑战一些常见的论点来突破传统智慧的界限，比如启发了这篇文章的论点:“HTML 和 CSS 不是编程语言。”

## 灵感

老实说，我过去并没有真正考虑过什么是编程语言。毕竟，我们只是写代码，这可以采取多种形式。谁来决定什么是编程语言，什么不是？

事实证明，关于 HTML 和 CSS 是否是编程语言，在 web 开发界有一场激烈的争论。事实上，我偶然发现了这场关于 dev.to 的辩论，在那里 [Desi 发起了一场关于主题](https://dev.to/desi/are-css-and-html-programming-languages-1lmn)的讨论。

幸运的是，这个平台很少爆发激烈的战争，所以你可以在这样的讨论中找到很多金块。当然，我想我应该插话:

> 就个人而言，我会说是的 HTML 和 CSS 是编程语言]，但我对编程语言的定义更灵活。例如，对于像 brainf*ck 这样深奥的语言或者像 yml 或 json 这样的数据语言，你可以问一个类似的问题。只要文本的内容被计算机解释/编译，它就是一种编程语言。
> 
> 现在，这肯定是有层次的，因为一些语言可以比其他语言完成更多，但这是一个不同的讨论。

就经验而言，我上过一些课，甚至写过一个玩具语言的编译器和一个 Lisp 解释器。现在，我只是在写作时维护包含 106 种语言的示例程序库(无耻的插件)。

做了这个评论后，我开始思考是什么让一个东西成为编程语言，所以我决定分享我的一些想法。

## 编程语言定义

如果你在互联网上搜索编程语言的定义，你会发现很多单词。例如， [Wikipedia 声明](https://en.wikipedia.org/wiki/Programming_language)“编程语言是一种正式语言，它由一组产生各种输出的指令组成。”同时， [TechTerms 将](https://techterms.com/definition/programming_language)编程语言描述为“用于创建软件程序的一组命令、指令和其他语法。”见鬼，这里还有几个定义:

*   [计算机希望](https://www.computerhope.com/jargon/p/proglang.htm):“编程语言是程序员用来开发软件程序、脚本或其他供计算机执行的指令集的特殊语言。”
*   韦氏词典:“任何一种用于计算机程序的高级语言”
*   Webopedia :“编程语言是一个词汇表和一组语法规则，用于指导计算机或计算设备执行特定的任务。”

我们能从这些定义中提取出某种统一的线索吗？换句话说，我们能根据这些定义提出任何语言分类的标准吗？对我来说，我们似乎只需要一些东西就可以称之为编程语言:

*   语法(即由指令、命令等组成的语法。)
*   语义(即赋予该语法的某种意义)

哦，我们应该可以在电脑上运行这种语言。现在，让我们看看我们能把这个定义延伸到什么程度。

## 考察各种语言

当考虑某个东西是否是一种编程语言时，看一看通常符合要求的语言对我们没有太大的好处。相反，我想看看 CSS 和 HTML 等处于边缘或刚刚超越边缘的语言。

### MATLAB

老实说，当挑战什么是编程语言，什么不是编程语言的想法时，MATLAB 似乎是一个奇怪的起点。毕竟很明显是编程语言吧？

如果你从未听说过 MATLAB，它本质上是工程师和数据科学家用来运行模拟和进行可视化的工具。和大多数编程语言一样，它有非常清晰的语法和语义:

```
sum1 = 0;
sum2 = 0;
N = 27
for k = 1:N 
  sum1 = sum1 + k; 
  if (mod(k, 7) == 0) 
    sum2 = sum2 + k; 
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

对于不经意的观察者来说，这看起来像编程——的确如此！然而，许多纯粹的编程主义者瞧不起 MATLAB。因为它不是用来创建应用程序的，所以它不是真正的*编程语言。*

当然，MATLAB 绝对是一种基于我们上面同意的定义的编程语言，让我困扰的是，有一个非常直言不讳的少数人将 MATLAB 程序员排除在真正的程序员之外。例如，我的一些机械工程朋友一直使用 MATLAB 来模拟他们的设计。他们至少在某种程度上不是程序员吗？

### HTML

如果你不熟悉 HTML，它是一种标记语言，通常用于指定网页的结构。具体来说，它是一种类似树的语言，其中文档有一个根节点(即`<html>`)，可以包含任意数量的子节点(即`<head>`、`<body>`等)。).自然这些孩子可能会有孩子等等。

```
<html xmlns="http://www.w3.org/1999/xhtml"> 
  <head> 
    Hello, World! 
  </head> 
  <body> 
    <h1>Check Out This Heading</h1> 
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在，HTML 通常在编程语言辩论中遇到障碍，因为它不执行任何动作；它是静态的。换句话说，没有流量控制。相反，HTML 依靠浏览器来解释和显示它。

也就是说，构造一个 HTML 文档仍然需要相当多的逻辑。理解浏览器如何呈现 HTML 与理解 JVM 如何运行 Java 程序没有什么不同。在这两种情况下，开发人员都在编程。

如果你不相信这种逻辑，至少，HTML 符合我们定义的所有特征。特别是，它有明确定义的语法，并且该语法对浏览器(恰好在计算机上)有明确定义的含义。

### CSS

除了 HTML，你经常会发现一种叫做 CSS 的有趣的语言。据我所知，CSS 通过对节点应用属性来设计 HTML 的样式。

```
h1 { 
  display: block; 
  font-weight: bold; 
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

结合 HTML，CSS 实际上可以用来驱动用户交互的逻辑。换句话说，只使用 HTML 和 CSS 就可以用按钮来改变网页的状态。对我来说，这听起来像一种编程语言。

当然，CSS 遇到了障碍，因为它依赖于 HTML。一个人的话，其实没什么用。也就是说，我认为这是一种愚蠢的比较，因为对于所有编程语言来说都是如此(也就是说，没有 JVM，Java 什么都不是)。所有语言都需要其他软件——或者至少是硬件——来赋予它意义。

因此，我认为 CSS 非常符合我们的编程语言定义。毕竟，它有清晰的语法和语义(针对每个浏览器)，并且可以在计算机上愉快地运行。

### SVG

现在，我们开始进入一些严重的灰色地带:数据格式。在这种情况下，我想谈谈像 JSON，XML，SVG，甚至 CSV 和 Markdown 这样的格式。这些是编程语言吗？我要说是的。

让我先问一个问题:你曾经尝试过编写 SVG 吗？如果你有，那么你就知道这有多有挑战性

```
 
  <polygon points="200,10 200,190 120,210" style="fill:purple;stroke:aqua;stroke-width:2" />
 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们绘制了一个三角形，它可以被许多能够理解 SVG 格式的工具(例如浏览器)解释和绘制。换句话说，SVG 是一种编程语言，因为它有明确定义的语法，可以从浏览器等特定环境中获得意义。

### XML

如果 HTML 和 SVG 是编程语言，那么 XML 无疑是一种编程语言。嗯，这就有点难证明了。

```
<CATALOG> 
  <CD> 
    Empire Burlesque 
    <ARTIST>Bob Dylan</ARTIST> 
    <COUNTRY>USA</COUNTRY> 
    <COMPANY>Columbia</COMPANY> 
    <PRICE>10.90</PRICE> 
    <YEAR>1985</YEAR> 
  </CD> 
  <CD> 
    Hide your heart 
    <ARTIST>Bonnie Tyler</ARTIST> 
    <COUNTRY>UK</COUNTRY> 
    <COMPANY>CBS Records</COMPANY> 
    <PRICE>9.90</PRICE> 
    <YEAR>1988</YEAR> 
  </CD>
</CATELOG> 
```

Enter fullscreen mode Exit fullscreen mode

虽然 XML 有明确定义的语法，但它的含义却不太清楚。毕竟，XML 和 JSON 一样，主要用于携带数据，所以它只在特定情况下才有意义，比如作为 SVG。

然而，因为如此多的工具依赖 XML 作为配置格式(见 [Maven](https://maven.apache.org/pom.html) )，XML 应该被认为是一种扩展的编程语言。换句话说，XML 只有在上下文中才有意义，但这并没有降低它作为编程语言的地位。毕竟 Python 对 GCC 来说没有任何意义，但我们还是认为它是一种编程语言。

### JSON，CSV 等。

既然我们谈到了数据格式，那么 JSON、CSV 和 Markdown 怎么样？它们都有明确定义的语法，但是我们能从中推导出任何含义吗？自然，答案是肯定的:

```
{  "menu":  {  "id":"file",  "value":"File",  "popup":  {  "menuitem":  [  {  "value":"New",  "onclick":"CreateNewDoc()"  },  {  "value":"Open",  "onclick":"OpenDoc()"  },  {  "value":"Close",  "onclick":"CloseDoc()"  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我选择分享一个示例 JSON，它应该让人想起 XML(这意味着 JSON 确实是一种编程语言)。当然，和 XML 一样，JSON 从上下文中获得含义。例如，JSON 也可以用于配置文件。事实上，我见过一种类似的语言，YAML，在建立像 Travis CI 这样的持续集成平台时，就是这样使用的。配置文件不是一种编程形式吗？

同样，CSV 是一种本身没有任何意义的数据格式。像 XML、JSON、YAML 甚至 HTML 一样，CSV 依赖于程序来解释它。微软对 Excel 不就是这么做的吗？Google Sheets 怎么样？这些程序解释和显示 CSV 数据，赋予数据意义。

最后，Markdown 是一种文本格式化语言，通常用于为网页生成 HTML。这是否因为它简化了一个过程而使它不那么像一门语言？我敢肯定，这和那些拒绝从 COBOL 和 FORTRAN 继续前进的人对 Python 和 Java 等高级语言提出的观点是一样的。换句话说，守门的艺术。

### 活页乐谱

现在，我们开始进入浑水，但是听我说:乐谱也可以是一种编程语言。

[![Handwritten Sheet Music](img/c692e7ea0756b955fe0e85c5de4c4e55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F9AlEaLP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/therenegadecoder.com/wp-content/uploads/2019/06/vintage-2792562_1280.png%3Ffit%3D800%252C800%26ssl%3D1)

当我决定写这篇文章时，我实际上是在排练。当时，我在想，我们诠释音乐的一些方式是如何模棱两可的。例如，指挥说我们应该像弹奏八分音符一样弹奏一个短的四分音符，这使得一个萨克斯管演奏者质疑指挥的意思。他想要八分音符后面跟着八分休止符吗？断奏四分音符怎么样？

无论如何，那时我开始思考:活页乐谱是一种编程语言吗？毕竟，作曲家实际上只是一个程序员，试图通过一种具有特定语法(如常见的音乐符号)和语义的语言，从合奏中获得正确的声音组合。

事实上，通用音乐符号有各种机制，反映了流行编程语言中的控制流。例如，有类似于循环的重复符号，告诉音乐家何时停止的双小节，以及让人想起行号的测量数字。

当然，我们在最初的定义中遇到了障碍。毕竟，乐谱是由音乐家而不是计算机来诠释和运行的。此外，我不认为音乐家会喜欢被称为计算机，但我不认为这种比较是那么遥远。

## 编程语言的分类

在这一点上，我们已经看了很多语言，并试图提出一些关于它们是否是编程语言的争论。由于编程语言缺乏具体的定义，我愿意认为任何有正式语法和派生含义的东西都可以被认为是编程语言。然而，并不是所有的编程语言都是一样的。

一般来说，有几种编程语言类别:命令式、函数式、结构化、声明式等。今天，由于多范式语言，我们已经模糊了这些类别之间的界限。也就是说，我仍然认为这些类别至少在证明像 HTML、XML 和 JSON 这样的语言是真正的编程语言方面起到了重要的作用。

大多数人们倾向于描述为非编程语言的语言——HTML、CSS、XML 等。—是真正的声明式编程语言。换句话说，我们使用这些语言来声明我们希望我们的系统做什么，而不是如何做。

为了在伤口上撒一点盐， [Wikipedia 实际上列出了相当多的声明式编程语言](https://en.wikipedia.org/wiki/Declarative_programming)，包括 Make、SQL、HTML 和 Prolog。事实上，正则表达式被认为是一种声明式编程语言。这对于扩展编程语言的定义界限来说如何？

## 什么是编程语言？

如果我可以证明活页乐谱是一种编程语言，那么为什么我们还在争论什么是编程语言，什么不是呢？

这并不是说编程语言不应该被分类。毕竟，这也有一些优点。然而，当我们试图通过说“你不是真正的程序员，因为你用 *x* 编程”来贬低对方时，这让我很困扰

在一天结束的时候，谁会在乎别人用什么编码呢？我们在这里谈论的是工具，我不认为我听说过有人因为使用以下任何工具而受到指责:

*   铅笔(哇，我真不敢相信你用的是 2 号铅笔。)
*   锤子(德瓦尔特？真的吗？)
*   计算器(TI-84？试试 TI-86，失败者！)

看到这听起来有多傻了吗？让人们编码，好吗？

一如既往，感谢您在百忙之中抽出时间来支持我的工作。除了编码，我还喜欢写作，我希望像这样的内容能帮助到一些人。如果你喜欢这篇文章，一定要给它分享一下。另外，考虑一下[成为会员](https://therenegadecoder.com/members/)或者至少[加入邮件列表](https://newsletter.therenegadecoder.com/)，这样我们就可以保持联系。既然您在这里，为什么不看看其他一些文章呢:

[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 测试私有方法是可以的

### 杰里米·格里夫斯基 6 月 21 日 197 分钟阅读

#java #testing #healthydebate #codequality](/renegadecoder94/it-s-okay-to-test-private-methods-1dj9)[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 为什么我鼓励我的学生使用 DrJava

### 杰里米·格里夫斯基 6 月 13 日 196 分钟阅读

#java #beginners #computerscience #teaching](/renegadecoder94/why-i-urge-my-students-to-use-drjava-3h5b)[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 复制可变数据类型时要小心

### 杰里米·格里夫斯基 5 月 6 日 196 分钟阅读

#python #beginners #tutorial #coding](/renegadecoder94/be-careful-when-copying-mutable-data-types-2loa)

好了，暂时就这样吧！再次感谢。

什么是编程语言？最先出现在[的《变节的程序员》](https://therenegadecoder.com)。