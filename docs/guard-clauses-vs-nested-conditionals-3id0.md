# 保护子句与嵌套条件句

> 原文：<https://dev.to/anthonyharvey/guard-clauses-vs-nested-conditionals-3id0>

**TLDR**；guard 子句是一个提前返回(提前退出)的语句，它“防止”代码的其余部分在不必要的情况下执行(基于您指定的标准)。

* * *

在我作为 Ruby on Rails 开发人员开始职业生涯后不久，我就了解了保护子句以及它们如何提高代码可读性。

让我们看一个带有标准`if/else`语句的示例方法。

```
def example_method(parameter)
  if parameter == true
    # awesome code here
  else
    return nil
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个方法中，我们做了一些常见的事情:评估一个参数并:

*   如果我们的期望得到满足，执行一些代码**或**
*   如果我们的期望没有实现，就执行其他代码。

在这种情况下:

*   如果参数是`true`,我们执行一些“令人敬畏的代码”
*   如果没有，我们就返回`nil`

通过使用一个 guard 子句，这个方法可以缩短到只有两行代码。

```
def example_method(parameter)
  return nil unless parameter == true #<-- guard clause
  # awesome code here
end 
```

Enter fullscreen mode Exit fullscreen mode

[除非方法](https://dev.to/anthonyharvey/ruby-if-unless-while-and-until-4f9a)

这是一个简短的例子，用来说明保护条款的概念。现在让我们看一个例子，在这个例子中，guard 子句可以帮助消除代码味道:嵌套条件。

```
 def print_shipping_label(order)
  if order.outstanding_payments?
    nil
  else
    if order.address_incomplete?
      order.send_address_reminder
    else
      if order.standard_shipping?
        order.print_standard_shipping_label
      else
        order.print_priority_shipping_lable
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这段代码很难阅读和理解发生了什么(只要看看嵌套的`if/else`语句中有多少级缩进就知道了！)

## 使用 Guard 子句重构嵌套条件

我们可以使用 guard 子句来重构嵌套的`if/else`语句，并使该方法更易读、更易理解。

```
def print_shipping_label2(order)
  return nil if order.outstanding_payments?
  return order.send_address_reminder if order.address_incomplete?
  return order.print_standard_shipping_label if order.standard_shipping?
  order.print_priority_shipping_label
end 
```

Enter fullscreen mode Exit fullscreen mode

出于这些原因，我喜欢安全条款，但是你应该在对你和你的团队有意义的地方使用它们。

例如，一些语言需要显式的资源管理，拥有一个方法的单一出口点对于防止内存泄漏是有意义的。然而 Ruby 有[垃圾收集](https://blog.heroku.com/incremental-gc)来自动收集未使用的对象。

* * *

## 外卖:

Guard 子句**保护**(保护)代码的其余部分在不必要的情况下不被执行(基于某些标准)，并且被放置在方法的顶部。

## 资源:

1.  [Refactoring.com——用保护子句替换嵌套的条件语句](https://www.refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html)
2.  [Refactoring.guru -用保护子句替换嵌套的条件语句](https://refactoring.guru/replace-nested-conditional-with-guard-clauses)

*本文原载于 2019 年 7 月 28 日[anthonygharvey.com](https://anthonygharvey.com/ruby/guard_clauses_vs_nested_if_statements)。*