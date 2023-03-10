# 用 JavaScript +有趣的语言 API 编写的观察者设计模式教程

> 原文：<https://dev.to/erikwhiting88/observer-design-pattern-tutorial-in-javascript-fun-with-a-language-api-21o3>

【2021 年 4 月更新: *发布我在本教程中使用的 API 密钥的网站已经不再提供免费的 API 密钥，这里的例子就不管用了。非常抱歉*:(

大家好，谢谢你点击这篇文章。我对我们今天的教程感到非常兴奋，因为它结合了我最喜欢的两样东西:编程和语言(口语)。

我将向您展示如何用 JavaScript 实现“观察者”设计模式，然后我将展示它在一个 web 项目中的用途。**我们 1000%鼓励你采用这段代码，调整它，使它成为你自己的代码，并在你的文件夹中展示它**，因为如果我自己这么说的话，我们的最终产品会非常酷。([这个](http://erikwhiting.com/newsOutlet/)就是我们要做的)

今天的日程如下:

*   快速说说*是什么*是观察者模式
*   谈谈我们将如何用 JavaScript 实现它
*   `The code`
*   我们将何去何从？

# 什么是...

## ..一个设计模式？

在编程中，设计模式是我们经常遇到的问题的可靠解决方案。根据老维基百科，他们是

> ...程序员在设计应用程序或系统时可以用来解决常见问题的正式的最佳实践

## ..观察者模式？

观察者模式是当我们有一个对象控制一个订阅者列表时。官方说法是，我们将订阅控制对象称为“主体”，将订阅者称为“观察者”

举一个简单而淡化的例子，想想新闻机构 BBC。BBC 会从各地获取新闻，然后期待他们不同的网络对此进行报道。他们用英语报道 BBC 世界新闻，用阿拉伯语报道 BBC 阿拉伯语，用葡萄牙语报道 BBC 巴西新闻。在这种情况下，英国广播公司作为一个整体将是主题，观察员将是各种网络(世界服务，阿拉伯语，巴西)。

# 我们要做什么？

很高兴你问了！我们将创建两个 JavaScript 类，`News`我们的主题，`NewsOutlet`我们的观察者。我们将实例化一个新闻对象和三个观察者，将观察者添加到主题的订阅者列表中，并通过主题将数据传输给所有观察者，将其翻译成不同的语言，并显示出来。

听起来很困惑？我向你保证，不是的。让我们开始编码吧，你会看到的。

# JavaScript ❤️

## 主语

首先，我们的主题，我们将其命名为“新闻”:

```
// The news class is the Observable class or "subject"
class News {
  // A list of observers
  constructor() {
    this.observers = [];
  }

  // Method for subscribing to, or "observing" observable
  addSubscriber(subscriber) {
    this.observers.push(subscriber);
  }

  // Method for unsubscribing from observable
  unsubscribe(subscriber) {
    var index = this.observers.indexOf(subscriber);
    this.observers.splice(index, index);
  }

  // Method for sending data to subsribers
  transmit(data) {
    this.observers.forEach(subscriber => subscriber.receive(data));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们一个方法一个方法地讨论这个方法:

*   这里没什么特别的，我们只是想确保当新闻被创建时，它有一系列的观察者。我们稍后会添加它们。

*   这是正式让观察者订阅该主题的方法。添加订户的语法类似于`Subject.addSubscriber(observer)`

*   `unsubscribe` -不用说，但是我们传递给这个函数的观察者将停止从主体获取数据。

*   这就是数据如何传递给观察者的。这个方法遍历主题的观察者数组，并调用观察者的`receive`方法。显然，这意味着我们*必须*给观察者类一个`receive`方法。

因此，简而言之，这是我们观察者模式的一个非常基本的主题。现在让我们定义组成我们的观察者的类。

## 观察者

这是我们的观察者“NewsOutlet”的代码，我们将一个方法接一个方法地查看

```
// The News Outlets are subscribers to the news in different languages
class NewsOutlet {
    // We will set the language when we instantiate the news outlet
    constructor(language = "en") {
        this.language = language;
        this.data = "";
        this.news = "";
        // For this example we'll use my API key, but please go to 
        // https://yandex.com/ and sign up to get your own
        this.apiKey = "trnsl.1.1.20190807T020501Z.f95163fde699ac87.1f9b3df7b5d7c045104d21249dc322086ee38004";
        this.translateUrl = "https://translate.yandex.net/api/v1.5/tr.json/translate";
    }

    receive(data) {
        this.data = data;
        var urlParamList = "?";
        urlParamList += "key=" + this.apiKey;
        urlParamList += "&text=" + this.data;
        urlParamList += "&lang=" + this.language;
        var self = this;

        // Translate after receiving
        jQuery.ajax({
         url: this.translateUrl + urlParamList,
         contenttype: "application/json",
         datatype: "json",
         success: function(result) {
            self.news = result.text[0];
            self.reportTheNews();
         }
        });

    }

    reportTheNews() {
        // A shady workaround for our HTML efforts!
        let elemId = this.language + "1";
        document.getElementById(elemId).innerText = this.news;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

好吧！这里有很多东西要打开，所以我们慢慢来。

首先，我们将使用 Yandex API 进行翻译。请去[https://yandex.com](https://yandex.com)获取你自己的 API 密匙，并在你运行这个太多次之前在那里与我的交换。免费的！

*   对于观察者来说，我们在实例化他们的时候会给他们一种语言。我们必须将 ISO 语言代码传递给 API 来完成这项工作([这里的](https://www.sitepoint.com/iso-2-letter-language-codes/)是所有受支持语言的列表)。“en”是英文，也是我们的默认。我们正在制作名为`data`(来自我们主题的数据)和`news`(翻译数据)的实例变量。最后，为了方便起见，我们将 API 键和翻译 API URL 放在实例变量中。

*   还记得在新闻课上，我们的`transmit`方法调用我们所有观察者的`receive`方法吗？这就是了。这种方法首先从对象获取传输的数据，并将其放入自己的数据变量中。然后，它构建完整的 API URL，我们将使用它来翻译传输的数据。

*   第`jQuery.ajax`部分——这是使用公共 API 的一个非常基本的方法。我们分配 URL、内容类型和数据类型，然后说在成功调用 API 之后需要发生什么。注意这个函数前面的`var self = this;`。我们这样做是因为`this`在 ajax 函数中不可用，所以我们将使用`self`。不要让这让你困惑，在某些语言中`self`是一个保留字。不是在 JavaScript 中，你可以随意调用这个变量。无论如何，一旦 API 调用成功返回，它会将`news`实例变量设置为返回的`result.text`对象的第一个元素(看起来像`["this is the text returned in an array for some reason"]`)。最后，它将调用`reportTheNews`方法，你可以通过评论看到，这不是我超级自豪的东西。

*   `reportTheNews` -当我们稍后在 HTML 中展示时，我们将有一些`div`元素显示新闻。如果你想在控制台上看到结果，就像这样修改函数:

```
reportTheNews() {
    console.log(this.news);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 快好了，让我们回顾一下

此时，我们实际上已经构建了我们的观察者模式。如果你想看到整个事情的运行，确保像上面显示的那样切换`reportTheNews`中的代码，并将其写入你的控制台:

```
let news = new News;
let enOutlet = new NewsOutlet("en");
let ptOutlet = new NewsOutlet("pt");
let arOutlet = new NewsOutlet("ar"); 
```

Enter fullscreen mode Exit fullscreen mode

这些线创建了我们的主题`news`和观察者`enOutlet`、`ptOutlet`和`arOutlet`。让我们的经销店订阅:

```
news.addSubscriber(enOutlet);
news.addSubscriber(ptOutlet);
news.addSubscriber(arOutlet); 
```

Enter fullscreen mode Exit fullscreen mode

我们已经准备好了。决定你希望第一个标题是什么。我的将是“埃里克是最棒的”

```
> news.transmit("Erik is the best")
< undefined
  Erik is the best
  إريك هو أفضل
  Erik é o melhor 
```

Enter fullscreen mode Exit fullscreen mode

就叫我环球先生吧😎

就是这样！这就是观察者的模式。我们通过主题(本例中为`news`)将数据传输给订户(3 个`**Outlet`变量)。

现在，我将把它放入一些有价值的东西中，非常欢迎你跟随。但是为了“学习观察者模式”，你完了！恭喜你，直接跳到“我们将何去何从？”部分

# 现场试玩！

准备好将我们所学付诸实践了吗？这是我们将要建造的:

*   [现场演示](http://erikwhiting.com/newsOutlet/)
*   [Github 回购](https://github.com/erik-whiting/JsObserverDemo)

## 像上面的演示一样实现观察者模式

让我们开始吧。首先，如果您切换了`reportTheNews`功能，请将其切换回

```
 reportTheNews() {
        // A shady workaround for our HTML efforts!
        let elemId = this.language + "1";
        document.getElementById(elemId).innerText = this.news;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们将在制作完 HTML 页面后使用它。之所以以迂回的方式进行配置，是因为 ajax 有时的行为方式。我不会深入讨论这个问题，因为这个项目并不是一个优秀 ajax 作品的范例，所以让我们继续。

## HTML 部分

让我们做一个输入，我们可以在这里输入我们的新闻，一个发送它的按钮，以及一些向我们显示不同媒体在说什么的东西:

```
<!DOCTYPE html>
<html>
    <head>
        <link rel="stylesheet" type="text/css" href="style.css">
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
        <script src="News.js"></script>
        <script src="NewsOutlet.js"></script>
        <script src="implement.js"></script>
    </head>
    <body onload="defaultBreaking()">
        <h2 id="pageTitle">The World News</h2>
        <h3>JavaScript implementation of the Observer Pattern</h3>

        <div class="worldNews">
            <div>
                What's the news??
            </div>
            <div>
                <input type="text" id="sourceNews">
            </div>
                <button onclick="sendNews()" id="transmitter">Transmit!</button>
            <div>
            </div>
        </div>

        <div class="row">
            <div class="column" id="enOutlet">
                <div class="newsTitle">The US Gazette</div>
                <div id="en1" class="breaking"></div>
            </div>
            <div class="column" id="ptOutlet">
                <div class="newsTitle">The Portugal Post</div>
                <div id="pt1" class="breaking"></div>
            </div>
            <div class="column" id="arOutlet">
                <div class="newsTitle">The Halab Herald</div>
                <div id="ar1" class="breaking"></div>
            </div>
        </div>

        <div class="footer" id="Yandex">
            All translation Powered by <a href="https://translate.yandex.com/">Yandex</a>
        </div>
        <div class="footer">
            <p>by Erik Whiting</p>
        </div>

    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您正在复制/粘贴，您必须将我们正在创建的其他文件命名为与我相同的名称:

*   `style.css`
*   `News.js`
*   `NewsOutlet.js`
*   (我们还没做这个)

## CSS

*旁注*我真的很讨厌 CSS，但我正在努力变得更好。我绝对不是设计师，所以不要嘲笑我:

```
* {
  box-sizing: border-box;
}

body, h2, p {
  font-family: Courier, sans-serif;
}

#pageTitle {
  font-size: 50px;
}

.worldNews {
  background-color: #262626;
  color: white;
  padding: 30px;
  text-align: center;
  font-size: 35px;
  border: 1px solid black;
  border-radius: 8px;
}

button {
  background-color: orange;
  font-family: Courier;
  font-color: black;
  font-size: 20px;
  width: 400px;
}

input {
  width: 400px;
  height: 40px;
  font-size: 30px;
}

.column {
  float: left;
  width: 33.33%;
  padding: 10px;
  height: 300px;
  border-radius: 8px;
  border: 1px solid black;
}

.newsTitle {
  width: 100%;
  text-align: center;
}

.breaking {
  text-align: left;
  font-size: 20px;
  border: 1px solid;
  border-radius: 8px;
  padding: 5px;
  margin: 2px;
}

#enOutlet {
  color: white;
  background-color: #820400;
}

#enOutlet > .breaking {
  background-color: #fc6f6a;
}

#ptOutlet {
  color: black;
  background-color: #008c00;
}

#ptOutlet > .breaking {
  background-color: #78f580;
}

#arOutlet {
  color: white;
  background-color: #000485;
}

#arOutlet > .breaking {
  background-color: #515cfc;
}

.newsTitle {
  font-size: 20px;
}

.row:after {
  content: "";
  display: table;
  clear: both;
}

.footer {
  background-color: #f1f1f1;
  padding: 10px;
  text-align: left;
}

@media (max-width: 600px) {
  .column {
    width: 100%;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想把它变成你自己的项目，试试它的格式和颜色，让它成为你自己的，向人们展示你艺术的一面！

## 把它联系在一起的脚本

好了，最后一件事是最后一件事，我们需要一个小脚本来把所有的东西放在一起。我把它叫做`implement.js`，没有别的原因，只是我想不出一个更好的名字:

```
// Create all the objects we'll use
let news = new News;
let enOutlet = new NewsOutlet("en");
let ptOutlet = new NewsOutlet("pt");
let arOutlet = new NewsOutlet("ar");

// Subscribe
news.addSubscriber(enOutlet);
news.addSubscriber(ptOutlet);
news.addSubscriber(arOutlet);

// A function for setting the news elements to prompt user to type
// you don't need this if you don't want, just remove the onload
// from the HTML file if you remove this
var defaultBreaking = function() {
  var breaking = document.getElementsByClassName("breaking");
  for (let item of breaking) { item.innerText = "Type some news..."; }
}

// The function that will call transmit on news
// setting off the chain event of subscribers receiving
// then translating, then showing the news
var sendNews = function() {
  let theNews = document.getElementById("sourceNews").value;
  news.transmit(theNews);
  news.observers.forEach(function(o){
    o.reportTheNews();
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。如果你一直在复制/粘贴，那么你的网站应该看起来像我的。如果你在 windows 上遇到一个奇怪的关于跨站脚本的错误，打开`run.exe`并输入这个:

```
chrome.exe  --user-data-dir="C:/Chrome dev session"  --disable-web-security 
```

Enter fullscreen mode Exit fullscreen mode

但是要小心，不要经常这样做。

# 我们将何去何从？

设计模式很棒，了解它们会让你成为更好的程序员，我不在乎别人怎么说。就像数据结构和算法一样，当你至少知道了设计模式的*和它们的一般用途，你的脑海中就会有许多常见问题的解决方案列表。*

当人们谈论观察者模式时，我经常看到的一个例子是交易。假设一个股票代码从 1.02 升级到 1.14，有一千个不同的界面需要提醒。理想情况下，无论这种东西在什么服务器上运行，这些接口中的每一个都应该订阅广播股票代码价格的对象。

我们用的例子非常简单，你可以用它做很多事情。比如，我很确定`KnockoutJS`框架(嗨 C#开发者们！)完全建立在这个概念上。这只是一个非常酷的设计模式的例子，还有很多需要学习。

如果你喜欢这个教程，请告诉我你喜欢它的什么，以及你希望我接下来谈论什么。一种新的模式？不同的语言？不同语言的新模式？完全不同的东西？让我知道，请不要犹豫问我任何问题。