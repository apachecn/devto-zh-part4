# [JavaScript] -如何制作直观且自我记录的代码

> 原文：<https://dev.to/adyngom/javascript-how-to-craft-intuitive-and-self-documenting-code-43dh>

作为一名开发人员，你可能花更多的时间阅读代码，而不是实际编写代码。除了你的常规输出和重构，想想你花在阅读教程、文档、查看拉取请求、查看 Github 上最新的酷酷的回复、维护一个项目等等上面的时间。

不幸的是，阅读并不一定转化为理解。我敢打赌，一个代码库或项目没有被采用或放弃的主要原因之一仅仅是因为它需要太多的大脑处理能力，简单地说就是不直观。

在西方文化中，很多人都很熟悉食物的 5 秒规则，我知道有些人在这种情况下觉得 5 秒非常慢。

说到代码，约翰爸爸理所当然地说:

> 如果你不能在 5 秒钟内理解代码，很可能有一种气味，很可能有一种想法，你可能应该重构。
> __ 约翰爸爸——可读代码

所以，让我来说清楚:

*   首先向您介绍一个代码重构的实际用例。
*   然后我们可以讨论它可以提供的附加价值和好处，最后
*   分享一些有用的资源，它们对“干净代码”的探索非常有帮助

如果你是一个经验丰富的干净的程序员，我们希望你能通过在评论中分享一些关于这个主题的很酷的技巧和资源来帮助我们进一步提高。

## 一个“简单”的用户故事

给定一个酒店列表，请选择那些位于美国的酒店，并提供经过过滤的列表以及格式正确的电话号码。以下是酒店列表的样本。

```
const hotels = [
    {
     name: "Panthers Alley",
     country: "Wakanda",
     address: "123 Best Luck Finding, 3 Rivers, Wakanda",
     phone: "5551790871"
    },
    {
     name: "The Golden Thread",
     country: "USA",
     address: "5 Maple Street, Los Angeles, CA, 90000",
     phone: "2125552345"
    },
    {
     name: "12 Crowns",
     country: "Japan",
     address: "36 Wide Alley Blvd, Tokyo",
     phone: "5558725910"
    },
    {
     name: "Petit Paris",
     country: "usa",
     address: "3669 Elm Street, New York, 30000",
     phone: "7075559087"
    },
    {
     name: "The Empress Lounge",
     country: "USA",
     address: "1 Kings Place, Atlanta, GA, 30000",
     phone: "6785553125"
    }
]; 
```

如果你能在 5 秒钟或更短的时间内阅读以上内容并不斜视地理解发生了什么，我向你鞠躬🙂

公平地说，尽管提议的解决方案有许多优点，例如:

*   常数 USHotels 的命名是一个非常具有描述性的选择。这暗示了它将保存的值的类型，可能是美国的酒店列表。
*   使用 map 和 filter 提取结果并将其转换为新的集合，保持原始对象不变，或者用更专业的术语来说，有助于避免原始对象发生变异。
*   Map 和 filter 还支持更具声明性的编码风格，而不是 for 或 forEach 循环中不可避免的命令式。

如果你想了解更多关于**命令式 vs 声明式**的知识，帮你自己一个忙，读一读来自**泰勒·麦金尼斯** : [命令式 vs 声明式编程](https://tylermcginnis.com/imperative-vs-declarative-programming/)的这篇好文章

简而言之，陈述性更关心的是**是什么**，而命令性则是明确的关于**如何**。

这让我们看到了我们的解决方案中可能需要修改的第一个方面 map 块太冗长，并且深入探究了**如何将一个无格式的数字转换为美国标准。**

在我们的重构工作中，我们将创建一组专门的单元——读函数，这将有助于我们轻松阅读和理解，但也会产生创建可重用代码的副作用。

我个人称之为**“工具化”步骤**——一个很酷的杜撰词😉请随意提出其他建议。我听着呢。

[![Toolify: turn your code into small units of specialization](img/3e7b43e38cf25be1520a505faddf407c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N0LB8N8k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gq4v6wl9ryhnf2m44b23.jpeg)

## 让我们抽象而征服

map 块很长，将是我们重构的主体，但是让我们从过滤步骤开始。

让我们首先创建一个名为**pro equals**的助手函数，它只关心一个**对象 x** 的属性是否等于一个**值 y** 。

如果对您来说不明显，请随意使用更具描述性的名称。重要的是这个名字带有一个隐式的返回类型。

很明显，它将返回一个布尔值。

```
function propEquals(key, value, matchCase = false) {
 return function(obj) {
   return (!matchCase) ? obj[key] === value 
         : obj[key].toLowerCase() === value.toLowerCase();
   }
} 
```

**pro equals**足够通用，可以针对任何对象查找任何键/值对。例如，如果我们想知道 hotels 数组中的第一个对象是否是 Wakanda，我们可以这样调用它:

```
const IsWakanda = propEquals(‘country’, ‘Wakanda’)(hotels[0]);
console.log(IsWakanda); // will output true 
```

现在让我们添加一个更具体的函数，它只针对美国国家。

使用 **propEquals** 的外部函数，我们可以**“固定”参数值**始终默认为美国。让我们把这个新功能命名为**is country**。

```
const isUSCountry = propEquals.call(null, ‘country’, ‘USA’, true); 
```

简单地说，我们可以说**is country**派生自**pro equals**，并将父函数的默认值设置为:

*   国家是关键，
*   美国作为价值，
*   并希望选择字符串 USA 的小写或大写值。

用“更简单”的专业术语来说，**is country**是**pro equals**的**成分**。

使用它会给我们一个非常漂亮和直观的语法:

```
const USHotels = hotels.filter(isUSCountry);
console.table(USHotels); 
```

和下面是过滤后的数组
的输出

```
[
    {
      "name": "The Golden Thread",
      "country": "USA",
      "address": "5 Maple Street, Los Angeles, CA, 90000",
      "phone": "2125552345"
    },
    {
      "name": "Petit Paris",
      "country": "usa",
      "address": "3669 Elm Street, New York, 30000",
      "phone": "7075559087"
    },
    {
      "name": "The Empress Lounge",
      "country": "USA",
      "address": "1 Kings Place, Atlanta, GA, 30000",
      "phone": "6785553125"
    }
  ] 
```

这种语法，其中 **USHotels** 构造没有明确指定任何参数，而只是函数引用，被称为**默认编程或无点组合**

## 帮手越多越开心

现在我们已经完成了过滤器部分，让我们用同样的方法来处理转换部分或映射。

首先，让我们提取电话格式功能，并将其与总是绑定到一个对象的事实分离开来。

我们应该能够格式化一个数字，不管它是不是对象的一部分。

所以下面我们现在有了我们的 **USPhoneFormat** 函数:

```
function UPhoneFormat(tel) {
    let area, prefix, line;
    area = tel.substr(0, 3);
    prefix = tel.substr(3, 3);
    line = tel.substr(6, 4);
    const formatted = `(${area}) ${prefix}  ${line}`;
    return formatted;
} 
```

现在让我们添加最后一个助手**变形金刚**。它基本上接受一个键和一个转换函数，并将值转换应用到给定的对象上。

```
function transformProp(key, fn) {
   return function(obj) {
     return {…obj, [key]: fn.call(null, obj[key]) };
   }
} 
```

现在，按照我们对**is country**所做的相同逻辑，我们可以“编写”一个更具体的功能，只处理电话号码格式
T0】

```
const formatPhone = transformProp.call(null, ‘phone’, UPhoneFormat); 
```

## 大手笔

既然我们已经准备好了所有的“工具”，我们最终可以修改我们原来的语法，就像这样“在折叠之上”:

```
const USHotels = hotels.filter(isUSCountry).map(formatPhone);
//add all the functions below the fold or better yet use imports 
```

登录 **USHotels** 到控制台会输出如下:

```
[
 {
 “name”: “The Golden Thread”,
 “country”: “USA”,
 “address”: “5 Maple Street, Los Angeles, CA, 90000”,
 “phone”: “(212) 555 2345”
 },
 {
 “name”: “Petit Paris”,
 “country”: “usa”,
 “address”: “3669 Elm Street, New York, 30000”,
 “phone”: “(707) 555 9087”
 },
 {
 “name”: “The Empress Lounge”,
 “country”: “USA”,
 “address”: “1 Kings Place, Atlanta, GA, 30000”,
 “phone”: “(678) 555 3125”
 }
] 
```

有些人现在可能会挠头想“要得到基本相同的结果，需要很多步骤。我们从中获得了什么？”很高兴你问了。

真正的收获可以归结为我所说的 S.C.R.E.A.M.S 原则。使用它，我试图总是确保我的代码是:

*   自我记录——希望这是一个明显的例子
*   可组合的——造词，但给你一个概念
*   可读——首先用于人类，其次用于机器
*   雄辩——主观，但你定义了什么，并坚持下去
*   **一个**b 抽象层——在任何有意义的地方添加它
*   不可维护——小单元易于测试
*   可扩展——独立和通用的函数易于重用

## 在关闭

老实说，在过去的几个月里，我已经开始做额外的“工艺”努力，我可以承认这是一个非常非常缓慢的开始。

如果你对此感兴趣，我建议从小型重构开始。

使用**映射、过滤、减少**和**绑定、调用、应用**的组合是你的瑞士刀，可以带你走很长的路，并且是有助于在 JavaScript 中产生“干净代码”的途径之一。

如果你被说服了，现在是时候编写一些代码了！！🙂

## 几个资源

来自主人本人——收藏它！！！
[可读代码](https://speakerdeck.com/johnpapa/readable-code)

**老掉牙但仍不失为好东西**
[JavaScript Apply、Call 和 Bind 方法对 JavaScript 专业人员来说是必不可少的](https://javascriptissexy.com/javascript-apply-call-and-bind-methods-are-essential-for-javascript-professionals)

**感谢不尽[克里斯蒂安·萨尔塞斯库](https://www.freecodecamp.org/news/author/cristiansalcescu/)对于这篇伟大的作品**
[无点构成如何让你成为一个更好的函数式程序员](https://www.freecodecamp.org/news/how-point-free-composition-will-make-you-a-better-functional-programmer-33dcb910303a/)

* * *

你好，我是紫琳·恩戈姆。在专业上，我拥有解决方案设计师/ JS 技术主管的头衔。但本质上，我只是对 JavaScript 和前端生态系统充满热情。在推特、 [LinkedIn](https://www.linkedin.com/in/adyngom/) 上与我联系，并访问我的博客【adyngom.com】T4