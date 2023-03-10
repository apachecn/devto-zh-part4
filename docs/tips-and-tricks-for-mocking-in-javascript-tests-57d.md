# JavaScript 测试中模仿的技巧和窍门

> 原文：<https://dev.to/daniel_werner/tips-and-tricks-for-mocking-in-javascript-tests-57d>

在我之前的一篇文章中，我写了关于 JavaScript 的单元测试(你可以在这里找到:[https://42coders.com/unit-testing-javascript-with-jasmine/](https://dev.to/dv42coders/unit-testing-javascript-with-jasmine-3676-temp-slug-4411122))。当进行 tdd(测试驱动开发)或 bdd(行为驱动开发)时，有必要模拟被测试方法的输入。如果代码足够干净，那么做起来应该很简单，但是在某些情况下事情会变得复杂。在这篇文章中，我将展示一些应对这些情况的技巧。

## Datetime

当一个函数使用内置的 datetime 对象时，我们需要找到一种方法来模仿内置的 time 对象。还可以通过重构函数来解决这个问题，将 datetime 对象作为一个参数，这将使嘲笑变得微不足道，但这是另一篇文章的主题。让我们看看如何解决上述问题。我用过 sinon . js[https://sinonjs.org/](https://sinonjs.org/)这是一个很酷的库，能够伪造定时器、xhr 请求等等。

使用 npm 安装 sinon】

```
npm install sinon 
```

使用前需要:

```
let sinon = require('sinon'); 
```

创建一个日期对象来使用一个假计时器:

```
let fakeDate = new Date(2019, 5, 11, 12, 0, 0); 
```

使用假计时器:

```
let clock = sinon.useFakeTimers(fakeDate); 
```

当所需的测试运行后，使用
恢复计时器

```
clock.restore(); 
```

## 地理定位

支持地理定位的应用程序不容易测试，尤其是因为高质量的 GPS 信号只能在室外捕捉到。从健身和健康的角度来看，写一些代码，到外面去测试是不错的，但是生产力不会很好🙂

你可以用 geomock 库来模拟 HTML5 地理定位:[https://github.com/daniel-werner/GeoMock](https://github.com/daniel-werner/GeoMock)。我已经从 https://github.com/janmonschke/GeoMock 的[分支了这个库，并且只增加了一个特性，只发送一次预定义的坐标，不重复它们。](https://github.com/janmonschke/GeoMock)

首先需要 geomock

```
require('geomock/geomock'); 
```

设置发送坐标的时间间隔(以毫秒计)

```
navigator.geolocation.delay = 1000; 
```

设置它是应该重复坐标还是只“播放”一次。

```
navigator.geolocation.repeat = true; 
```

添加预定义坐标

```
var startTime = 1551018055000;navigator.geolocation.waypoints = [   {coords : {latitude : 45.8849114, longitude : 19.2545559, accuracy: 65 }, timestamp: startTime},    {coords : {latitude : 45.8856601, longitude : 19.2553514, accuracy: 65 }, timestamp: startTime + 30000},    {coords : {latitude : 45.8849114, longitude : 19.2545559, accuracy: 65 }, timestamp: startTime + 55000},    {coords : {latitude : 45.8856601, longitude : 19.2553514, accuracy: 65 }, timestamp: startTime + 75000},    {coords : {latitude : 45.8849114, longitude : 19.2545559, accuracy: 65 }, timestamp: startTime + 90000},]; 
```

当被测试的代码使用地理定位特性时，它将得到假的坐标，这样你就可以很容易地测试它了。使用这个库有利于创建应用程序的集成测试或特性测试。

## 为现有代码编写测试

在某些情况下，您可能会为现有的工作代码编写测试。这不被认为是测试驱动的开发，但是它比根本没有测试要好得多，并且测试可以确保您在修改代码时不会破坏现有的功能。但是编写测试很无聊，编写模拟更无聊，我们开发人员很懒:)，所以我们会使用技巧来避免做无聊的事情。在这种情况下，我最喜欢的技巧是手动测试代码，并在测试过程中复制函数的参数。使用 Chrome 开发者控制台很容易做到，方法如下:

将 **console.log(argument1)** 添加到函数中。

在 DevTools 中打开控制台，右键单击记录的数据，选择**存储为全局变量**

它会将其存储为临时变量，例如 temp1。您可以使用控制台中的 **copy(temp1)** 命令将该值复制到剪贴板，并粘贴到您的测试中。当参数是具有许多字段的对象或具有多个元素的数组时，这尤其有用。

## 结论

在我们的代码中使用内置的浏览器/语言特性不应该阻止我们为应用程序编写单元/特性测试，以确保我们产生干净的代码和高质量的软件！

关于 JavaScript 测试中嘲讽的技巧和诀窍的帖子最早出现在 [42 编码员](https://42coders.com)上。