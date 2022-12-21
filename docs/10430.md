# 苗条:第一印象

> 原文：<https://dev.to/eostrom/svelte-first-impressions-45ga>

*【交叉发布自[我的网站](https://www.erikostrom.com/code/words/svelte-first-impressions/)。]*

Svelte 是“构建快速网络应用的工具”下面是跑完【优秀】[教程](https://svelte.dev/tutorial/basics)后的几个
的想法。我只想说一句:如果看起来我做错了什么，我可能就是错的！我所做的就是看了教程。

* * *

关于苗条，每个人告诉你的第一件事是...你猜怎么着让我们跳过那个。你已经听过了，或者如果你还没听过，我以后再告诉你。

我对苗条时的反应感到兴奋。这里有一些来自教程 :
的[代码](https://svelte.dev/tutorial/reactive-declarations)

```
<script>
  let count = 0;
  $: doubled = count * 2;

  function handleClick() {
    count += 1;
  }
</script>

<button on:click={handleClick}>
  Clicked {count}
  {count === 1 ? 'time' : 'times'}
</button> 

<p>{count} doubled is {doubled}</p> 
```

`count`被声明为瘦组件中的变量。这就是让它起反应的原因。现在，当它在`handleClick`、
中增加时，按钮文本会自动更新。

`$`标签表示*反应声明。* `doubled`不仅仅是*初始化*，而且*将*定义为`count * 2`——这意味着每当`count`发生变化时，`doubled`都会被重新计算。当`doubled`改变时，按钮下方的段落也会自动更新。

所有的现代框架都有这方面的一些版本，差别是微妙的。我能说的就是这让我感觉很好。

* * *

*(顺便问一下，Svelte 是如何摆脱感觉如此简单的反应的？因为它不仅仅是“JavaScript”上面的代码看起来有点像嵌入了脚本的 HTML，从语法上来说，该脚本是有效的 JavaScript。但是语义上是不同的——变量赋值触发代码执行！–这是因为 Svelte 不是一个框架，而是一个编译器。但我们会谈到这一点。)*

* * *

正如组件内部的反应感觉简单一样，组件外部的数据管理也是如此。这是教程 :
的另一个[样本](https://svelte.dev/tutorial/custom-stores)

```
function createCount() {
  const { subscribe, set, update }
    = writable(0);

  return {
    subscribe,
    increment: () => update(n => n + 1),
    decrement: () => update(n => n - 1),
    reset: () => set(0)
  };
} 
```

好吧，脱离上下文就没什么意义了。但是它创建了一个具有标准接口(订阅和取消订阅)和自定义接口(递增、递减、重置)的数据存储。

我不知道在复杂的应用程序中使用它是什么感觉，但在这个级别上，它非常有吸引力。

* * *

这只是一个独立的特性，而不是像反应和数据存储这样的基本概念。但是我喜欢苗条的模板有内置的承诺语法 :

```
{#await promise}
  <p>...waiting</p>
{:then number}
  <p>The number is {number}</p>
{:catch error}
  <p style="color: red">
    {error.message}
  </p>
{/await} 
```

这使得“加载”指标成为一个显而易见的事情，我经常把它推迟到以后，因为它们感觉像是乏味的样板文件。

此外，在构建
时做一些基本的可访问性检查还有加分。

* * *

其他人告诉你的关于苗条的第一件事是它是“编译的”大多数 web 开发框架由大量代码组成，每个用户的浏览器都必须下载并解析这些代码，这些代码反过来又必须解释应用程序代码，以计算出每时每刻应该在页面上放置什么。苗条不会那样做。相反，您使用 Svelte 将您的应用程序代码翻译成一小束非常高效的代码，这些代码已经知道它必须如何操作页面。

大家告诉你的第二件事是，因为是编译过的，Svelte 比大多数框架快很多。

对我来说，这些都不是苗条最令人兴奋的事情。原因有三:

*   虽然苗条交付了[令人印象深刻的小包裹，](https://www.freecodecamp.org/news/a-realworld-comparison-of-front-end-frameworks-with-benchmarks-2019-update-4be0d3c78075/#metric-2-size)那只是感知和实际[性能](https://www.freecodecamp.org/news/a-realworld-comparison-of-front-end-frameworks-with-benchmarks-2019-update-4be0d3c78075/#performance)的一个组成部分。
*   性能很重要，但不是唯一重要的。稳健很重要。开发者体验很重要。
*   我不知道，这不是我认为有趣的事。

需要说明的是，我并不是说 Svelte 的整体性能不好，或者不够健壮，或者开发者体验不好。(其实这个帖子大部分是在赞美开发者体验！)我只是说，“编译，因此表演”的故事本身并没有抓住我。

* * *

至少有两件事我很好奇:

*   调试是什么样的？是不是因为编译的 JavaScript 被进一步从源代码中移除而变得更难了？
*   测试是什么样的？(听起来这是一项正在进行的工作。)

总的来说，我对苗条印象深刻。我期待着用它来构建一些东西，我建议前端开发人员检查一下。