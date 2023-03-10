# Svelte 是我见过的最漂亮的网络框架

> 原文：<https://dev.to/jesseskinner/svelte-is-the-most-beautiful-web-framework-i-ve-ever-seen-325f>

我第一次听说 Svelte 是在一年前，当时 Rich Harris [在 JSConf EU 2018](https://www.youtube.com/watch?v=qqt6YxAZoOc) 上展示了它。演示神对他有点苛刻，但对我来说没关系，因为他的哲学和思想给我留下了深刻的印象，我已经被说服了。我知道他会解决问题，经历几个主要版本，Svelte 会很快变得足够成熟。

[https://www.youtube.com/embed/qqt6YxAZoOc](https://www.youtube.com/embed/qqt6YxAZoOc)

从那以后，我有点忘记了苗条，直到上周我读了 Rich Harris 的博客文章[为什么我不使用 web 组件](https://dev.to/richharris/why-i-don-t-use-web-components-2cia)。它提醒了我 Svelte 的语法是多么简单和美丽，我决定是时候认真考虑一下了。

[![richharris image](img/8fa7da49ec2b0f3735bf6415dc51aa22.png)](/richharris) [## 为什么我不使用 web 组件

### 里奇哈里斯 6 月 20 日 197 分钟阅读

#html #webcomponents](/richharris/why-i-don-t-use-web-components-2cia)

首先，我玩了一会儿苗条的 REPL，了解了它的工作原理。然后我决定[尝试在 Twitch 和 YouTube 上做一个井字游戏。尽管我对苗条身材一无所知，也几乎没有看过相关的文件，但我只用了半个小时就让井字游戏开始运行了。之后，我探索了一些不同的苗条特征，试图将游戏状态移到非苗条模块中，并在这个过程中发现了一些反模式。](https://www.youtube.com/watch?v=S_6ApOagzzM)

[https://www.youtube.com/embed/S_6ApOagzzM](https://www.youtube.com/embed/S_6ApOagzzM)

最后，当我发现产品版本只有 2，418 字节的 JavaScript 时，我完全惊呆了..！这是 2.4kb，包括苗条的运行时间！！！

Svelte 是怎么做到的？因为 Svelte 是一个编译器。它只包含完成工作所需的最少的 JavaScript 代码。它将您编写的 HTML 模板转换成极其简单的 DOM 脚本。它传输您编写的 JavaScript，以便您的简单变量赋值触发重新呈现。它生成 JavaScript 类来表示您的`.svelte`文件，并为您连接一切，因此您真正需要的唯一样板文件是一个`<script>`标签和一个`<style>`标签。

如果你有兴趣看我做的井字游戏，你可以[在 GitHub](https://github.com/jesseskinner/svelte-tic-tac-toe) 上克隆它，用`npm install`和`npm start`旋转它。

否则，我强烈推荐你查看官方的[苗条教程](https://svelte.dev/tutorial/basics)，亲自尝试一下！

* * *

对苗条和网页开发感兴趣？[订阅《与杰西一起编码》时事通讯！](https://www.codingwithjesse.com/newsletter)