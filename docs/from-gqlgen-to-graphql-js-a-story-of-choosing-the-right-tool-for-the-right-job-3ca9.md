# 从 gqlgen 到 GraphQL.js:“为正确的工作选择正确的工具”的故事

> 原文：<https://dev.to/louis993546/from-gqlgen-to-graphql-js-a-story-of-choosing-the-right-tool-for-the-right-job-3ca9>

<figure>[![](img/edea7d329071a798bf670e689a075d2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WFXCEhNJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHKerLy4MDPdmIqvfbLovKA.jpeg) 

<figcaption>[哦这个？与内容无关，我昨天刚拍的，我很喜欢 _(ツ)_/](https://unsplash.com/photos/C8V9gKjMr_s)</figcaption>

</figure>

几个月前，我简单地谈过[使用 gqlgen 为一个现在](https://medium.com/@louis993546/loft-choosing-graphql-instead-of-rest-api-w-the-help-of-gqlgen-14c8643ee9a1)[“推迟”的项目](https://dev.to/louis993546/loft-i-ll-be-back-3ha5-temp-slug-6235093)创建一个后端。在那之后，我一直试图为官方的 HackerNews REST API 创建一个 GraphQL 包装器。

*   [louistsaitszho/hacker news-graph QL](https://github.com/louistsaitszho/HackerNews-GraphQL)
*   [黑客新闻/API](https://github.com/HackerNews/API)

大约一周前，我终于说“去他的”，删除所有内容，并从 GraphQL.js 开始

*   [99designs/gqlgen](https://github.com/99designs/gqlgen)
*   [graphql/graphql-js](https://github.com/graphql/graphql-js)

那么是哪里出了问题呢？是因为围棋吗？是因为 gqlgen 吗？还是有什么事/什么人该怪？让我们来了解一下！

所以，这个项目的前提很简单:REST API 有点奇怪，因为它(几乎)只返回 ID。客户将不得不发出十亿个额外的请求，以获得足够的数据显示在屏幕上。这可能不是一件好事，尤其是对于移动设备来说，所以让我们使用 GraphQL 作为代理，并将灵活性暴露给客户端。(大概)强大的服务器完成了大部分繁重的工作，甚至可能在某个时候添加一些聪明的缓存机制。

所以我和 Go 和 gqlgen 一起开始了这个项目，主要是因为三个原因:

*   我想练习我的围棋技巧
*   我以前用过 gqlgen
*   “类型安全+代码生成”的前提似乎非常适合 GraphQL

我仍然记得我当初选择它的所有原因。而且在开始的时候，用不了多久就能拿出一个超级简单的“只有快乐路径+没有效率”的模式+解析器+查询。但是当我开始深入一点的时候，事情开始变得有点复杂。以下是我必须要解决的一系列问题，而我对 Go & gqlgen 的了解非常有限:

*   如果查询只要求 id，它不应该调用任何额外的 API 调用。这意味着我需要告诉 gqlgen 使用 resolver 来获取基本上每一种类型中的每一个字段。它变得非常冗长非常快。
*   item (story，comment，job listing，poll，poll option)的 ID 是一个整数，而 user 的 ID 是一个字符串(就像 twitter 的 handler)，但是我不能教 gqlgen 同时将 GraphQL 的 ID 类型解释为两种不同的类型。
*   我花了很长时间才意识到，我至少应该把解析器函数放到另一个文件中，这样当我必须更新模式并重新生成 resolver.go 时，我就不必费力地把逻辑放回-_-
*   从 dep 更新到 mod。没什么大问题，只是我必须处理一些事情。
*   等等。

大约两个月后，我真的开始觉得我在每个级别上都在和围棋和围棋对决。我只想把 GraphQL 里的一个黑客新闻 API 搞定。一定有更好的方法。

<figure>[![](img/62d91c6a5f10e5c89d9054849056dde7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j-dXlBsv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/281/1%2AXHhbQ21lLyLw3i2i6qeNEw.gif)

<figcaption>*2 个月后我的大脑*</figcaption>

</figure>

由于我不控制黑客新闻，也不控制它的 API，我将不得不调整我如何处理这个问题。在那段时间里，我重新考虑了以下事情:

*   类型安全:也许对于这个项目来说，它不那么重要？输出肯定需要与 GraphQL 模式指定的内容相匹配，但内部并不一定要匹配。事实上，没有类型安全也有一些好处，这样就减少了类型强制转换/检查/转换。
*   框架:用 codegen+10 亿行代码来配置模式优先可能不是这个项目所需要的。只需采用典型的旋变器优先方法。
*   开发人员工效学:我仍然非常熟悉 Java-esq-languages-land，试图找出指针，基本上没有 OOP，不知道如何进行基本的并行，这意味着我可能会花更多的时间走错路，走进死胡同，然后回头意识到什么是正确的方法。不要误解我，学习很好，但感觉自己一事无成并不好。

[![](img/8a98a3262095eea93245ffecaec97c27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h1_jyfTV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/498/1%2AsigTjKGJtjmJ0pVJLoqLFg.gif)

因此，在考虑了以上所有因素后，我决定尝试一下 GraphQL.js。快进到今天，我已经用 Go + gqlgen 构建了比旧版本更多的解析器，所以我认为可以称之为成功(目前)。

别误会，**我还是很喜欢 Go 和 gqlgen** 。只是在这个非常特殊的情况下，**它对我和这个项目来说都不是合适的工具**。而另一方面，Node.js + JavaScript + GraphQL.js 还有很多我不喜欢的地方，但大部分都是我可以处理的权衡(目前来说)。

那么这里的外卖是什么呢？

*   为正确的工作使用正确的工具。
*   如果你有充分的理由，不要害怕做一些实验，甚至重新开始你正在做的事情。也许真的会有回报。
*   工程师是社会的未来，你必须学会如何发现并留住他们。 [/s](https://www.urbandictionary.com/define.php?term=%2Fs)

如果你对 Go/gqlgen/node . js/JavaScript/Hacker News/this project/graph QL/任何东西感兴趣，欢迎留言或联系我🤗