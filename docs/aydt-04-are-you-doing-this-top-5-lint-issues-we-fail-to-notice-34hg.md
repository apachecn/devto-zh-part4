# AYDT - 04(你这样做了吗)-我们没有注意到的 5 大棉绒问题。

> 原文：<https://dev.to/chintukarthi/aydt-04-are-you-doing-this-top-5-lint-issues-we-fail-to-notice-34hg>

在一个早已被遗忘的王国里，住着三个人，分别叫杰基和朱莉和他们的叔叔。

[![jackie chan adventures](img/eb85c58e44b2b925fcad5f7f1c08766b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--60bTWD_J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n9ritukrz5gukqkk68d2.jpg)

叔叔是一个老人，他总是试图教朱莉生活的艺术。但是朱莉，另一方面，认为她的叔叔是一个老人，他对生活一无所知。

但是朱莉尊重杰姬。至少在我们的幻想故事里是这样。😝每当杰基做了不酷的事情，她总是会纠正。这就是我们的“lint”在被引入 ruby 代码时所做的事情。

就像我们的叔叔一样，每当 rubocop 发现不标准的代码时，他都会纠正人们。

这个人被称为“Rubocop”
[![rubocop image](img/c875cea9bac700de43986033e297d9d9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--yNr1co3S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hhifafsvtw9hsjf2ytlz.png)

欢迎来到本系列的第四集:你在做 This❓.吗如果你还没有读过以前的文章，我强烈推荐你看看那些。你肯定会喜欢的💙。快乐编码👨🏻‍💻。

上周链接:
[https://dev . to/chintukarthi/aydt-03-are-you-do-this-programming-and-cases-1 enj](https://dev.to/chintukarthi/aydt-03-are-you-doing-this-programming-and-cases-1enj)

你在做我们没有注意到的 this❓- 5 大皮棉问题吗？

在今天的系列文章中，我将带您了解最常出现的 5 个 lint 问题及其解释，最后是链接，您可以在这里了解更多关于编码标准的信息。

在前一集，我提到了一个有 lint 问题的代码片段。我想在这里回忆一下同样的片段，以便你更容易跟进。

**1)检测到冗余“返回”**

这是最常见的棉绒问题。人们常常无法解决这个问题。

```
# Example method

def first_omnitrix
  return true
end 
```

Enter fullscreen mode Exit fullscreen mode

在 ruby 的函数末尾，你不需要使用 return 关键字。你可以使用它，但这不是一个好的做法。

代码片段应该是这样的，

```
# Example method

def first_omnitrix
  true
end 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多，你可以在这里找到我写的另一篇文章。([https://dev.to/chintukarthi/redundant-return-detected-ruby-编程-gac](https://dev.to/chintukarthi/redundant-return-detected---ruby-programming-gac) )

2)使用 snake_case 作为方法名。

定义方法时，使用 snake_case 作为方法名。Snake case 只不过是带有分隔符“_”的所有小写字母。

示例:

```
# Example snake_case method

def snake_case
  true
end 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于用例的使用，你可以在这里找到我写的另一篇文章。([https://dev . to/chintukarthi/aydt-03-you-do-this-programming-and-cases-1 enj](https://dev.to/chintukarthi/aydt-03-are-you-doing-this-programming-and-cases-1enj))

**3)不要在“如果”的条件周围使用括号**

如果你是 c 或 c++背景，并开始学习和使用 ruby，你肯定会遇到这种情况，

```
# assume the value of a and b are already initialized and b is less than a.
def first_omnitrix
  if (a>b)
    a = 5
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

ruby 中不建议在 if 条件周围使用括号。所以去掉它。为了避免这种情况，请修改代码，如下面的代码片段所示。

```
def first_omnitrix
  if a>b
    a = 5
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用 guard 子句，而不是将代码包装在条件表达式中。

嗯🤔听起来很奇怪，对吧？

没什么奇怪的，只是很简单。看看下面的代码片段，

```
# assume the value of a and b are already initialized and b is less than a.
def first_omnitrix
  if a>b
    a = 5
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用了一个“if”条件来设置“a”的值，以检查它是否大于 b。

这可能感觉是正确的，也是有效的。但问题是，这不是编写这段代码的正确方法。

你可能想把它改写成这样，

```
# assume the value of a and b are already initialized, and b is less than a.
def first_omnitrix
  a = 5 if a>b
end 
```

Enter fullscreen mode Exit fullscreen mode

检测到尾随空白。

我等不及要分享这个了😇。这个是我个人最喜欢的，因为我经常做这个😂。

在为一些功能编写代码时，我们使用 enter/return 来导航到下一行。但是我们没有意识到的是，它会在新行周围引入空白，就像这样。

```
def whitespace
  if a>b
    if a < c
      true
    end
  end.. (some extra code) 
end 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的是，你导航到一个新的行，在那里写了一些额外的代码，然后删除了代码。但是您可能会忘记删除两个额外的空格(表示为“.”片段中的点)，这将产生“尾随空白”lint 问题。

解决掉 lint 后，上面的方法会是这样的，

```
def whitespace
  if a>b
    if a < c
      true
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

要避免这个问题，只需删除代码中不必要的多余空格。

这是我想与大家分享的 5 大棉绒问题。如果你想提高你的编码技能，你可以到这里([https://rubocop.readthedocs.io/](https://rubocop.readthedocs.io/))学习更多关于 ruby lint 的东西。

这就是本周的总结。🧙周围的人再见🏻‍♂️.

[![see you around gif](img/31338efcda5d89f2f19f2436af6b802d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDLAMfln--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7aumbl5t4huv7o3y4mof.gif)

封面图片提供:[https://www.codeninja.com.sg/](https://www.codeninja.com.sg/)