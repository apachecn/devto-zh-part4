# JavaScript 日期

> 原文：<https://dev.to/mcapodici/javascript-dates-2dbj>

如果您进行任何数量的编程，您可能会遇到日期和时间这个棘手的问题。这是一个棘手的问题，因为世界上所有的时区，变化的时钟，闰年和其他奇怪的事情。也就是说，在 Javascript 中开始使用日期是很简单的，本教程将解释在 JavaScript 中使用日期可以做的大部分事情，如果需要更多内容，还可以使用一个库。

## 什么是 JavaScript 日期？

JavaScript 中的日期是表示日期和时间的对象。JS 日期可以回溯或向前约 280，000 年，精确到最近的毫秒(千分之一秒)。在 Date 对象中，只有一个值，即 UTC 时间中 1970 年 1 月 1 日之前或之后的毫秒数。UTC 时间是一种标准时间，用于帮助协调世界各地的时间。它类似于格林威治标准时间(GMT)时区。

在处理 JavaScript 日期时，您实际上只是在处理一个数字，比如 819131040000。然而，像这样处理数字是不方便的，所以 JavaScript 中有助手函数来创建日期、查询和操作它们。我们很快就会看到这些，但首先让我们确定一个日期。

### 创建一个 JavaScript 日期

#### 从今天的日期和当前时间创建一个 JS 日期

下面是一个创建日期的例子，它将日期设置为今天的日期和时间:

```
var today = new Date(); 
```

现在，您可以通过使用 date 上的方法之一来查看该日期中的内容，例如`toDateString` :

```
console.log(today.toDateString()); 
// Returns a string like: "Fri Mar 01 2019"

console.log(today.toTimeString()); 
// Returns a string like: "05:00:00 GMT+1100 (Australian Eastern Daylight Time)" 
```

Javascript 中没有指定`toDateString`和`toTimeString`方法中使用的格式。这意味着不同的浏览器可能会返回不同的格式，这也可能取决于您电脑的位置设置。

#### Aghhh！-时区

从包含“GMT+1100”和“澳大利亚东部夏令时”的第二个结果来看，时区是这里的一个因素，所以让我们快速讨论一下。

前一个示例中的`today`变量被设置为今天的日期和时间，并以 UTC 时间存储。代码在澳大利亚运行，当时的 UTC 日期和时间是 28th 2019 年 2 月 28 日下午 6 点。`today`变量知道现在是 28th 2019 年 2 月 28 日下午 6 点’。

当调用`toDateString`和`toTimeString`时，它们使用浏览器的当前时区，并将内部日期和时间转换为*的那个*时区。在这种情况下，它碰巧又在澳大利亚，因此结果是根据澳大利亚产生的，即“2019 年 3 月 1 日凌晨 5 点，AEDT”。

我们将在教程中多次看到这个 UTC 转换过程，所以如果现在看起来有点混乱，也不用担心。

如果您想创建一个具有特定日期和时间的日期，该怎么办？

有三种方法可以做到这一点:

#### 按零件创建 JS 日期

如果您需要特定的日期和时间，而不是当前时间，建议创建日期的方法是指定年、月、日、小时、分钟和秒。这可以按如下方式进行:

```
var date = new Date(2019, 02, 03, 00, 00, 00); // 03 March 2019 in local time zone. 
```

论据如下:

*   年
*   月份(从零开始，所以 0 =一月，1 =二月，以此类推。)
*   天
*   小时
*   分钟
*   秒

日期和时间在 date 对象中从本地时间转换为基于 UTC 的数字。这意味着您可以在 Date 上调用方法来查找它的 UTC 值。你也可以用其他方法来查看当地的时间。

要了解这一点，请看这个例子，当在澳大利亚运行时，使用`getDate`和`getUTCDate`从我们刚刚创建的日期
中提取一个月中的某一天

```
new Date(2019, 2, 3, 00, 00, 00).getUTCDate(); // 2
new Date(2019, 2, 3, 00, 00, 00).getDate(); // 3 
```

每当您向另一个系统(如 web 服务器)发送日期时，通常明智的做法是以 UTC 格式发送，因为它们最终到达的地方可能在不同的时区，使用 UTC 可以更容易地进行协调，因为您只需要一次转换就可以到达 UTC 或从 UTC 获得。

#### 通过字符串创建 JS 日期

不建议这样做，因为它不是标准化的，而且不同的浏览器(或其他 JavaScript 环境，如 Node、嵌入式设备等。)可能会给出不同的结果。

```
var date = new Date('2019-03-03T00:00:00'); 
```

这也会将您给出的时间视为本地时间，并将其转换为 UTC。

只有当你有一个 ISO 格式的字符串时，才是使用它的最佳时机。我将在教程的后面告诉你如何创建一个。

#### 从一个数字创建一个 JS 日期

您可以从“unix 时间戳”创建日期，这是自 1970 年 1 月 1 日以来的毫秒数。这个数字就是 JavaScript Date 在内部存储日期的方式。如果您从服务器获得这样的时间戳，这是一个有用的方法，这种情况有时会发生，因为一些数据库以这种方式存储日期。

```
var date = new Date(1551531600000); 
```

## 查询 JavaScript 日期

### 局部地

有很多方法可以从 date 对象中获取本地日期和时间信息。这里对它们进行了总结。它们都返回当地时区的结果:

#### 日期

*   `getFullYear()` -返回年份，如果是 4 位数年份，则返回 4 位数。
*   `getMonth()` -返回月份，0 表示一月，1 表示二月，依此类推。
*   `getDate()` -返回一个月中的某一天。
*   `getDay()` -返回星期几，0 表示星期一，1 表示星期二，依此类推。

#### 时间

*   `getHours()` -返回小时。
*   `getMinutes()` -返回分钟数。
*   `getSeconds()` -返回秒。
*   `getMilliseconds()` -返回毫秒数。

这里有一个例子:

```
var date = new Date(2019, 2, 3, 0, 0, 5);
// Note that Date object stores the date and time as UTC internally, but it is converted
// back to local time when these methods are used:
date.getFullYear(); // 2019
date.getMonth(); // 2, i.e. March
date.getSeconds(); // 5 
```

### 采用 UTC

您还可以使用以下等效方法获取 UTC 日期和时间信息:

*   `getUTCFullYear()`
*   `getUTCMonth()`
*   `getUTCDate()`
*   `getUTCDay()`
*   `getUTCHours()`
*   `getUTCMinutes()`
*   `getUTCSeconds()`
*   `getUTCMilliseconds()`

这里有一个例子，同样使用`toUTCString`来显示存储在里面的完整日期。

```
var date = new Date(1551531600000);
date.toUTCString(); // Returns "Sat, 02 Mar 2019 13:00:00 GMT"
date.getUTCDate(); // 2
date.getDate(); // 1,2 or 3 depending on your local time zone. 
```

## 改变 JavaScript 日期

上面所有的 get 方法都有一个等价的 set，因此您可以更改日期:

*   `setFullYear()`
*   `setMonth()`
*   `setDate()`
*   `setDay()`
*   `setHours()`
*   `setMinutes()`
*   `setSeconds()`
*   `setMilliseconds()`
*   `setUTCFullYear()`
*   `setUTCMonth()`
*   `setUTCDate()`
*   `setUTCDay()`
*   `setUTCHours()`
*   `setUTCMinutes()`
*   `setUTCSeconds()`
*   `setUTCMilliseconds()`

这些方法中的每一个都接受一个数字，其格式与等效的 get 返回的格式相同......()方法。下面是一些例子:

```
var date = new Date(2019, 2, 3, 0, 0, 5);
date.getFullYear(); // 2019
date.setFullYear(2020);
date.getFullYear(); // 2020

var date = new Date(1551531600000);
date.toUTCString(); // Returns "Sat, 02 Mar 2019 13:00:00 GMT"
date.setUTCHours(10); 
date.toUTCString(); // Returns "Sat, 02 Mar 2019 10:00:00 GMT" 
```

## 转换

有一些方法可以将日期转换成不同的格式。以下是最有用的几个:

| 日期方法 | 时区 | 返回 |
| --- | --- | --- |
| toDateString() | 当地的 | 日期的日期部分，采用人类可读的格式。格式可能因浏览器而异。 |
| toTimeString() | 当地的 | 日期的时间部分，采用人类可读的格式。格式可能因浏览器而异。 |
| toUTCString() | 协调世界时。亦称 COORDINATED UNIVERSAL TIME | 日期和日期的时间部分，采用人类可读的格式。格式可能因浏览器而异。 |
| toisostring() | 协调世界时。亦称 COORDINATED UNIVERSAL TIME | 日期和时间为 UTC 时间，采用`YYYY-MM-DDTHH:mm:ss.sssZ`格式(ISO 8601 扩展格式)。 |
| toLocaleDateString() | 当地的 | 日期的日期部分，例如“3/2/2019”，根据您电脑的区域设置进行格式化。例如，月和日的顺序将取决于这些设置。 |
| 托 LocaleTimeString() | 当地的 | 日期的时间部分，例如“9:00:00 PM”，根据您计算机的区域设置进行格式化。例如，时钟类型 12 小时制或 24 小时制将取决于这些设置。 |

有一些类似的方法不太有用，或者不是完全被接受的标准，因此可能在您的浏览器中不起作用。要查看它们[请点击此处](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date#Conversion_getter)

示例，包含我所在时区的结果。根据您所在的时区、浏览器和计算机的区域设置，您的结果可能会有所不同。

```
var date = new Date(2019, 2, 3, 0, 0, 5);
date.toDateString(); // "Sun Mar 03 2019"
date.toTimeString(); // "00:00:05 GMT+1100 (Australian Eastern Daylight Time)"
date.toUTCString(); // "Sat, 02 Mar 2019 13:00:05 GMT"
date.toISOString(); // "2019-03-02T13:00:05.000Z"
date.toLocaleDateString(); // "3/3/2019"
date.toLocaleTimeString(); // "12:00:05 AM" 
```

上述结果在您的计算机上不能保证是相同的，因为当我们创建日期时，日期是从当地时区转换成 UTC 的。所以你的约会可能和我的不一样。即便如此，大多数方法返回的格式取决于您的计算机设置。

### [小心点...](#be-careful-)

**...使用取决于时区和电脑设置的方法**

我刚刚列出的大多数方法取决于时区、你的浏览器和你的电脑设置。应该小心使用它们。它们很适合向用户显示日期，但是如果您的程序(或其他人的程序)要再次读取相同的日期，则不推荐使用它们。这是因为您无法预测字符串的格式。一个月中的某一天和某个月中的某一天的顺序可能与您预期的不同，那么使用该日期的代码有时会出错。

#### 我推荐:

*   如果需要在程序中传递日期，尽可能将它们作为日期对象。
*   当向服务器或服务发送日期时，使用`toISOString`来格式化日期，因为这是标准格式并使用 UTC。或者使用`getTime`来获得自 1970 年 1 月 1 日以来的毫秒数，这也是用 UTC 表示的。
*   当从服务器或服务接收数据时，如果您有任何控制或影响，尝试确保它们作为 ISO 字符串或自 1970 年 1 月 1 日以来的毫秒发送，然后您可以将它们作为参数传递给`new Date()`。
*   仅使用`toDateString`、`toTimeString`、`toUTCString`等。将向网页用户显示的字符串的方法。
*   在使用带有日历控件的库时要小心，并且要阅读关于它们期望使用什么日期格式的文档。

## Moment.js

是否需要对日期做一些 [Javascript Date](https://superjavascript.com/t/javascript-dates/) 对象不支持的事情？例如，使用格式字符串格式化日期？或者更高级的时区转换？

[Moment.js](https://momentjs.com/) 是一个库，它为您提供了更多在 js 中处理日期的能力，如果您正在努力实现您对标准 JavaScript 日期对象的需求，那么推荐使用它。以后我会单独为 moment.js 写一个教程。

来自 *moment.js* 网站的第一个例子向您展示了您可以使用这个库按照自己的喜好格式化日期:

```
moment().format('MMMM Do YYYY, h:mm:ss a'); // March 2nd 2019, 9:06:12 am
moment().format('dddd');                    // Saturday
moment().format("MMM Do YY");               // Mar 2nd 19
moment().format('YYYY [escaped] YYYY');     // 2019 escaped 2019
moment().format();                          // 2019-03-02T09:06:12+11:00 
```

#### UTC 上的脚注

前面我说过 UTC 类似于格林威治标准时间(英国的冬季时区)。然而这是不一样的！两者之间最多有 1 秒钟的差别。

这是因为一天并不完全是 24 小时。我们的星球是一块大石头，不是劳力士。它正在减速。GMT 追踪地球自转来保持我们习惯的那种时间。UTC 使用科学上精确的秒，但需要沿途调整，以保持与地球自转同步。

我之前说过日期和时间很棘手吗？在任何情况下，几乎在所有情况下，你都不必担心那 1 秒钟，但知道这一点是有用的，它表明我们必须非常小心地对待日期和时间。

## 总结

本教程介绍了 Javascript 中的 Date 对象，它很好地为您提供了处理日期的基本功能。有时候这对你来说太有限了，你需要使用像 moment.js 这样的库，或者至少是一些你保存在程序某个部分的“实用函数”。了解时区是有好处的，JavaScript 日期是一个基于 UTC 的日期，即使您在本地时区创建和查询它。

这篇文章最初发布在超级 JavaScript: [JavaScript 日期](https://superjavascript.com/t/javascript-dates/)