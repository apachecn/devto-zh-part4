# 改编客户端 JavaScript 以用于 11ty(十一)数据文件

> 原文：<https://dev.to/brob/adapt-client-side-javascript-for-use-in-11ty-eleventy-data-files-5d99>

我的好朋友 James Q. Quick 最近写了一篇很棒的文章，描述了在没有框架的情况下与 API 的交互。他使用了神奇宝贝 API [PokeAPI](https://pokeapi.co/) 。他钩住了我，我不得不把我自己的旋转！

在我们进一步讨论之前，你需要去他的网站[看看原始帖子](https://www.jamesqquick.com/blog/build-a-pokedex-with-vanilla-javascript)！我不会详细描述他做了什么，但有必要了解这篇文章中发生了什么。

您甚至可以在这里观看嵌入的视频！去吧，我等着。

[https://www.youtube.com/embed/T-VQUKeSU1w](https://www.youtube.com/embed/T-VQUKeSU1w)

## 我的挑战:把 James 的客户端 JS 转换成构建 HTML 的构建过程

我喜欢 JavaScript，但我更喜欢渲染 HTML，所以我挑战自己，将 James 的客户端 JS 代码转换成渲染 HTML 的代码。我想尽可能快而简洁地完成它。

在这种情况下，我做了我通常会做的事情，并使用了我最喜欢的静态站点工具: [11ty](https://11ty.io) 。11ty 带来了 [JavaScript 数据文件](https://bryanlrobinson.com/blog/using-eleventys-javascript-data-files/)和简单的模板。我想过保持 James 文章的精神，并尝试去摆脱框架，但是我不是一个好的开发人员。

## 第 0 步:设置 11ty 依赖关系

因为我们将使用 11ty，所以我们需要创建我们的`package.json`并将 11ty 安装到我们的项目中。

运行以下命令进行设置:

用最基本的开始你的`package.json`。

```
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

然后安装 11ty:

```
npm install --save @11ty/eleventy 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装完成，你应该可以运行`npx eleventy --serve`，11ty 将开始为你的网站服务。

## 第一步:只获取必要的功能

在 James 的代码中，他有两个主要的 JavaScript 函数:`fetchPokemon()`和`displayPokemon()`。我们的显示问题将由我们的模板来处理，所以我们可以去掉那个函数。

`fetchPokemon()`函数的主要功能是创建一个承诺数组，并在所有承诺完成时构建一个数据数组。他运行这些来获得每个口袋妖怪的附加数据，而不是 API 通常给出的基本细节。在他构建了数据数组之后，他调用他的`displayPokemon`函数来处理 HTML 的构建。

在我们的例子中，这个 JavaScript 只需要将数组返回到 11ty。

下面是修改后的代码:

```
const fetchPokemon = () => {
    const promises = [];
    for (let i = 1; i <= 150; i++) {
        const url = `https://pokeapi.co/api/v2/pokemon/${i}`;
        promises.push(fetch(url).then((res) => res.json()));
    }
    return Promise.all(promises).then((results) => {
        const pokemon = results.map((result) => ({
            name: result.name,
            image: result.sprites['front_default'],
            type: result.types.map((type) => type.type.name).join(', '),
            id: result.id
        }));
        return pokemon;
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

在我们项目的文件结构中，我们需要一个文件夹来包含我们的数据文件。我保留了这个基本配置，所以我们将我们的文件夹命名为`_data`，这是 11ty 的默认值。

我们将把数据提取器文件放在这里。无论你给这个文件取什么名字，它都是你在模板中得到的变量。在这种情况下，我将我的命名为`pokemon.js`。把我们的函数放在这个文件的顶部。11ty 的数据文件是节点模块，所以需要导出我们的数据。

```
module.exports = async function() {
    return await fetchPokemon();
} 
```

Enter fullscreen mode Exit fullscreen mode

出口中的那个`await`很重要。否则，11ty 将在数据返回之前完成站点的构建。

明眼人可能会注意到，James 正在使用一个强大的新浏览器 API 来获取数据:Fetch API！这是一个令人惊叹的 API，但我们在构建过程中没有浏览器。在我们的情况下，这将打破。我们需要导入一个包来解决这个问题。您可以将这段代码转换到 Node 的本机`https`模块，但是我将这段代码放在一起，并尽可能少地重写代码。我将选择使用可爱的`node-fetch`模块，它有相似的语法和风格。

安装软件包:

```
npm install --save node-fetch 
```

Enter fullscreen mode Exit fullscreen mode

需要文件顶部的包:

```
const fetch = require('node-fetch'); 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，我们的数据文件应该在 11ty 建设或服务网站时工作。

## 步骤 2:修改显示，使用 HTML 模板代替 JavaScript 函数

接下来，我们需要添加一个使用这个变量的基本 HTML 模板。

出于我的目的，我公然借用了詹姆斯的 HTML 和 CSS，并围绕他的`<li>`为每个口袋妖怪设计了一个模板循环。

```
<div class="container">
    <h1>Robinson's Pokedex</h1>
    <ul id="pokedex">
        {% for pokemon in pokemon %}
            <li class="card">
                <img class="card-image" src="{{ pokemon.image }}"/>
                <h2 class="card-title">{{ pokemon.id }}. {{ pokemon.name }}</h2>
                <p class="card-subtitle">Type: {{ pokemon.type }}</p>
            </li>
        {% endfor %}
    </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:添加一些样式

默认情况下，11ty 不会通过您的静态资产。在这种情况下，我们的`style.css`文件。

我们需要添加一个最小的配置来解决这个问题。

我们将在项目的根目录下创建一个`.eleventy.js`文件。

在该文件中，我们将导出一行，设置 11ty 将任何根级 CSS 传递到我们的最终分发目录。

```
module.exports = function(eleventyConfig) {  
   eleventyConfig.addPassthroughCopy("*.css");  
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 就是这样！

如果您一直在运行 11ty 服务器，您可能需要重新启动它。当它重新启动时，你将有一个功能，詹姆斯的 Pokedex 的“服务器渲染”版本！推上 GitHub，托管在 Netlify 上拍拍自己的背！

你可以在这里找到 11ty 代码[，在这里](https://github.com/brob/pokedex-11ty)找到詹姆斯最初的帖子[。](https://www.jamesqquick.com/blog/build-a-pokedex-with-vanilla-javascript)