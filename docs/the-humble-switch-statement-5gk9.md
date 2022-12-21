# 谦逊的开关声明

> 原文：<https://dev.to/mikesherov/the-humble-switch-statement-5gk9>

switch 语句长期以来一直是流行编程语言中的主要部分。它通常用于通过将一个值与多个案例进行比较来执行分支逻辑，并且至少有一种方式会引入微妙的错误:fallthrough。在本文中，我们将深入 javascript 中这种看似“简单”的语法的内部工作原理，发现它是如何工作的，并找到它的优点的用例。请记住，这都是“玩具代码”，考虑到这些边缘的相对模糊性，可能不适合在您的代码库中使用。

## 语法定义

我们将对语法的每一部分使用精确的术语，所以让我们从一些定义开始。考虑下面的代码，使用下表将语法映射到它的名称:

```
switch ("a") {
  case "b":
    log("c")
    break
  default:
    log("d")
} 
```

| 密码 | 学期 |
| --- | --- |
| 开关{ … } | 开关状态 |
| 一个 | 判别式 |
| 案例 b | 开关盒 |
| ' b ' | 试验 |
| 日志(' c ') | 随之发生的 |
| 系统默认值 | 切换默认值 |
| 日志(' d ') | 随之发生的 |

## 评估案例和“反转开关”

在大多数典型情况下，SwitchStatement 用于将计算出的判别式与多个整数或字符串的文字 SwitchCase 测试进行比较:

```
function speak(animalType) {
  switch (animalType) {
    case "dog":
      return "woof"
    case "cat":
      return "meow"
  }
} 
```

在上面的代码片段中，`"dog"`和`"cat"`都是字符串文字，但事实并非如此。JS 规范说 SwitchCase 测试可以是任何表达式，甚至是函数调用。让我们重写 switch 语句来使用函数调用:

```
const getDogType = () => "dog"
const getCatType = () => "cat"

function speak(animalType) {
  switch (animalType) {
    case getDogType():
      return "woof"
    case getCatType():
      return "meow"
  }
} 
```

看到我们可以在 SwitchCase 测试中运行函数，我们现在可以颠倒 SwitchCase 语句的用途。也就是说，不是将一个变量与一组文字进行比较，我们可以将一个文字，例如`true`，与一组函数调用进行比较。考虑下面的代码:

```
function log(logValue, returnValue) {
  console.log(logValue)
  return returnValue
}

switch (true) {
  case log("case 1", false):
    console.log("body 1")
    break
  case log("case 2", true):
    console.log("body 2")
    break
  case log("case 3", true):
    console.log("body 3")
    break
}

// output:
// case 1
// case 2
// body 2 
```

注意，`case 3`不在输出中，因为开关情况测试按顺序**执行，直到一个严格等于判别式**(又名**【短路】**)，然后执行相应的开关情况后果。不执行任何后续的开关盒测试。这让我们可以“颠倒”开关的用途！也就是说，我们可以将一个文字与一组函数进行比较，直到有一个匹配为止。此类代码的真实用例可能如下所示:

```
function accessControlStatusCode(user, request) {
  switch(true) {
    case request.isForbidden():
      return 403;
    case !user.isLoggedIn():
    case !user.isAuthorized(request):
      return 401;
    default:
      return 200;
  } 
```

注意，因为顺序很重要，所以一个禁止的和未授权的请求将显示禁止的错误代码。还要注意，从第 5 行到第 6 行有一个“失败”,这意味着在两种情况下都返回了`401`……但是也许我们会想，当`case !user.isLoggedIn():`已经为真时，是否调用了`!user.isAuthorized(request):`。也就是我们在想`case !user.isLoggedIn(request):`是不是还在“短路”。让我们来看看:

```
function log(logValue, returnValue) {
  console.log(value)
  return returnValue
}

switch (true) {
  case log("case 1", false):
    console.log("body 1")
    break
  case log("case 2a", true):
  case log("case 2b", true):
    console.log("body 2")
  // no break here
  case log("case 3", true):
    console.log("body 3")
    break
}

// output:
// case 1
// case 2a
// body 2
// body 3 
```

我们的规则仍然有效！SwitchCase 测试只执行到一个严格等于判别式，即使下面直接有另一个 SwitchCase。还要注意，我们在`body 2`之后引入了不中断的“失败”,因此`body 3`被执行！我们可以总结出以下关于 switch 的规则: **switch 将执行每一个 SwitchCase 测试，直到其中一个严格等于判别式，然后将依次执行随后的每一个结果，直到遇到类似`break`、`return`或`continue`的语句。正如你将要看到的，如果有意使用，这条规则会产生非常有趣的模式。**

## 落空

到目前为止，我们看到的几个例子都包含错误。乍一看，fallthrough 就像一个等待发生的巨大错误。使用 fallthrough，没有在控制流语句中结束的结果，如`break;`，将执行下一个`consequent`，这通常是一个错误:

```
function speak(animal) {
  let sound
  switch (animal) {
    case "dog":
      sound = "woof"
    case "cat":
      sound = "meow"
  }

  return sound
} 
```

哎呀，现在`speak('dog') === 'meow'`！这种类型的错误非常普遍，以至于大多数 linters 都包含了一条 switch 语句规则，禁止 fallthrough，除非添加一个注释`// falls through`来故意指出它。如果这么容易出错，为什么 switch 甚至会出错？正如我们在 accessControl 示例中看到的，我们可以肯定地说，顺序 SwitchCase(即背靠背 switch case，没有结果分离它们)是一种有用的失败形式，但是在结果之后有合法的失败用例吗？答案就在我们发现的规则中，我们可以利用它来**在任何时候输入一组数据转换。**例如，考虑一个时标转换函数:

```
function toSeconds(value, term) {
  switch (term) {
    case "years":
      value *= 365
    case "days":
      value *= 24
    case "hours":
      value *= 60
    case "minutes":
      value *= 60
  }

  return value
}

toSeconds(1, "minutes") // 60
toSeconds(1, "hours") // 3600
toSeconds(1, "days") // 86400 
```

这个例子揭示了不起眼的 switch 语句的真正本质。在许多方面，它类似于一个`goto`语句，允许您跳转到一组语句中的特定“标签”。`goto`在现代编程语言中早已失宠，也许这暗示了这种形式的 switch 语句也应该保持低调。它可以用一组 if 语句或循环来重写，也许这样更好，因为现在很少有人会故意出错。

## 结论

深入研究基本的语言特性是提升水平的好方法。如您所见，switch 语句充满了惊喜。像漏通、短路和“反转”这样的特征使得这种说法很有力，但也很危险。了解它们将使你成为一个更好的调试器，但是在使用它们时保持克制将确保你的代码更容易被团队中的每个成员理解和访问。