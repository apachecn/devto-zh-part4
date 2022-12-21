# 创建更强大系统的简单方法

> 原文：<https://dev.to/edwardgunawan/the-simple-approach-to-create-a-more-robust-system-326f>

[![Photo by Adli Wahid](img/fba663f29c3774cb4ff93b1cbc581b0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oiHWDAnD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1542828810-3372a0020f50%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1350%26q%3D80)

系统不应失败，应用程序不应崩溃这一原则已经在软件开发和设计分布式系统中得到广泛应用。各个公司都实施了某种方法来防止他们的应用程序崩溃。例如，亚马逊通过使用“[高可用系统](https://www.inc.com/kevin-j-ryan/the-crazy-ways-amazon-will-prevent-your-site-from-crashing.html)”来保护其云基础设施免于崩溃，高可用系统是指没有单点故障的系统——通过创建冗余，如果一个组件出现故障，系统可以继续运行。脸书在刚开始使用 MYSQL 数据库时，将其分区到每个区域，以防止应用程序崩溃。然而，就像所有人都会犯错一样，所有系统都会遭遇失败。有各种方法来限制应用程序不崩溃，如延迟失败，找到一个解决方法的错误，使系统可以继续运行，只是失败了。这些方法不是处理系统故障的最佳方式。

为什么？因为做那三件事并没有解决问题。bug 依然存在。隐藏问题只会导致问题在将来变得更大，从而花费更多的时间和资源来解决它。

**那么，如果申请失败了，我们该怎么办？**

答案是快速失败，然后早点回来。我们需要改变我们对待软件开发的方式。如果有任何问题，我们应该给出异常并返回它，而不是在方法中避免任何类型的异常并优雅地返回。

> “对我来说，软件开发最烦人的部分就是调试。我讨厌的错误是那些在不寻常的情况下，每小时成功操作后出现的错误。”
> 
> <cite title="Jim Shore">Jim Shore</cite>

## 快速失效，而不是自动防故障

当软件快速失败时，它不会减少系统中的 bug 数量；但是会更容易发现错误并解决问题。快速失败会鼓励我们立即、明显地让系统失败。看起来它会通过立即暴露错误而使我们的系统变得脆弱。但是，它创建了一个更健壮的系统，可以在投入生产之前解决问题。当错误更容易被发现和复制时，就更容易修复。例如，让我们创建一个自动防故障的方法来计算租金:

```
public int maxRent() {
  RentPortfolio rent = Portfolio.get("rent-portfolio");
  if(rent == null) {
    return 1000;
  } 
  return rent.getMaxPrice();
} 
```

Enter fullscreen mode Exit fullscreen mode

该方法通过从`Portfolio`对象获取`rent-portfolio`属性来计算最大租金。当您在这个方法中输入任何值时，它都会优雅地返回。

现在，想象一下在`Portfolio`类中有一个小的升级，一个正在升级代码库的开发人员在`rent-portfolio`到`rent-porttfolio`上创建了一个错别字。软件仍然可以成功交付，应用程序仍然可以工作。然而，用户每次都会得到错误的值，因为它将返回一个默认值。当客户意识到它不准确时，将很难调试这个问题。

相反，如果你写一个快速失败的方法:

```
public int maxRent() {
  RentPortfolio rent = Portfolio.get("rent-portfolio");
  if(rent == null) {
    throw new PortfolioNotFoundException("rent-portfolio is not found in " + this.portfolioPath);
  }
  return rent.getMaxPrice();
} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，开发人员将在发布软件之前检测到该 bug，因为它会抛出一个`PortfolioNotFoundException`。

故障快速和故障安全方法的另一个例子:

 *防故障*

```
public division(int numerator, int denominator) {
  /* some code here ........ */
  return numerator / denominator;
}

public static void main(String...args) {
  division(2,0);
} 
```

Enter fullscreen mode Exit fullscreen mode

异常:Java . lang . arithmetic Exception:/by zero...

```
public division(int numerator, int denominator) {
  if(denominator == 0) throw new IllegalArgumentException("denominator cannot be 0");
  /* some code here ..... */
  return numerator / denominator;
}

public static void main(String...args) {
  division(2,0);
} 
```

Enter fullscreen mode Exit fullscreen mode

异常:Java . lang . illegalargumentexception:分母不能为 0....

在这个例子中，fail-fast 确实抛出了一个错误。但是，应用程序没有抛出正确的消息。bug 的根本原因是因为是`InvalidArgumentException`而不是`ArithmeticException`。因此，开发人员需要多次搜索代码才能知道问题出在哪里。Fail-fast 将帮助维护程序的开发人员立即发现错误。

### 温馨提示——一对情侣快速检查失败:

*   空
*   空字符串
*   空列表
*   大多数先决条件检查

### 识别可见故障的方式:

*   使用测试驱动开发。甚至在实现方法之前，您就可以通过使用断言来陈述成功的用例以及失败的用例。
*   在您的软件开发过程中有一个持续的集成构建将确保在将功能分支合并到主分支时任何必要的失败。
*   为健全性检查创建断言——断言和异常之间的区别可以在这里找到(链接)。
*   当系统设置出现任何问题时，不使用后备功能，而是让系统出现故障，以便您可以得到通知并立即解决问题。
*   当前端向服务器请求对集合的 GET 调用时，不是在没有找到集合时返回空列表，而是返回 404 not found 异常。

## 提前返回

早归与快败相伴。当您发现一个 bug 时，您可以将它抛出到函数上方的系统堆栈中，或者立即返回该错误。例如，当你想在你的函数参数中创建一个前置条件检查时，你可以这样做:

```
public String getPersonalData(Person person) {
  if(!systemIsUp) {
   if(person.getName() == "") {
    if(person.age == 0) {
      if(person.pin == "") {
        return "Person doesn't have a pin"; 
      }
      return "Person age cannot be 0";
    }
    return "Person Doesn't have a name"
   }
   return "System is Down"
  }

  return person.getName();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在读这段代码，当你读到第三个语句时，你可能会忘记前两个参数的语句是什么。这种检查的主要问题是，当出现故障时，不仅很难调试，而且很难阅读。这会导致很高的圈复杂度。圈复杂度是一个度量你的软件系统有多复杂的指标。你如何衡量复杂性？每当有`if`、`else`、`try`、`catch`、`for`、`and`、`or`、`others`、`while`的时候，你都在增加一点复杂性。圈复杂度越高，意味着系统越复杂，越难维护。此外，记住所有的检查和嵌套的 if/else 语句很难。相反，应该尽早返回预处理的 if/else 语句，首先从简单的条件开始。

```
public String getPersonal(Person person) {
  if(!systemIsUp) return "System is Down";

  if(person.getName() == "") return "Person Doesn't have a name";

  if(person.age == 0) return "Person age cannot be 0";

  if(person.pin == 0) return "Person' doesn't have a pin";

  return person.getName();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在阅读这段代码，你就会明白先决条件应该是什么。调试应用程序时，您不需要准备一张纸并编写所有的 if/else 语句。此外，函数中编写的代码更少。

## 外卖

> “失败，一次又一次的失败，是通往成功之路的路标。一个人朝着成功前进。”刘易斯。

为了使系统更加健壮，开发人员应该抛出任何出现的错误，以便很容易立即捕捉并修复它们。处理先决条件和健全性检查的一个好方法是抛出一个异常或断言。一旦条件不满足，立即退货。通过快速失败和早期返回，我们可以降低调试成本并提高系统质量。

### 引用:

*   马丁-福勒快速失效法
*   [软件开发中的快速失败原则——DZone 敏捷](https://dzone.com/articles/fail-fast-principle-in-software-development)
*   [干代码与湿代码| Codementor](https://www.codementor.io/joshuaaroke/dry-code-vs-wet-code-89xjwv11w)**