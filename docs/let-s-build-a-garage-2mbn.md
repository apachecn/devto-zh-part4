# 让我们建一个车库吧！

> 原文：<https://dev.to/aminnairi/let-s-build-a-garage-2mbn>

在这个非技术性的标题背后，隐藏着一个让你爱上循环的 JavaScript 小技巧。

假设你有一个车库。很明显，你想用 JavaScript 管理你的车库，因为 JavaScript 是管理车库中车辆的最佳语言。不服气？嗯，名人就是。

> JavaScript 不仅是世界上最好的语言，它还让我的摩托车车库的管理能力提高了+99999%。—山谬·里维，2019 年。

好了，现在你确信了，让我们从一点点代码开始。

## 初始设置

我们将编写一个简单而强大的车库类来存放我们所有的车辆。

```
"use strict";

class Garage {
  constructor() {
    this.vehicles = [];
  }

  add(vehicle) {
    this.vehicles.push(vehicle);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将需要实例化一个新的车库来存放我们的车辆。

```
const garage = new Garage(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以把车存放在车库里。

```
garage.add("Triumph Street Triple");
garage.add("Mazda 2");
garage.add("Nissan X-Trail"); 
```

Enter fullscreen mode Exit fullscreen mode

循环遍历它们来列出我们所有的车辆怎么样？

```
for (const vehicle of garage.vehicles) {
  console.log(vehicle);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经可以通过使用 Node.js.
看到我们脚本的结果

```
$  node main.js
Triumph Street Triple
Mazda 2
Nissan X-Trail 
```

Enter fullscreen mode Exit fullscreen mode

太好了！或者是？

## 走向复杂和超越！

大多数时候，我们的类会比这个简单的例子更复杂。假设我们的车库现在明确区分摩托车和汽车。同时仍然将它们保持在一起。得听车库管理员的命令，对吧？

```
 constructor() {
-   this.vehicles = []; +   this.cars = [];
+   this.motorcycles = [];
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可能还需要稍微改变一下我们的 add 方法来反映这种区别。

```
- add(vehicle) {
-   this.vehicles.push(vehicle);
- } + addMotorcycle(motorcycle) {
+   this.motorcycles.push(motorcycle);
+ }
+
+ addCar(car) {
+   this.cars.push(car);
+ } 
```

Enter fullscreen mode Exit fullscreen mode

以及我们将车辆加入车库的方式。

```
- garage.add("Triumph Street Triple"); + garage.addMotorcycle("Triumph Street Triple"); - garage.add("Mazda 2"); + garage.addCar("Mazda 2"); - garage.add("Nissan X-Trail"); + garage.addCar("Nissan X-Trail"); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以运行我们的脚本了。这应该如预期的那样工作，对吗？

```
$  node main.js
for (const vehicle of garage.vehicles) {
                             ^

TypeError: garage.vehicles is not iterable 
```

Enter fullscreen mode Exit fullscreen mode

## 怎么了？

你看，我们现在已经删除了`garage.vehicles`属性，取而代之的是两个保存车辆的属性。我们可以做两个循环，然后遍历这两个属性。我们甚至可以将两个数组合并成一个数组，并对其进行循环。为什么不呢，我们开始吧！

```
- for (const vehicle of garage.vehicles) { + for (const vehicle of [...garage.motorcycles, ...garage.cars]) {
    console.log(vehicle);
  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们来测试一下:

```
$  node main.js
Triumph Street Triple
Mazda 2
Nissan X-Trail 
```

Enter fullscreen mode Exit fullscreen mode

耶！按预期工作。但是这使得我们的语法比以前可读性更差，更不自然。现在想象一下，我们的车库越来越受欢迎，全国各地的人都想修理他们的自行车、公共汽车、卡车，...你会继续这样做吗？当然可以！我是说不！

## 你有时间谈谈我们的主和救世主，迭代器协议吗？

JavaScript 语言下隐藏着这个奇怪的迭代器世界。俗话说，一旦你去了那里，你就再也不会回来了。你是伟大事业的一部分。你现在是 JavaScript 语言的一部分了。你感觉完整，但也感觉与 JavaScript 引擎发出的内部系统调用相连。但是在感受到这种力量之前，我们需要稍微重构一下我们的代码。

```
 class Garage {
+   *[Symbol.iterator]() {
+     for (const vehicle of [...garage.motorcycles, ...garage.cars]) {
+       yield vehicle;
+     }
+   }
  } 
```

Enter fullscreen mode Exit fullscreen mode

好吧！但我们之前不就是这么做的吗？用一点新语法？是的，当然，但是我们现在能够使用一种可读性更好的语法。可能不太自然，因为我们不经常迭代对象，但是现在它允许我们用`for...of`循环和三点语法迭代对象。

星星告诉引擎，我们的功能现在是发电机功能。一种特殊的函数，可以帮助我们返回符合迭代器协议的东西。该符号将允许我们的实例通过 for 循环(和三点语法)进行迭代，并在所有将 iterable 作为参数的方法中使用我们的实例。例如，我们现在可以做类似于:
的事情

```
Array.from(garage).map(vehicle => console.log(vehicle)); 
```

Enter fullscreen mode Exit fullscreen mode

那会工作得很好！

## 用法

现在一切都设置好了，我们可以回到 for 循环的第一个初始定义。

```
- for (const vehicle of [...garage.motorcycles, ...garage.cars]) { + for (const vehicle of garage) {
    console.log(vehicle);
  } 
```

Enter fullscreen mode Exit fullscreen mode

行得通吗？(剧透:会的)

```
$  node main.js
Triumph Street Triple
Mazda 2
Nissan X-Trail 
```

Enter fullscreen mode Exit fullscreen mode

## 但是等等，还有更多！

现在我们使用了这个新的协议和迭代器符号，我们可以做一些很酷的事情，比如不用 for 循环就可以遍历它:

```
+ [...garage].map(vehicle => console.log(vehicle)); - for (const vehicle of garage) {
-   console.log(vehicle);
- } 
```

Enter fullscreen mode Exit fullscreen mode

这可以很好地过滤出车辆的名称，例如:

```
- [...garage].map(vehicle => console.log(vehicle)); + [...garage]
+   .filter(vehicle => vehicle.toLowerCase().includes("triumph"))
+   .map(vehicle => console.log(vehicle)); 
```

Enter fullscreen mode Exit fullscreen mode

运行这个将只给我们凯旋摩托车(虽然，我们只有一个，这些自行车是相当昂贵的，你知道！).

```
$  node main.js
Triumph Street Triple 
```

Enter fullscreen mode Exit fullscreen mode

## 结束

这就是现在所有的人们！如果你对这个主题感兴趣，你可以查看一下关于符号迭代器和 T2 迭代器协议的文档。

你可以在这里玩这个例子。

您会使用该功能吗？你认为这对你的应用有帮助还是增加了复杂性？请在评论区告诉我！