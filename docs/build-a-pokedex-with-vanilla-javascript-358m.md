# 用普通的 JavaScript 构建一个 Pokedex

> 原文：<https://dev.to/jamesqquick/build-a-pokedex-with-vanilla-javascript-358m>

[https://www.youtube.com/embed/T-VQUKeSU1w](https://www.youtube.com/embed/T-VQUKeSU1w)

像 React 这样的前端框架得到了很多关注，但是你并不总是需要它。在这篇文章中，我将结合我对两样东西的热爱，JavaScript 和口袋妖怪。我们将使用普通的 HTML、CSS 和 JavaScript 为最初的 150 个口袋妖怪构建一个 Pokedex。

> 查看 [Codepen](https://codepen.io/jamesqquick/pen/NWKaNQz) 获取源代码！

## 脚手架工程

这个项目不会有很多文件，即一个**index.html**、 **app.css** 和一个 **app.js** 。也就是说，要开始，在你的电脑上创建一个文件夹，用你最喜欢的编辑器打开它。我的是 [VS 代号](https://code.visualstudio.com)！

打开文件夹后，您需要从上面创建三个文件。

*   index.html
*   app.css
*   app.js

> 尝试一下 VS 代码扩展 [Web 样板](https://marketplace.visualstudio.com/items?itemName=jamesqquick.web-boilerplate)，它可以为您生成这三个文件

创建完这些文件后，您现在需要链接到 index.html 内部的 CSS 和 JavaScript 文件。您的 HTML 代码将如下所示。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    Quick's Pokedex
    <link rel="stylesheet" href="app.css" />
  </head>
  <body>
    <script src="app.js"></script>
  </body>
</html> 
```

有了样板代码，我们就可以开始向页面添加一些内容了。首先添加一个 **div** ，在主体内部有一个**容器类**。这是我们放置所有内容的地方。

在容器 div 内部，添加一个带有应用程序名称的 **h1** 元素。我把我的叫做“Quick's Pokedex”。最后，添加一个 id 为 **pokedex** *的 **ol** 元素。这个有序列表是我们将要显示所有口袋妖怪信息的地方。*

你的 HTML 文件应该是这样的。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    Quick's Pokedex
    <link rel="stylesheet" href="app.css" />
    <link
      href="https://fonts.googleapis.com/css?family=Rubik&display=swap"
      rel="stylesheet"
    />
  </head>
  <body>
    <div class="container">
      <h1>Quick's Pokedex</h1>
      <ol id="pokedex"></ol>
    </div>
    <script src="app.js"></script>
  </body>
</html> 
```

## 使用 PokeAPI 获取口袋妖怪数据

信不信由你，这是我们将添加到 HTML 页面的所有内容。现在，我们转到 JavaScript，我们将从 [PokeAPI](https://pokeapi.co) 加载口袋妖怪数据。为此，我们将使用 [JavaScript Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 向 API 发出 HTTP 请求。

> 要复习获取 API，请查看 [JavaScript 获取 API -入门！](https://www.youtube.com/watch?v=uBR2wAvGces&t=1s)

让我们先来看看 API 是如何工作的。要获得关于某个口袋妖怪的信息，您可以发出请求并传递该口袋妖怪的 id。所以，如果你想得到关于妙蛙种子(数字 1)的信息，你可以像这样在请求的末尾加 1。

[https://pokeapi.co/api/v2/pokemon/1](https://pokeapi.co/api/v2/pokemon/1)

这已经足够好了，但是我们需要 150 个口袋妖怪的信息，而不仅仅是一个。为此，我们可以遍历数字 1-150，并为每个数字发出 API 请求。这是它的一个片段。

```
for (let i = 1; i <= 150; i++) {
  const url = `https://pokeapi.co/api/v2/pokemon/${i}`;
  fetch(url)
    .then(res => res.json())
    .then(pokemon => {
      console.log(pokemon);
    });
} 
```

在这段代码中有几件事需要注意。我们使用 ES6 模板文字字符串将 *i* 的值(索引)附加到每个请求的末尾。此外，我们必须通过调用 **res.json()** 将初始获取响应转换为 JavaScript 对象。我们使用“承诺链”来实现这一点。

> 要重温 JavaScript 承诺，请查看 [JavaScript 承诺-入门](https://www.youtube.com/watch?v=gyC19H4ip1k)

最后，我们可以访问保存口袋妖怪数据的**口袋妖怪**变量。但是这里有一个大问题。这是一种**非常低效的**方式来发出如此多的 API 请求。因为 API 调用是在 For 循环内部，所以我们必须等待每个调用完成后才能进行下一个调用。

> 通常，不应该在 For 循环中进行异步调用。

我们可以通过使用 [Promise.all()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 来更新这个。Promise.all()允许我们并行运行异步 API 调用，而不是顺序运行。这意味着发出 150 个请求所需的时间将与发出 1 个请求所需的时间大致相同！这是一个巨大的进步！

这是它的代码。

```
const promises = [];
for (let i = 1; i <= 150; i++) {
  const url = `https://pokeapi.co/api/v2/pokemon/${i}`;
  promises.push(fetch(url).then(res => res.json()));
}

Promise.all(promises).then(results => {
  console.log(results);
}); 
```

这可能看起来有点复杂，所以让我们分解步骤。

1.  创建一个空数组来保存承诺
2.  迭代 1-150
3.  发出返回承诺的 API 请求
4.  将该承诺添加到承诺数组中
5.  使用 Promise.all()等待所有请求完成(并行)
6.  使用**结果**变量访问口袋妖怪信息数组

## 转换口袋妖怪数据

载入 Pokemon 数据后，我们现在需要将它转换成我们想要使用的格式。例如，对于每个返回的 Pokemon 对象，都有大量不必要的数据。我们只关心几个属性。

*   名字
*   身份证明（identification）
*   类型
*   图像

总的来说，我们希望将请求中的数据数组转换成只有上述信息的 Pokemon 数据数组。我们可以使用 [Array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 函数来处理这种转换。

> 每当你想转换数组中的每一项时，想想 [Array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ！

```
const pokemon = results.map(data => ({
  name: data.name,
  id: data.id,
  image: data.sprites["front_default"],
  type: data.types.map(type => type.type.name).join(", "),
})); 
```

如您所见，我们不费吹灰之力就检索到了所需的大部分数据。然而，类型数据并不容易。传入的 Pokemon 数据将类型表示为一个数组，但我们想要一个逗号分隔的字符串。对于这个转换，我们可以使用 **map()** 数组函数和一个 **join()** 字符串函数**。**

```
type: data.types.map(type => type.type.name).join(", "); 
```

## **显示口袋妖怪数据**

现在我们有了转换后的口袋妖怪数据，是时候在屏幕上显示它了。这可能看起来有点奇怪，但是我们将通过使用 JavaScript 生成一个 HTML 字符串来做到这一点。该字符串表示进入 **ol** 元素内部的 HTML 内容。以下是我们将要遵循的步骤。

1.  创建一个名为 displayPokemon 的功能
2.  从 fetchPokemon 调用此函数，将 Pokemon 数据作为参数传递
3.  使用 Array **map()** 将每个口袋妖怪对象转换为一个 **li** 元素字符串
4.  使用 String **join()** 来连接所有的元素字符串
5.  获取对 JavaScript 中的 **ol** 元素的引用
6.  将 **ol** 元素的 **innerHTML** 设置为生成的字符串

首先创建一个名为 displayPokemon 的函数，它带有一个名为 **pokemon** 的参数。

```
const displayPokemon = pokemon => {}; 
```

从 fetchPokemon 内部调用新函数。确保将 **pokemon** 变量作为参数传递。

```
Promise.all(promises).then(results => {
  const pokemon = results.map(data => ({
    name: data.name,
    id: data.id,
    image: data.sprites["front_default"],
    type: data.types.map(type => type.type.name).join(", "),
  }));
  displayPokemon(pokemon);
}); 
```

现在，我们将使用 **map()** 和 **join()** 生成 HTML 字符串。因为我们使用 [ES6 模板文字字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)，我们可以做一些有用的事情。我们可以让字符串跨越多行并保持正确的格式。我们也可以使用字符串插值将变量注入字符串。最后，请注意，我们将类应用于我们创建的元素。我们将在下一节中使用这些类来添加样式。

```
const pokemonHTMLString = pokemon
  .map(
    pokeman =>
      `
    <li class="card">
        <img class="card-image" src="${pokeman.image}"/>
        <h2 class="card-title">${pokeman.id}. ${pokeman.name}</h2>
        <p class="card-subtitle">Type: ${pokeman.type}</p>
    </li>
    `
  )
  .join(""); 
```

在文件的顶部，您需要用 JavaScript 获取一个对 id 为 **pokedex** 的 **ol** 元素的引用。

```
const pokedex = document.getElementById("pokedex"); 
```

现在，将 **ol** 元素的 innerHTML 设置为我们生成的 HTML 字符串。

```
pokedex.innerHTML = pokemonHTMLString; 
```

下面是整个函数的样子。

```
const displayPokemon = pokemon => {
  console.log(pokemon);
  const pokemonHTMLString = pokemon
    .map(
      pokeman =>
        `
    <li class="card">
        <img class="card-image" src="${pokeman.image}"/>
        <h2 class="card-title">${pokeman.id}. ${pokeman.name}</h2>
        <p class="card-subtitle">Type: ${pokeman.type}</p>
    </li>
    `
    )
    .join("");
  pokedex.innerHTML = pokemonHTMLString;
}; 
```

## 风格口袋妖怪卡片

所有的口袋妖怪数据现在都在显示，但是不好看！让我们进入 css 开始添加一些样式。

先给正文添加一些基本样式，主要是字体和背景色。

```
body {
  background-color: orangered;
  margin: 0;
  font-family: rubik;
  color: white;
} 
```

对于字体，我指的是一种叫做 **Rubik** 的谷歌字体。如果您也想使用它，您需要将下面一行添加到 HTML 文件的 head 部分。

```
<link
  href="https://fonts.googleapis.com/css?family=Rubik&display=swap"
  rel="stylesheet"
/> 
```

接下来，我们可以向内容容器添加一些样式。这会给内容一些喘息的空间。

```
.container {
  padding: 40px;
  margin: 0 auto;
} 
```

我们还将添加一个应用程序的标题有点风格。

```
h1 {
  text-transform: uppercase;
  text-align: center;
  font-size: 54px;
} 
```

然后，我们为 Pokedex“卡片”添加基本样式。

```
.card {
  list-style: none;
  padding: 40px;
  background-color: #f4f4f4;
  color: #222;
  text-align: center;
}

.card-title {
  text-transform: uppercase;
  font-size: 32px;
  font-weight: normal;
  margin-bottom: 0;
}

.card-subtitle {
  font-weight: lighter;
  color: #666;
  margin-top: 5px;
}

.card-image {
  height: 180px;
} 
```

这些开始看起来好一点了，但是我们希望能够将网格显示为适应屏幕大小的网格。为此，我们将使用 [CSS 网格](https://www.w3schools.com/css/css_grid.asp)。我们不会在这里深入 CSS 网格的细节。。如果您正在寻找其他资料，我推荐这两个资源。

1.  来自 Wes Bos 的 [CSS 网格](https://cssgrid.io)
2.  布莱恩·罗宾逊的实用 CSS 网格

通常，我们希望水平显示卡片，每张卡片的最小宽度为 320 像素。如果宽度对屏幕来说太大，卡片应该换到下一行。我们可以通过以下方式实现这一点。

```
#pokedex {
  padding-inline-start: 0;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  grid-gap: 20px;
} 
```

## 悬停时动画口袋妖怪卡

在本教程中，我们要做的最后一件事就是当用户停留在口袋妖怪上时，给它添加一个动画。当有人滚动浏览时，这增加了互动的乐趣。

让我们将动画应用到悬停的卡片上。

```
.card:hover {
  animation: bounce 0.5s linear;
} 
```

现在，我们需要创建动画。这将是一个简单的反弹效果。这个动画将卡片上下移动两次。

```
@keyframes bounce {
  20% {
    transform: translateY(-6px);
  }
  40% {
    transform: translateY(0px);
  }
  60% {
    transform: translateY(-2px);
  }
  80% {
    transform: translateY(-0px);
  }
} 
```

## 总结起来

就是这样。您应该有一个功能正常的 Pokedex，它是用普通的 HTML、CSS 和 JavaScript 创建的。看，你并不总是需要一个框架来创造一些有趣的东西！