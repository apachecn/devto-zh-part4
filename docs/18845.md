# 用更好的代码替换注释

> 原文：<https://dev.to/ilvalerione/replace-comments-with-better-code-3950>

代码中的注释很重要，我坚信注释是某些函数或整个类的文档。

当你是团队的一员时，代码中的注释经常会引起讨论和分歧。让我们就“代码中的注释”这一概念达成一致。

```
class Math
{
   public function calc() 
   {
     // Add b to a 
     $c = $this->a + $this->b;
     // return the result of a + b 
     return $c;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的内容可能是会议的结果，在会议中，团队被要求仔细地进行评论。

重复代码是你所能做的最糟糕的事情，当阅读代码本身会更清楚时，添加描述代码正在做什么的注释意味着你浪费时间，其他开发人员也会花时间去研究，除非是文档。

初级开发人员在应该依靠代码编写故事的时候，却依靠评论来讲述故事。经验不足的开发人员倾向于使用注释来描述代码块背后的故事。

我们甚至可以更富于表现力地关注类名、函数名和变量名，而不用写一行注释。

如果你觉得有必要写注释来解释你的函数是做什么的，你需要做的第一件事就是考虑重组你写的代码的可能性，让它单独解释它的目的。举这个例子:

```
/**
 * Find low rent at 15% gross monthly income
 */
public function rent($income)
{
  return round(($income*0.15) / 12);
} 
```

Enter fullscreen mode Exit fullscreen mode

只能接受一个注释行。或者，我们是否可以审查代码，使其更加清晰、模块化，并避免任何评论？

```
public function calculateLowRent($monthlyIncome): int
{
  return $this->calculateMonthlyRent($monthlyIncome, 0.15);
}

public function calculateMonthlyRent($monthlyIncome, $percentage)
{
  return round(($monthlyIncome*$percentage)/12);
} 
```

Enter fullscreen mode Exit fullscreen mode

代码更加冗长，不需要注释。

数字现在有了一个标签，函数也有了一个名字，清楚地解释了它们的作用。如果单独来看，这个例子似乎有点过分。你需要关注的是策略，即使用代码本身解释如何以及为什么在那里找到代码的价值。

我的建议是不要低估这方面。如果在代码中加入了太多的注释，那么您和其他开发人员可能会忽略它们的存在，这种风险会成倍增加，并且会在文档中传播旧的和错误的信息。

很明显，经常需要注释来解释更复杂的场景或链接到 bug，而仅仅使用代码中的名字是不可能做到的。

在现代 IDE 中，注释通常对改进代码导航很有用。无论如何，下一次你觉得有必要写注释的时候，你可以问问自己，是否有可能使用代码本身来提高代码库的可维护性，从而获得同样的可读性。

* * *

我是 Inspector 的创始人，这是一款面向网络代理和自由职业者的实时监控工具。

如果您有一个 PHP 应用程序来支持您业务，检查员可以在您的用户意识到问题之前发现它们，从而保护您的业务安全。

*试用 Inspector，免费:*[https://www . Inspector . dev](https://www.inspector.dev)