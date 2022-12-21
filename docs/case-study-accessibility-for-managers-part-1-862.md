# 案例研究:面向经理的可访问性报告-第 1 部分

> 原文：<https://dev.to/chinchang/case-study-accessibility-for-managers-part-1-862>

这是一个关于我为什么以及如何创建我最近的副业- [经理](https://a11yformanagers.now.sh/)可及性报告的案例研究。第 1 部分讲述了它背后的想法以及前端是如何创建的。

易访问性是任何产品的一个非常重要的方面。如果你的产品不是每个潜在用户都能接触到的，那么你就失去了潜在用户。在过去的几年里，我越来越关心我所做的每一项工作中的可访问性，不仅仅是 web 开发，而是一切。随之而来的是失望，看到所有这些网站，甚至打破了基本的无障碍规则。

缺乏知识当然是网站易访问性差的原因。但这并不总是仅仅是开发人员缺乏知识。我觉得有些时候，当开发人员慢慢学习新的东西时，他们确实想抽出时间来提高他们工作中的可访问性，但他们并不总是被给予这样做的自由。同样，这是缺乏知识和同情心，但在管理这些开发人员的不同的一套人。这就是为什么易访问性，就此而言，网站的任何部分，不仅仅是开发者的工作。它必须是一种公司价值观，从创意概念化的早期阶段开始，到设计、开发、产品管理、营销等等。

## “管理者无障碍报告”背后的理念

我们从试图说服人们关注可访问性的人那里听到的最常见的论点是:

> 嘿，我们真的有这样的用户吗？有人抱怨过我们的网站无法访问吗？
> -经理

这给了我一个想法——为什么不做一个给这些人他们想要的东西的应用程序——来自实际用户的真实反馈。但是后来我想，这里的主要目的是“真实的反馈”，而不是“实际的用户”😉这就产生了“管理人员可访问性报告”的整个概念——你可以在网站上输入任何网站的 URL，然后生成一份报告，报告中充满了虚拟用户对该网站实际可访问性问题的虚拟反馈🙂你把这份报告交给你的经理，他一直在征求真正的客户反馈，瞧！

免责声明:这里的想法并不是要贬低实际的易访问性测试和真实用户提交的反馈的重要性。这是最重要的。这个应用程序只是一个有趣的尝试，从不同的角度来看待真正的可访问性问题。

## 真实值

好吧，先不说有趣的部分。**面向经理的可访问性报告**不仅仅是一个有趣的不切实际的网站来产生这些虚假的反馈。我意识到它有更实际的用途。这个应用程序基本上给你一个非常不同的，以客户为导向的，关于你的网站的可访问性问题的观点。将此与阅读故事和在电影中实际看到/听到相同的故事进行比较。类似地，一个由 linter 生成的问题的文本列表不会在读者中产生同样多的共鸣，这可能是一个失望的用户的推文显示了网站上坏掉的东西的截图。

例如，比较这两个:

**棉绒**:颜色对比度不够。应该至少是 4.5:1。

**用户反馈**:嗨，网站管理员，我在浏览你的网站以完成一些工作，但在某些地方，我真的很难阅读文字，可能是由于颜色对比不佳。

希望你能看到两者的巨大区别。我觉得后者对同一问题给出了一个非常容易理解的观点。

## TL；DR:创建应用程序

这个应用程序虽然看起来很小，但在幕后有相当多的组件。最棒的是，作为一名前端开发人员，我可以用 JavaScript 编写所有程序，并且不用实际设置或管理服务器就可以运行。这些事情真的让我很兴奋，这个系列文章试图与所有最新的技术分享创建这样一个应用程序的整个过程。让我们从应用程序中使用的技术栈开始。

注:app 在 Github 上[开源。](https://github.com/chinchang/a11y-for-managers/)

### 组件 1 -前端

我选择了 [Preact](https://preactjs.com/) 来写这个应用的前端。Preact 是一个极其轻量级(只有 3KB gzipped)的替代方案，可以与几乎相同的 API 进行反应。它非常适合这样的场景:你有一个这么小的应用程序，而你又不想让它被某个框架占用太多。

我用的是 Preact 的`preact-cli`，它为 Preact 渐进式网络应用做了一个基本的搭建。除了 Preact，对前端没有其他依赖。

CSS 是以传统的方式编写的——一个 CSS 文件。

### 组件 2 -后端

后端基本上是一组无服务器的功能，为前端创建 2 个端点。还是那句话，都是用 JavaScript 写的(NodeJS)。

1.  有一个 API 端点可以获取给定页面上的实际问题，它使用 [pa11y](https://github.com/pa11y/pa11y) 包提取所有问题。
2.  第二个 API 端点是获取受影响区域的截图。这是通过使用[木偶师](https://pptr.dev/)获得页面上特定元素的截图来完成的。

### 组件 3 -部署

前端和后端现在都托管在 Zeit 上。它只需要为您的组件编写部署配置，只需一个命令就可以部署所有组件，而无需管理任何服务器！

## 前端

**注意**:在我们到达后端之前，假设我们有一个后端 API。我们可以向 API 发送任何 URL，它会返回一个可访问性问题列表。

### 框架

我喜欢反应。但是如果我正在做的项目在特性方面要求不高，并且只有我一个人在做，我更喜欢使用 [Preact](https://preactjs.com/) -这是一个 3KB 的替代 react。这几乎就像是我在运行一个普通的 JS 应用程序，同时获得了与 React 相同的易用性。

Preact 也有一个 CLI 来搭建一个基本的渐进式应用程序。

### 制造虚假推文-内容

正如我提到的，我们的 API 给了我们一个可访问性问题的列表(通过 [pa11y](https://github.com/pa11y/pa11y) 找到)。首先，我们需要把这些变成推文。清单中每个问题的结构如下:

```
 {
      code: "WCAG2AA.Principle2.Guideline2_4.2_4_1.H64.1",
      message: "Iframe element requires a non-empty title attribute that identifies the frame.",
      type: "error",
      selector: "#77c8c1c1-74e9-4d61-be2e-c1e39ffe02a0"
    } 
```

`code`是未被遵循的特定可访问性准则的唯一标识符，在`message`值中简要解释。这个应用程序的主旨是将技术指南转化为来自真实用户的看似真实的反馈。这就是为什么我需要把它转换成 tweet。同样的推文总是针对一个特定的问题类型，看起来会不真实和无聊。因此，我为每种问题类型维护 3-4 条不同的推文，并随机挑选一条。像这样:

```
const supportedRules = {
  INSUFFICIENT_CONTRAST: "WCAG2AA.Principle1.Guideline1_4.1_4_3.G18.Fail",
  MISSING_LABEL_ON_FORM_CONTROL: "WCAG2AA.Principle1.Guideline1_3.1_3_1.F68"
};

function generateFeedback(issue, url) {
  switch (issue.code) {
    case supportedRules.MISSING_LABEL_ON_FORM_CONTROL: {
      return {
        message: [
          `Dear Web Developer, I cannot see and browse the web using a screen reader. And websites like ${url} on which you don't label your form elements are completely unusable to people like me. #fixtheweb #a11y`,
          `Oh God! It's a disaster browsing websites where the developer simply didn't care to put a label on form controls!! Encountered one such site -> ${url} #accessibility.`
        ][random(0, 2)]
      };
    }
    case supportedRules.INSUFFICIENT_CONTRAST: {
      return {
        message: [
          `Hello! I was browsing through your website ${url} and facing issues with reading few texts. I guess it could get a lil' better for people like me if the text color had some more contrast with the background.`,
          `It's such a difficult task to read websites these days. Eg. Missing text contrast here -> ${url} Someone please fix the web. #accessibility`
        ][random(0, 2)]
      };
    }
  }
} 
```

这里发生的事情是，我为支持的可访问性指南代码做了枚举，以便在引用它们时更加清晰。然后是`generateFeedback`函数，它对可访问性问题`code`执行`switch`操作，并从与该问题对应的反馈列表中返回一个随机反馈。

这里还要注意的一点是，我将生成报告的网站的 URL 传递给了`generateFeedback`函数。这个传递的参数用于反馈字符串中的`${url}`占位符，实际上是[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。

### 制造虚假推文——虚假用户

这个有意思！接下来，我们需要一些假用户显示在我们的反馈推文中。我搜索了很多能给我随机用户数据的服务，最后选定了 [https://randomuser.me](https://randomuser.me/) 。这是免费的，快速的，并给了我想要的数据(如姓名，照片等)。它有一个带有各种选项的 API，但是我们感兴趣的是`results`选项。您可以在`results`参数中传递一个计数，API 会将这个数量的假用户返回给您。甜蜜的权利！

因此，到目前为止，整个报告生成过程如下:

1.  我从 API 中得到一个问题列表
2.  问题列表被转换成虚假的反馈推文(仅支持的问题)
3.  根据我们生成的反馈数量，我们从 [https://randomuser.me](https://randomuser.me/) 中获取这些虚假用户的数量

### 创建虚假推文——推文句柄

为了完成我们的假反馈推文，我们需要显示我们的假用户的 twitter 句柄(用户名)。现在我们可以用数字和字母创建随机的字符串，但这看起来又不真实了。制作不真实的东西有什么乐趣！所以我现在做的是:

1.  对于每个用户，我们都有用户的全名。
2.  我提取用户名字的一部分( **F** )和他们姓氏的一部分( **L** )。
3.  然后以 50%的概率，我用下划线将 F 和 T2【L】连接起来。另外 50%的时候，我直接把它们连接起来。因为人们一般会联系自己的名和姓来创建句柄。
4.  接下来，以 30%的概率，我在第 3 步的结果后追加一些数字(最多 3 个)。为什么？因为并不是 Twitter 上的每个人都可以选择自己的手柄。他们附加随机数以使其唯一:)
5.  结果被附加到一个“@”字符。

我们已经准备好把手了！如果你想知道我们如何以 X%的概率做某事，通过检查`Math.random()`的结果可以很容易地做到。

例如:要运行一个概率为 70%的代码，我们可以使用`Math.random() > 0.3`。这个条件意味着我们希望由`Math.random()`返回的数字大于 0.3。并且`Math.random()`从 0 返回到 1。所以基本上我们检查返回的数字应该在 0.3 和 0.1 之间。这意味着有 70%的机会:

0 - 0.3 → 30%的可能结果

0.3 - 1 → 70%的可能结果

下面是完整函数的样子:

```
function getHandleFromName(fname, lname) {
  // Concatenating extracts from first and last name, with _ maybe?
  let handle = [
    `${fname.substr(0, random(1, fname.length - 1))}`,
    Math.random() > 0.5 ? "_" : "",
    `${lname.substr(0, random(1, lname.length - 1))}`
  ].join("");

  // Add some digits?
  if (Math.random() > 0.7) {
    handle = `${handle}${random(0, 999)}`;
  }

  // Append to @
  return `@${handle}`;
} 
```

### 创建虚假推文-最终项目

对于上述所有数据，我们添加了随机的“喜欢”计数和推文日期。推文显示的另一件事是网站上不符合可访问性准则的元素的图像。还记得我们从 API 得到的问题结构吗？它有一个名为`selector`的键，是对应元素的 CSS 选择器。我创建了另一个 API，将 CSS 选择器和网站 URL 传递给它，它返回给我该元素的截图。稍后会详细介绍。

结合所有的虚假数据，我们生成了这个美丽的虚假反馈推文:

<figure>[![](img/543cc0d520d565069c1696a39c6e61dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdLxbNV3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kushagra.dimg/2019/a11yformanagers-tweet.png) 

<figcaption>某假用户抱怨色差用户的推文截图</figcaption>

</figure>

这就是本文的全部内容。在下一篇文章的[中，我将介绍这个应用的后端以及它是如何部署的。](https://kushagra.dev/blog/case-study-accessibility-report-for-managers-p2/)