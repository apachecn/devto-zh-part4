# 检测 JavaScript 中的无效日期

> 原文：<https://dev.to/georgemandis/detecting-invalid-dates-in-javascript-1plc>

当您想要实例化一个 JavaScript date 对象的新实例时，您可以像这样传递日期信息:

```
new Date('June 27, 2019') 
```

事实上，接受各种日期格式是有点宽容的。在编写本文时，Chrome 75 中的所有这些都是创建日期对象的有效方法:

```
new Date('June 27, 2019')
new Date('2019, June 27')
new Date('2019/06/27')
new Date('6/27/19')
new Date('6-27-19')
new Date('6/27/2019')
new Date('6-27-19')
new Date('2019-06-27')
new Date('6/27/19 11:00')
new Date('2019-06-27') 
```

对于生产，我不建议这样做。我建议依赖像 [Moment.js](https://momentjs.com/) 这样的已建立的库，或者[GitHub 资源库](https://github.com/you-dont-need/You-Dont-Need-Momentjs)中建议的许多较小的替代库之一。但是，如果您只是在尝试一些东西，并且需要一个快速而简单的解决方案来创建一个日期对象，那么知道您可以这样做是很方便的。

知道自己是否意外创建了无效日期也很方便。查看控制台中的输出，这看起来应该很简单:

```
new Date('My Birthday')
// Invalid Date 
```

您可能希望能够做这样的事情:

```
const birthday = new Date('My Birthday')

if (birthday === "Invalid Date") { ... } 
```

但你可能错了！虽然通常认为测试严格的相等性并使用三重等号是最佳实践，但它在这里对您没有帮助。发送到控制台的值实际上并不是一个字符串，尽管这看起来很奇怪。

从技术上来说，这是可行的:

```
if (birthday == "Invalid Date") { ... } 
```

但我并不完全信任它，也不确定老版本的浏览器是否支持它。

需要记住的是，日期对象只是一个数字，只要格式正确:

```
const day1 = new Date('June 27, 2019')
const day2 = new Date('My Birthday')

console.log(day1.valueOf())
// 1561618800000 

console.log(day2.valueOf())
// NaN 
```

这意味着我们可以使用非常奇怪的 [isNaN()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/isNaN) 函数来计算我们的日期格式是否正确:

```
if (isNaN(birthday)) { ... } 
```

给你 here you go .

如果您不熟悉所有这些 JavaScript/Date/NaN 欺骗，我将把它复制并粘贴到您的控制台:

```
typeof NaN 
```