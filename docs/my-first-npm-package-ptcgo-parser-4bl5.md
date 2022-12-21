# 我的第一个 npm 包:ptcgo-parser

> 原文：<https://dev.to/hamatti/my-first-npm-package-ptcgo-parser-4bl5>

## 简介

在过去的几年里，我为自己和我的本地和在线社区的几个玩家制作了几个原型，以帮助我们成为更好的口袋妖怪 TCG 玩家。基于这些项目，我今天发布了我的第一个 npm 包。

我的一些原型是 [Proxymon](http://proxymon.herokuapp.com/) 现在已经过时了，丢失了最近的几套，但仍然可以打印 playtesting 和 [pkmn-attack-damage](https://pkmn-attack-damage.netlify.com/) 的代理，这是我在一个周六晚上在 Discord 社区讨论后建立的。

**遇见 [ptcgo-parser](https://npmjs.com/package/ptcgo-parser) ！**

我编写了 [hamatti/ptcgo-parser](https://github.com/hamatti/ptcgo-parser) 来学习如何构建 npm 包，并减轻我为所有项目复制粘贴和重写解析部分的痛苦。我是 [PokemonTCG.io API](https://pokemontcg.io) 的超级粉丝，并决定捆绑该 API 的兼容性。

## 快速指南

您可以使用
通过 npm 安装包

```
npm install ptcgo-parser 
```

然后在你自己的代码中使用它，提供一个口袋妖怪 TCG 在线行李箱盖导出作为它唯一公开的函数`parse` :
的参数

```
const PTCGOParser = require('ptcgo-parser')

const ptcgoExportedDecklist = `
****** Pokémon Trading Card Game Deck List ******

##Pokémon - 13

* 1 Oranguru SUM 113
* 3 Darkrai-EX BKP 74
* 4 Dratini SUM 94
* 3 Dragonair SUM 95
* 1 Dragonite ROS 51
* 1 Mew FAC 29
`

const decklist = PTCGOParser.parse(ptcgoExportedDecklist)

console.log(decklist)

/*
  {
    cards: [
        {
            name: 'Oranguru',
            amount: 1,
            set: 'SUM',
            code: 113,
            ptcgoio: {
                id: 'sm1-113'
            }
        },
        ...
    ]
  }
*/ 
```

它将返回卡片的解析版本，但也包括 PokemonTCG.io ID，您可以使用它从 API 获取更多信息。

## 已知问题

最初的启动功能，但它仍然有一些已知的问题，主要是一些卡(如彩虹稀有)在 PokemonTCG.io API 中不可用。我会继续开发这个包，让它在 1.0.0 版本中包含缺失卡的例外情况，这样你就可以得到正确的卡，即使是错误的稀有卡。

如果你是一名开发者和口袋妖怪 TCG 玩家，请尝试一下，让我知道我如何改进它，使它完美地适合你的用例。

## 在 npm 中发布包

我很惊讶在 npm 中发布包的工作流程是如此简单。你所需要做的，就是[给 npmjs.com](https://www.npmjs.com/)创建一个账户，[创建一个`package.json`文件](https://docs.npmjs.com/files/package.json)，写一些代码，在你的命令行上运行`npm publish`。

使用 [Shields.io](https://shields.io) ，您可以获得这些酷炫的徽章，并将其添加到您的文档中:

[![npm badge](img/b5d196ee51971eeaf2a9340721f6b219.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A3D47-ba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/npm/v/ptcgo-parser.svg%3Fstyle%3Dplastic)

## 结论

这个库还有很多工作要做，特别是在丢失或不匹配卡片方面使它更加稳定。但是将它包装在一个 npm 包中迫使我考虑 API 和它返回数据的格式以及文档。

如果你想学习如何写好文档，@jamesstone 在他的知识库[中收集了大量的资源。](https://github.com/jamesstoneco/docsthursday)

*ptcgo 解析器的开发得到了 [Spice 计划](https://spiceprogram.org/)的支持。*