# Dart 中的控制流

> 原文：<https://dev.to/codingpizza/control-flow-in-dart-3ep6>

你好。上个月，我一直在写关于 Dart 以及这种语言的工作原理。如果你是新来的，可以看看我另一篇关于 Dart 的帖子。我会把它放在下面。

### [T3】变量 T5】](https://dev.to/codingpizza/variables-in-dart-4j63)

### [T3】功能 T5】](https://dev.to/codingpizza/functions-in-dart-175b)

### [T3】参数 T5】](https://dev.to/codingpizza/parameters-in-dart-3hm0)

## 没有控制的力量是无用的。

在这篇文章中，我们将讨论**控制流**如何在 Dart 中工作。先说它是什么，**控制流**。简而言之，就是代码的执行顺序。

比方说，我们需要在带伞外出和不带伞外出之间做出选择。我们查看我们的天气应用程序，如果它说要下雨，我们就带着它，否则我们可以把它留在家里。

如果我们想用代码来写，这可以。

```
if (isGoingToRainToday()) {
 takeUmbrella() 
} else {
 print ("What a great weather today 🌞")
} 
```

Enter fullscreen mode Exit fullscreen mode

## If 语句

**if 语句**帮助我们的代码做出决定，它有以下语法。

```
if (condition) {
 doThis()
} else {
 doAnotherThing()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们从保留的关键字 if 开始，然后在括号内添加一个条件。如果满足该条件，将执行第一个花括号内的代码。否则，执行 else 语句后的代码。末尾的条件是一个布尔值；让我们看一个例子。

在前面的例子中，我们想检查天气，以了解我们是否需要带雨伞。但是 isGoingToRainToday()是什么呢？Well 是一个返回布尔值的函数。看起来是这样的。

```
Boolean isGoingToRainToday() {
    return false
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个提示，else 语句并不总是需要的。如果你只需要执行 If 语句中的内容，你可以省略它。

如果你需要对两个以上的选项进行比较，你也可以使用一个 **else if** 语句，让我们看看它是如何实现的。

```
var flavor = "Vanilla";
if (flavor == "Vanilla"){
    print("Here's your vanilla ice cream");
} else if( flavor == "Chocolate") {
    print("Here's your chocolate ice cream");
} else {
    print("Since we don't know your favorite flavor, here's a random one");
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们有一个最喜欢的冰淇淋口味，如果语句将检查该口味是否是香草口味，如果不是，将尝试 **else if** 条件。如果满足前面提到的任何条件，将执行 else 语句。

如果我们有太多口味会怎么样？

## 介绍开关案例

switch 语句处理变量而不是条件。如果 switch 有一个变量值的“case ”,那么这个 case 中的代码将被执行。
如果开关不能为其找到一个案例，则执行默认案例中的代码。
这里有个例子。

```
var flavor = "Vanilla"
switch (flavor) {
    case "Vanilla":
        print("Here's your vanilla ice cream");
    case "Chocolate":
        print("Here's your chocolate ice cream");
    case "Orange":
        print("Here's your orange ice cream");
    default:
        print("Since we don't know your favorite flavor, here's a random one");
} 
```

Enter fullscreen mode Exit fullscreen mode

## for 语句

for 语句是一种常见的语句，几乎存在于每种语言中。用于循环访问对象数组。Dart 中的语法如下:

```
for (var i = 0; i < list.length; i++){
 print(list[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个声明可能看起来很混乱，但是让我们分开这个声明，变量 **i** 从值 0 开始，它将增加 1，直到它达到列表的长度减 1。

每当变量增加 1 时，大括号内的代码就会被执行。这将打印列表中“我反对”。

当你知道语句什么时候结束时，它的**是有用的。在我们的例子中，我们知道它是因为 list.length 是有限的。**

### While 语句

另一方面，当您不知道某个条件何时会满足时，While 语句会更有效。例如，它将执行大括号内的代码，直到满足条件。

```
int laps = 0;
while (laps < 5){
    print("Laps $laps");
    laps++;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，while 中的代码被执行，直到圈数少于五圈。

```
Laps 0
Laps 1
Laps 2
Laps 3
Laps 4 
```

Enter fullscreen mode Exit fullscreen mode

## while 的同级就是 do while

介绍 While 语句的兄弟，Do while 语句。此语句执行代码，并在语句对代码求值后执行。这里有一个例子。

```
int countDown = 5;
do {
 print("Time remaining: $countDown");
 countDown--;
} while (countDown != 0); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将打印倒计时变量，虽然它不是零。这是结果。

```
Time remaining: 5

Time remaining: 4

Time remaining: 3

Time remaining: 2

Time remaining: 1 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
就是这样

如果你是编程新手，我希望这能帮助你，如果你不是，我希望你喜欢它。我还在 Instagram 上创建了关于 Flutter 的新代码，请在@codingpizza 中关注我以获取更多内容。

我也在写一本电子书，是 Dart 的基础课程。开始使用 Flutter 所需要的一切。免费的，你可以在这里注册。T3】

## 现在轮到你了

你可以在 IDE 中尝试这些概念，比如免费的 Intellij idea 社区，你只需要安装 Dart 插件。Visual Studio 代码或者在一些在线编辑器中像

 **## 以前的帖子

如果你对更多这样的帖子感兴趣，你可以看看我其他关于 Dart 的帖子。

### [T3】变量 T5】](https://dev.to/codingpizza/variables-in-dart-4j63)

### [T3】功能 T5】](https://dev.to/codingpizza/functions-in-dart-175b)

### [T3】参数 T5】](https://dev.to/codingpizza/parameters-in-dart-3hm0)**