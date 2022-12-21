# JAMStack 中 API 集成的多种方式

> 原文：<https://dev.to/raymondcamden/multiple-ways-of-api-integration-in-your-jamstack-4mh6>

这是我思考了几个星期的事情，现在我终于花时间把它写下来。我以前实际上已经报道过这个，但不是以一种明确的方式，我想整理一下我最近在这个问题上的一些想法。在我开始之前，有一个小提示。我有点晚采用了“JAMStack”这个绰号。坦白地说，这个名字困扰着我。为什么不直接称它们为静态网站呢？但是随着静态站点变得越来越强大(感谢各种生成器、API 和像 [Netlify](https://netlify.com) 这样的平台)，术语“静态站点”已经不再适用了。当你说“静态”的时候，特别是对一个可能只有一点点技术知识的客户，你暗示了一整套现在根本不适用的限制。“jam stack”(JavaScript、API 和标记)没有这些内涵，它确实有助于更好地构建我们正在谈论的内容。

好了，既然这样，那我今天到底要讲什么呢？当向 JAMStack 站点添加交互性时，通常会想到 API，可以用来获取动态数据的远程服务，然后用 JavaScript 在您的站点上呈现这些数据。但是有多种使用这些 API 和 JavaScript 的方法，起初你可能不太明白。在这篇文章中，我将回顾这些选项，并讨论什么时候你可能想使用一种形式而不是另一种形式。我将使用 Netlify 作为我的示例主机，但是我在这里讨论的一切也适用于(大多数)其他主机。我没有隐藏我对网络生活的明显喜爱，所以我有点偏见，但同样，这些原则也适用于其他地方。

## 选项一-直接访问远程 API

在 JAMStack 站点上使用 API 的最直接、最简单的方法是从 JavaScript 直接访问它。在这种形式中，您只需向资源发出一个 HTTP 请求并呈现它。这里有一个使用 Vue.js 和[星球大战 API](https://swapi.co/) :
的快速页面

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="https://vuejs.org/js/vue.min.js"></script>
    SWAPI Example
</head>
<body>

<div id="app">
    <h1>Star Wars Films</h1>
    <ul>
        <li v-for="film in films"></li>
    </ul>
</div>

<script>
const app = new Vue({
    el:'#app',
    data: {
        films:[]
    },
    created() {
        fetch('https://swapi.co/api/films')
        .then(res => res.json())
        .then(res => {
            this.films = res.results;
        });
    }
});

</script>

</body>
</html> 
```

你可以在 https://jamstackapiapproaches.netlify.com/test1.html 观看现场直播。

简单明了，对吧？然而，它也有一些缺点。

*   首先，它假设远程 API 启用了 CORS，这允许您的域直接访问它的域。许多 API 允许这样做，但不是所有的。
*   其次，它假设匿名访问。这实际上是*而不是*标准，因为典型的 API 需要某种标识符。有时候这没什么大不了的。API 有一个慷慨的免费层，不太可能被滥用。但是一旦你把一个 API 密匙放到你的代码中，任何可以查看源代码的人都可以拿到那个密匙并自己使用它。有些 API 会让你锁定哪些域可以使用那个密钥，在这种情况下，你是相当安全的。但是你绝对要记住这一点。
*   最后，您只能使用 API 提供的形式的数据。这听起来没什么大不了的，但是如果 API 返回了一堆你不需要的数据怎么办？你把这个负担加在了用户身上，这意味着(潜在的)更慢的网站和(再次，潜在的)令人沮丧的体验。这就是 GraphQL 真正的亮点，因为它允许您精确地指定您需要的数据。

总之，这是向 JAMStack 添加动态内容的最简单快捷的方式。

## 选项二——一个 API 代理

第二个选项与第一个非常相似，主要区别在于您的代码会调用运行在您的服务器上的 API。“服务器”可能只是一个运行在室内某处的应用服务器，但通常是一个无服务器平台。基本上，不是您的代码向某个远程域发出 HTTP 请求，而是它请求您的代码，然后代码本身从远程域请求数据。

考虑这个使用[天气 API](https://developer.here.com/documentation/weather/topics/overview.html) 的例子。(这是一家很酷的公司，我稍后会在博客中详细介绍。)他们的 API 需要两个特定的认证值，一个`app_id`和`app_code`。如果我把它放在我的客户端代码中，任何人都可以使用它，这是不可取的。我将使用一个用 [Netlify 函数](https://www.netlify.com/docs/functions/)设置的无服务器代理来代理客户端代码对这里的 API 的请求。

```
/* eslint-disable */
const fetch = require("node-fetch");

exports.handler = async function(event, context) {
  try {
    let app_id = process.env.HERE_APP_ID;
    let app_code = process.env.HERE_APP_CODE;

    const response = await fetch(`https://weather.api.here.com/weather/1.0/report.json?app_id=${app_id}&app_code=${app_code}&product=forecast_astronomy&name=Lafayette,LA`, {
      headers: { Accept: "application/json" }
    });
    if (!response.ok) {
      // NOT res.status >= 200 && res.status < 300
      return { statusCode: response.status, body: response.statusText };
    }
    const data = await response.json();

    let results = data.astronomy.astronomy.map(r => {
      return {
        moonRise:r.moonrise,
        moonSet:r.moonset,
        moonPhase:r.moonPhase,
        moonPhaseDesc:r.moonPhaseDesc,
        time:r.utcTime
      }
    });

    return {
      statusCode: 200,
      body: JSON.stringify({ data:results })
    };
  } catch (err) {
    console.log(err); 
    return {
      statusCode: 500,
      body: JSON.stringify({ msg: err.message }) 
    };
  }
}; 
```

一般来说，这只是一些琐碎的节点代码，但我想指出我在这里做的一些具体的调整。首先，这里的天气 API 支持返回天文数据。在我的演示中，我想了解月亮，所以你可以看到我在`map`调用中过滤掉了它。这将导致更少的数据成为我的客户端代码。还要注意，API 的大小写略有不同。所以对于`moonrise`来说都是小写的，但是后来他们用了`moonPhase`。这可能有一个很好的原因，但对我来说，这不是我所期望的，所以我抓住机会重新格式化数据。

一旦完成，我就可以用更多的 Vue.js 代码来使用它。(明确一点，不一定要用 Vue，但是我推荐。；)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="https://vuejs.org/js/vue.min.js"></script>
    Moon Data
</head>
<body>

<div id="app">
    <h1>Moon Data for Lafayette, LA</h1>
    <ul>
        <li v-for="result in results">
On {{result.time | formatDate}}, the moon will rise at {{result.moonRise}} and set at {{result.moonSet}}. 
It is in {{result.moonPhaseDesc}}.
        </li>
    </ul>
</div>

<script>
Vue.filter('formatDate', function(d) {
    if(!window.Intl) return d;
    return new Intl.DateTimeFormat('en-US').format(new Date(d));
}); 

const app = new Vue({
    el:'#app',
    data: {
        results:[]
    },
    created() {
        fetch('/.netlify/functions/get-moon')
        .then(res => res.json())
        .then(res => {
            this.results = res.data;
        });
    }
});

</script>

</body>
</html> 
```

你可以在这里查看:[https://jamstackapiapproaches.netlify.com/test2.html](https://jamstackapiapproaches.netlify.com/test2.html)

所以，这需要更多的工作，但是根据你的应用平台，这很容易。正如我所说的，我使用了 Netlify 函数，除了我遇到的配置问题(我将很快在博客上讨论这个问题)，这是微不足道的。这给了我们什么？

*   我们有能力隐藏任何需要的钥匙。
*   我们有能力改变结果。这可能包括删除我们不需要的数据，根据我们的需要更改数据，或者，如果对客户有用，我们甚至可以添加数据。
*   我们甚至可以换供应商。如果我的数据需要使用这里以外的人，我可以在服务器上更改它，前端代码不必知道。我只是确保结果数据与我之前使用的数据相匹配。
*   您还可以添加缓存。一些 API 提供者要求您不要这样做，但是您可以将数据存储在本地，只在需要的时候提取。
*   我能看到的唯一真正的“缺点”是，它绝对是一个有点多的工作。对我来说这很容易，但是我有在服务器上写代码和使用无服务器平台的经验。我不想低估这样一个事实，如果你唯一的 JavaScript 经验是客户端代码，那么不具备这些技能将是一个跳跃。

## 选项三-使用构建过程

在前两个选项中，我描述了两种方法，这两种方法对客户端来说本质上是一样的:点击一个 API(远程或本地)来获取数据。还有另一个选择可以考虑。根据您的需要，您的数据可能需要是“动态的”，但不是“非常动态的”。我这么说是什么意思？想想我和[布莱恩·里纳尔迪](https://remotesynthesis.com/) - [尾波断路器](https://codabreaker.rocks/)一起运行的音乐时事通讯的登陆页面。该网页列出了所有以前版本的时事通讯，因此人们可以了解他们正在注册的内容。我们大约一个月发布两次，所以虽然数据肯定是动态的，但很少改变。

我们可以在我们站点的平台上使用一个构建脚本，而不是为用来托管时事通讯的 API 构建一个无服务器代理。我这么说是什么意思？想象一个简单的脚本，它点击一个 API，然后将数据保存在一个平面文件中。

```
const fetch = require('node-fetch');
const fs = require('fs');

fetch('https://swapi.co/api/films')
.then(res => res.json())
.then(res => {
    let films = res.results.map(f => {
        return {
            title:f.title,
            director:f.director,
            releaseDate:f.release_date
        }   
    });

    let generatedHTML = '';
    films.forEach(f => {
        generatedHTML += `<li>${f.title} was released on ${f.releaseDate} and directed by ${f.director}.</li>`;
    });

    let contents = fs.readFileSync('./test3.html','utf8');
    contents = contents.replace('{{filmData}}', generatedHTML);

    fs.writeFileSync('./test3.final.html', contents);

}); 
```

这个节点脚本向星球大战 API 发出一个 HTTP 请求。然后它将结果转换成 HTML。请注意，我是在李块包装薄膜。一旦完成，它读入一个源文件，寻找一个特殊的标记并用 HTML 字符串替换它，然后保存它。我使用了不同的文件名，但是我可以很容易地将源文件改写为，因为这是在已部署的构建上。这里是`test3.html` :

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    SWAPI Example
</head>
<body>

<div id="app">
    <h1>Star Wars Films</h1>
    <ul>
        {{filmData}}
    </ul>
</div>

</body>
</html> 
```

最后一点是告诉我的构建服务器在部署我的 JAMStack 站点时运行它。Netlify 允许您为我的演示站点指定一个构建命令，运行我的 package.json 文件中的命令，`npm run build`。此处定义:

```
 "scripts": {
    "build": "node build && cd functions/get-moon && npm i"
  }, 
```

忽略第一个`&&`之后的所有内容，这与无服务器函数有关，但是第一部分只是运行我的小脚本来更新平面文件。你可以在这里看到结果:[https://jamstackapiapproaches.netlify.com/test3.final.html](https://jamstackapiapproaches.netlify.com/test3.final.html)没有 JavaScript，它应该工作得非常快，而且非常有弹性。我的构建脚本肯定可以添加错误检查、回退内容等等。

现在每当我的网站建立时，内容会自动更新。我可以手动完成这项工作，或者像我使用 Coda Breaker 一样，设置一个回 Netlify 的 webhook，以便在发布新的时事通讯时触发构建。所以它是静态的…但却是动态的。它是手动的…但是是自动的。我喜欢这样。

## 结论

我希望这真的展示了静态时的选择，对不起，我是说 JAMStack。当然，我没有涵盖任何可能的迭代，一个网站可以利用其中的许多。我很想听听你对你正在使用的技术的评论，所以请给我留言！如果你愿意，你可以在这里浏览我的演示站点的源代码回购:[https://github.com/cfjedimaster/jamstack_api_approaches](https://github.com/cfjedimaster/jamstack_api_approaches)。

*标题图片由[本杰明·埃利奥特](https://unsplash.com/@benjaminjohnelliott?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash 上拍摄*