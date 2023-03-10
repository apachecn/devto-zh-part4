# 快速控制流程

> 原文：<https://dev.to/codingpizza/control-flow-en-dart-2651>

# 控制流- ES

你好！，上个月我一直在写关于 Dart 以及事情是如何用这种语言工作的。如果你是新来的你可以看看我关于 Dart 的其他文章。我把链接留在下面。

*   **[变量](https://dev.to/codingpizza/variables-en-dart-3963)**
*   **[功能](https://dev.to/codingpizza/funciones-en-dart-19on)**
*   [参数](https://dev.to/codingpizza/parametros-en-dart-1f11)

## 不受控制的权力是无用的

在本文中，我们将讨论 Dart 中的“T0”控制流(control flow)是如何工作的。让我们从控制流开始。简而言之，它是代码执行的顺序。

比方说，我们得选择是带着雨伞还是不带伞上街。我们查了天气预报如果他说要下雨我们就带把雨伞不然我们就把他留在家里。

如果我们想把它写在代码里，它会是这样的:

```
if (isGoingToRainToday()) {
 takeUmbrella() 
} else {
 print ("What a great weather today 🌞")
} 
```

Enter fullscreen mode Exit fullscreen mode

## If 语句

**If** 有助于我们的代码作出决定，其语法如下。

```
if (condition) {
 doThis()
} else {
 doAnotherThing()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们先从保留字 **if、**开始，然后在括号内添加条件。如果第一个键内的代码满足此条件，则执行该条件。否则，将执行 else 关键字中的代码。最后的条件是布林变数。

一个要考虑的事实是 **else** 并非总是必要的，如果你只需要执行 if 内的东西就可以避免。

如果您需要与两个以上的选项进行比较，可以使用一个 **else** **if。**让我们看看它是如何工作的。

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

在这个例子中，我们有一种最喜欢的冰淇淋口味，那就是香草。 **If** 将检查味道是否香草，如果香草味，将在 **if 内执行代码。**如果味道等于巧克力，则执行其他 **if** 中的内容，如果不符合上述两个条件中的任何一个，则执行其他中的代码。

当我们有很多口味时会发生什么？

## present mos El 开关盒

Case 开关使用变量而不是条件。交换机逐例比较变量与 case 值，如果 case 中的代码相同，则会执行该变量。

这里我们可以看到一个例子。

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

## 为

循环 **For** 是几乎所有语言都能找到的常用指令。用于执行代码的次数有限。它在 dart 中的语法如下:

```
for (var i = 0; i < list.length; i++){
 print(list[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

循环 **For** 可能看起来很混乱，但我们会把它分成几部分。变量 **i** 从值 0 开始，变量以 1 为增量递增，直到小于列表大小。

对于循环执行的每一个循环，**for**内的代码都会执行。就我们而言，它将打印列表中的项目**【I】**。

### 趁着

例如，循环 **While** 只要满足一个条件就会执行。

```
int laps = 0;
while (laps < 5){
    print("Laps $laps");
    laps++;
} 
```

Enter fullscreen mode Exit fullscreen mode

在此范例中，while 内的程式码会执行，直到变数**laps**(round)小于 5 为止。

```
Laps 0
Laps 1
Laps 2
Laps 3
Laps 4 
```

Enter fullscreen mode Exit fullscreen mode

## 埃尔曼诺·德尔虽然

While 回圈的兄弟是 Do While。此回圈与其兄弟相反，会执行程式码，然后评估条件。这里有一个例子。

```
int countDown = 5;
do {
 print("Time remaining: $countDown");
 countDown--;
} while (countDown != 0); 
```

Enter fullscreen mode Exit fullscreen mode

此示例打印变量**count**只要不为零。

```
Time remaining: 5

Time remaining: 4

Time remaining: 3

Time remaining: 2

Time remaining: 1 
```

Enter fullscreen mode Exit fullscreen mode

## 
 [T3
Eso es todo](#eso-es-todo) 

如果你是新来的编程人员，我希望这能帮助你，如果你不是，我希望你喜欢它。我还在 Instagram 上创建了关于浮动的新密码学，你可以跟我来，比如 [@codingpizza](https://www.instagram.com/codingpizza/) 和推特上的 [@coding__pizza](https://twitter.com/coding__pizza) 查看更多内容。

如果你不知道的话，我正在写一篇关于学浮力所需的 Dart 基础课程的电子书。[是免费的，你可以在这个链接上注册。](https://www.codingpizza.com/curso-dart-gratis/)

## 现在轮到你了

您可以在开发环境(IDE)中测试这些概念，例如[【intellij idea communit】](https://www.jetbrains.com/idea/download/)，而且它是免费的并安装 Dart 插件，如果您喜欢 Visual Studio 代码，也可以在其中测试这些概念，最后，如果您喜欢在线上的东西，可以使用[* * DARPA](https://dartpad.dartlang.org/)d。**

## 后置前置

如果你对更多这样的帖子感兴趣，你可以查看我关于 Dart 的其他文章。

*   **[变量](https://dev.to/codingpizza/variables-en-dart-3963)**
*   **[功能](https://dev.to/codingpizza/funciones-en-dart-19on)**
*   [参数](https://dev.to/codingpizza/parametros-en-dart-1f11)

## 知道更多 Dart 剩馀？

如果你喜欢这篇帖子并且对学习 Dart 感兴趣，我现在正在电子书上写更多这样的文章，这是 Dart 的基础课程，它将帮助你掌握好知识，然后你可以用它开始浮起；页:1。你可以出院*** *在这个链接 **因为电子书将是**完全免费的。****