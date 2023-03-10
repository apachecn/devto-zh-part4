# 熟食柜台挑战

> 原文：<https://dev.to/harlessmark/deli-counter-challenge-24lb>

## 问题

我有三个函数可以用来帮助操作排队等候的人。顾客进来并报出他们的名字，然后得到一个号码进入队伍的后面。当队伍中的第一个顾客被服务时，他们就被从队伍中移除。

1.  `takeANumber`将客户添加到行中。返回客户的姓名和在队列中的位置。
2.  宣布他们为谁服务。返回他们正在服务的客户的姓名，然后将客户从线路中移除。
3.  `currentLine`解读排队的人。返回行中所有客户的编号名称列表。

## 我学到了什么

1.  我不知道在单个实例中`shift()`可以用来返回*和*删除第一个索引。我最初在返回线下面有`shift()`,但是我一直得到一个错误。然后我把它放在函数的底部，但是它永远不会执行，因为当运行时，一旦它到达任何一个返回值，它就会退出函数。

2.  我必须将当前数组分割成一个新数组，以生成一个包含所有客户的有序列表。我最初过度考虑了这个挑战，因为我想创建一个非常长和复杂的连接字符串。

## 提问

1.  `output = output.slice(0, output.length - 2)`删除列表中最后一个人的逗号和空格。我不明白是怎么做到的，因为我以为`output.length - 2`会从数组中移除最后两个索引，而不是最后两个字符。

## 答案

1.  `var output`不是数组。我最初将它声明为一个数组，但是当我遍历 For 循环时，它将数组更改为一个字符串。这意味着`output.length - 2`正在删除最后两个字符，因为`var output`不是一个数组。

## 第一次迭代

```
function takeANumber(katzDeliLine, name){
    katzDeliLine.push(name);
    return  `Welcome, ${name}. You are number ${katzDeliLine.length} in line.`;
}

function nowServing(katzDeliLine) {
  if (katzDeliLine.length > 0) {
    return "Currently serving " + katzDeliLine.shift() + ".";
  } else {
      return "There is nobody waiting to be served!";
  }
}

function currentLine(katzDeliLine) {
    var output = [];
    if (katzDeliLine.length > 0) {
      for(var i = 0; i < katzDeliLine.length; i++) {
        output += (i + 1) + ". " + katzDeliLine[i] + ", ";
      }
      output = output.slice(0, output.length - 2);
      return "The line is currently: " + output;
    } else {
        return "The line is currently empty.";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 最终迭代

为了更容易理解和解释，我重构了上面的代码。唯一的更改发生在`function currentLine(katzDeliLine)`。

1.  将`var output = []`改为`var output = ""`，并将其移到 If 语句内。如上所述，它不是一个数组。

2.  将`output = output.slice(0, output.length - 2)`更改为`output = output.slice(0, -2)`。它做同样的事情，但更短。

```
function takeANumber(katzDeliLine, name){
    katzDeliLine.push(name);
    return  `Welcome, ${name}. You are number ${katzDeliLine.length} in line.`;
}

function nowServing(katzDeliLine) {
  if (katzDeliLine.length > 0) {
    return "Currently serving " + katzDeliLine.shift() + ".";
  } else {
      return "There is nobody waiting to be served!";
  }
}

function currentLine(katzDeliLine) {
  if (katzDeliLine.length > 0) {
    var output = "";
    for(var i = 0; i < katzDeliLine.length; i++) {
        output += (i + 1) + ". " + katzDeliLine[i] + ", ";
      }
    output = output.slice(0, -2);
    return `The line is currently: ${output}`;
  } else {
    return "The line is currently empty.";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 原问题

一家非常重要的熟食店需要有人为他们的柜台设计“取号码”功能。

一天开始的时候，熟食店是空的，用一个空数组表示，像`var katzDeliLine = [];`。然而，您不需要将数组编码为一个变量，因为测试脚本会创建它并将其传递给您将要构建的函数。

1.  建立一个新顾客进入熟食店时会用到的功能。函数`takeANumber`应该接受两个参数:当前的人，以及新的人的名字。该函数应该返回一个欢迎消息，包括新人在队列中的位置，如`"Welcome, Ada. You are number 1 in line."`。不要太程序员化，给他们他们的索引。这些都是正常人。如果他们排在第七位，告诉他们。不要告诉他们自己排在第六位，让他们抱太大希望。

2.  构建函数`nowServing`。该函数应该接受当前的一行人(`katzDeliLine`)并返回该行中的第一个人，然后从该行中删除该人。如果没有人排队，它应该返回“没有人等待服务！”

3.  构建一个函数 currentLine，接受当前行的人，并以字符串形式返回当前行；例如，如果`katzDeliLine`当前是`["Ada", "Grace"]`，`currentLine(katzDeliLine)`将返回`"The line is currently: 1\. Ada, 2\. Grace"`。你不必在函数中使用`katzDeliLine`作为变量或参数名，它只是一个可能传递给它的变量的例子。如果没有人排队，它应该返回`"The line is currently empty."`

## 鸣谢

感谢[马特·伊泽尔](https://blog.immatt.com/)为我解释我的问题。感谢[詹姆斯·蒙图尔](https://twitter.com/JamesMontour)帮助我重构代码，并向我展示负数在`splice()`中是如何工作的。