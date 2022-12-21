# 由开发人员 React(已停用)快速执行的实验[pt-BR]

> 原文：<https://dev.to/ninetails/experiencias-com-svelte-por-um-desenvolvedor-react-pt-br-4mj9>

> **古代文章**2019 年 8 月写的文章。当时，fast 不支持 TypeScript，并且尚不存在 fast kit。

这不是早期版本的速写，我想绝大多数人以前从未在意过，因为它看起来不够成熟。至少对我来说是这样。我也觉得我不是唯一一个在版本 3 之后开始对工具感兴趣的人。

<figure>

[![Uma tirinha em inglês onde um garoto aponta que "alguns caras" estão usando (aqui eu editei pro meu artigo) Svelte. No segundo quadrinho a mãe diz "Não olhe, você será influenciado por..." e no terceiro quadrinho, o garoto dizendo que já era tarde, com uma roupa mais hipster](img/62b3c672397a9e6d3da0286fe2bf1cbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zmJJ_p-Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szxxzaps6w7coqe9hi0a.png)

<figcaption>...e fiquei empolgada <sup><sub><sup><sub>fodeu</sub></sup></sub></sup></figcaption>

</figure>

为了定位你，我亲爱的读者，我亲爱的读者和我亲爱的单篇读者，我将介绍一些关于快的。因为我很少写文章，所以我不打算把它分成几部分(老实说，是空的)。我的经验集中在与市场工具和技术的快速集成上。假如你已经知道什么叫快，[跳到我所说的印象部分](#enfim)。

[![Imagem do jogo Super Mario Bros, onde o jogador pode pular mundos](img/dc20b5bfb0ca960bc34c6b87c15289d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVJ9HYZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.mariowiki.cimg/thumb/4/44/SMB_Warpzone.png/200px-SMB_Warpzone.png)

我还补充说，本人在本*帖子*以及其他帖子的内容上都重复了很多内容。尽管如此，如果你看到我过得很快的一个话题，那是因为他们一定有更深入的更好的消息来源，我会把重点放在这段时间里体验一下‘T2’框架。

## O queésvelite？

<figure>

[![Uma pessoa batendo com a cabeça na mesa](img/64c004b440ef18b26e9bc7ffc9ff7e7e.png)](https://i.giphy.com/media/9mWg0JLV4NwM8/giphy.gif)

<figcaption>**Oh noes!** Mais um framework...</figcaption>

</figure>

**fast**是一个声明式编写的组件框架，可将其组件转换为浏览器的本机代码。

> [以上是这里的摘要](https://svelte.dev/blog/svelte-3-rethinking-reactivity#What_is_Svelte)

当谈到声明式组件框架时，只需像当今最大的市场框架那样编写组件(React、vue、Angular)-我...。)并避免停留***【jquery】**【zando】*的全部应用，编写直接修改 DOM 的强制代码。

此工具的区别在于第二部分:

事实上，这种*框架*并不是像现在的*框架*那样重视*运行时*的*框架，相反，其优点在于*

 **<figure>

[![Bob Esponja abrindo os braços e sai um arco íris](img/b20f9aa2f85cc462db46e13b3f69bd89.png)](https://i.giphy.com/media/SKGo6OYe24EBG/giphy.gif)

<figcaption>ele compila seus componentes para código nativo do navegador</figcaption>

</figure>

编译器将把它所有的声明代码和结构完善的代码转换成必然改变‘t0’DOM’t1’的东西。告别*虚拟礼物*。它将会***【jquery】**【izer】*为您执行您的应用程序，同时您的代码将继续易于构建和维护。

<figure>

[![Uma imagem de um programa de televisão de vendas por telefone](img/d0e03b938c70abf764de13bbfd2e6736.png)](https://i.giphy.com/media/VF5doyjvpNfeFLIwjn/giphy.gif)

<figcaption>e não é só isso!</figcaption>

</figure>

它从一开始就被设计成产生反应性的应用程序。我非常推荐富哈里斯关于“思考反应”的演讲。

## 首先，语法！

我推荐这份荷兰 Claudio 写的完整文章:[快点:一个框架时代──汇编者](https://claudioholanda.ch/blog/svelte-a-era-dos-frameworks-compiladores/)。

## 从何说起呢？

官方网站有一个很好的工具，我们开发人员对此很熟悉: [REPL](https://svelte.dev/repl/) 。基本上是开发者的游乐场和社区(文章末尾的聊天链接)用来指出问题或错误的主要工具。不需要下载任何东西，在左边编程，在右边看到出口。就这么简单。

如果您希望更好地了解平台功能，基于“**repl〖t1〗还创建了一个与[教程](https://svelte.dev/tutorial/basics)和[有基本示例的画廊](https://svelte.dev/examples)交互的工具。**

## 快点，今天我要用它！

[![Foto do personagem Coronel Jesuíno, que tinha esta fala e que se tornou meme "'alguma coisa' vou lhe usar!"](img/a772ff3b8273e9f52479eb2154345f98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pxNNCsrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ytimg.com/vi/w7xQG1ujzuU/hqdefault.jpg)

据文献记载，另一个名为度的代码生成器工具**富哈里斯**也开发了，用于启动快速项目。基本上，使用此工具，您将传递一个公共存储库*【git】*，并将分支的最新版本下载并解压缩到您要编写代码的文件夹中。

为方便起见，我们提供了“模板”存储库，并允许您从两个选项中选择捆绑工具: **Webpack** 和 **rollup** 。

### 在当地迅速发展~~疯狂~~

要在本地快速启动一个应用程序 <abbr title="Single Page Application">SPA</abbr> ，只需下载模板:

```
# using rollup
npx degit sveltejs/template my-svelte-project
# using webpack
npx degit sveltejs/template-webpack my-svelte-project
cd my-svelte-project

npm install npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

> https://svelte.dev/

在运行`npm run dev`时，应用程序将以指向`http://localhost:5000/`的开发模式提升本地服务器。开发完成后，只需转动`npm run build`，其应用 <abbr title="Single Page Application">SPA</abbr> 即可使用在`public`文件夹内创建的工件进行生产。

### 乌桑多阿工兵

> 过时了。阅读关于**松开套件**

**Sapper** 是受 [Next.js](https://nextjs.org/) 启发的应用程序，是一个在社区中相当有名的框架 **React** 。但我非常喜欢的一项功能是静态内容生成，类似于[【Gatsby】](https://www.gatsbyjs.org/)(但更基本，没有图形 SQL 等)。

```
# for Rollup
npx degit "sveltejs/sapper-template#rollup" my-app
# for webpack
npx degit "sveltejs/sapper-template#webpack" my-app
cd my-app

npm install npm run dev & open http://localhost:3000 
```

Enter fullscreen mode Exit fullscreen mode

> 资料来源: [https://sapper .快点。dev/](https://sapper.svelte.dev/)

**一个重要的警告**:自动*重装*【hmr】)使用 Sapper 可能不能很好地工作，在开发过程中使用持续代码时需要不断地杀死并重新启动应用程序

运行`__sapper__/build`时，Sapper 会在`__sapper__/build`文件夹内生成节点应用程序。也可以使用 [express](https://expressjs.com/pt-br/) (模板中使用了名为 [Polka](https://github.com/lukeed/polka) 的较轻的服务器)。

对于*和[【jam stack】](https://jamstack.org/)的爱好者，如上所述，也可以产生静态内容。只要转动`npm run export`，他就会在`__sapper__/export`文件夹中创建用于生产的工件。*

 *## 蓬托斯烧结而成

不幸的是[没有 TypeScript](https://github.com/sveltejs/svelte/issues/1639) 的正式原生支援，但从另一个角度来看，可能会使这个工具对刚开始使用的人更友好-我...。

## 其他类似工具

> 你有别的选择吗？是的，先生！

### [蜡纸](https://stenciljs.com/)

fast 的理念[不是以生成 web 组件为主要焦点](https://dev.to/richharris/why-i-don-t-use-web-components-2cia)。但是，也可以按照[这个*问题*t8】本机创建*自定义元素*，并用作`web components`。](https://github.com/sveltejs/svelte/issues/797)

Stencil 由 Ionic 团队开发(即“回应毛发”项目)，并在 showcase 中拥有著名的公司。即使是有[手机(快-原生)](https://svelte-native.technology/)版本的快讯，我也会说两者都值得一看。使用 **TypeScript** 。

### 2022 年的一些替代品(编辑)

*   [固体](https://www.solidjs.com/)
*   [Qwik](https://qwik.builder.io/)

## *真正的莱弗*

虽然这是所有关于快速的布拉柴维尔文章的陈词滥调，但我在此重复一遍，其中最大的案例之一是斯通的绿色支付机器，使用快速(快速版本 2)。

[![Foto da palestra do Rich Harris falando sobre a Stone](img/545b8a6776fdd1ca5fa9af24b41aae00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gr1Fv6Yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pat10pn23h1g5m9wd8lx.png)

正如在[富哈里斯视频](https://youtu.be/AdNJ3fydeao?t=1498)中引用的那样

> [开源😍](https://github.com/stone-payments/pos-mamba-sdk)。
> 克雷迪斯:[@凯泽曼](https://github.com/kaisermann)

**但是我想在网上看到一个真实的案例！"**

忆及造物主曾在《纽约时报》**上工作过仅次于 fast(2022 年)，我在纽约时报工作时发现了一个的另一个用例，与 **d3 相集成它也是在早期版本的 fast 中开发的。****

 *** * *

## Enfim...

我来自反应堆。

*Fanboy* 真的，假定。

他总是有一个细节或者别的，一直激励着我不要从‘t0’框架换心。

我见过一些关于快餐的肤浅的东西，我觉得很有趣，但我总是被打，因为我没有听到太多关于使用。

过了一会儿，经过三个版本的*专业*之后，出现了很多很酷的案例，我想终于给一个机会，多了解一下。

### 和我从哪里开始呢？

老实说，当我开始学习一些新的东西，我觉得被吓得直截了当地进入文件，尽管她很好。教程和文章也并非总是完整或全面的，通常会遗漏一些东西。所以我选择了乌德米的一门课程(从 28 美元到 60 美元不等，当你升职的时候-我...。~~皮特凯恩的陈词滥调:引用马克西米利安·黑盒子(maximilian black fill)~~【快点。js - The Complete Guide(英文)所写的，我没有任何收获。】。

本课程虽然是基础/入门课程，但涵盖了 quick start 3(发布时的当前版本)所包含的许多主要功能，非常有教育意义。

### 再组装

与目前所有的<abbr title="User Interface">框架一样，其发展面向 <abbr title="User Interface">UI</abbr> 的构成。我发现创建组件的部分与视图. js 非常相似，如果您在一个文件中同时具有标记代码(*标记、*标记)、样式和 JavaScript。</abbr>

在 JavaScript 中，您可以直接写入 ES6。如上所述，尚不支持本机 TypeScript，但是-我...。

### 预处理程序

由于代码是快速编译的，所以可以非常简单的方式使用[预处理程序](https://www.npmjs.com/package/svelte-preprocess)。由于这是第三方工具(非官方模块，但维护者众多)，因此其集成可能不是最好的。

编写组件(如 TypeScript 和 coffescript)以供应用程序使用时，可以集成其他语言。Pug(古玉)用于*标记* (HTML)和 Postcss、SASS、Less 和 Stylus 用于风格化。很容易整合。

我用 TypeScript 玩了几个游戏，试着使用 Stylus，但在使用上没有太多问题，但是我还有其他问题(在文章末尾的一篇文章中添加了这些问题)-我...。

### 工具和集成

para 对集成工具的支持非常全面。

#### ESLint

加入本官方插件:[esint 插件-快捷 3](https://github.com/sveltejs/eslint-plugin-svelte3) 。就这么简单。

您的配置非常简单，因为它不是一个*预设*，所以请记住，激活规则必须在配置文件中的`rules`内单独完成。

#### 测试

对于来自 **React** 的人来说，你应该已经知道 Kent C 的`react-testing-library`。多德！多德！当该工具普及推广并催生其他[【测试库】](https://testing-library.com/)时，快捷并没有置身事外:[@ testing-library/speech](https://github.com/testing-library/svelte-testing-library)。

它要求 Jest 的变压器:jest 变换-快。

#### 漂亮些

这个代号"美化"也不能置身事外。只需添加[此插件](https://github.com/UnwrittenFun/prettier-plugin-svelte)，并按照文档中所述在售前设置中添加`override`。

#### 故事书

是的，可以有一部写得很快的成分的[故事书。](https://storybook.js.org/docs/guides/guide-svelte/)[你的支撑仍然基本](https://www.npmjs.com/package/@storybook/svelte#todos)。

#### 著名的“了解更多”

[点击此处](https://github.com/sveltejs/integrations)。

### E estilos？

我已经提出了一些特点:

#### 【全部】*【CSS】*写入组件文件将仅为该组件进行

 *编译时，fast 会更改选择器并添加类以避免样式冲突。并且此功能是*框架*的标准功能。

#### 不能在*标签* `<style>`内使用 JavaScript 变量插值。

但是，没有什么可以阻止使用[CSS 变量](https://developer.mozilla.org/pt-BR/docs/Web/CSS/Using_CSS_custom_properties):[leviano 示例](https://svelte.dev/repl/c28c244c8a414530acf291942585340f?version=3.6.7)传递这些值。当然，您也可以使用`style`属性来变更*内嵌*样式，但请记住，为了良好的做法-我...。

#### 速记员假定 *CSS* 必须无法触摸

也就是说，在*`<style>`标签内的东西，在将选择器转换成刷类后，是一个代码，准备提取到单独的文件中(`extract-text-webpack-plugin`，我指的是你。*

 *我甚至不知道是否可以使用 **CSS-in-JS** ，包括。但是，请记住“T2”前端“”根，在此，我们使用类来定义不同的样式，而不是在运行时更改它们。

### 但是-我...。没有*虚拟天赋*？

不可否认的是反应堆的*虚拟礼物*的模式是有效的(受我*狂热分子*影响的词语)，不要当真！)，但是，按照 fast 的理念，为使用并在操作上直接更改‘T6’DOM’T7 的应用程序编译代码，除添加‘T10’运行时‘t11’之外，拥有‘t8’虚拟 DOM’可能是多馀的

### 反应性

也引起我很大关注的是反应性地更新变量。在代码中，只需为 basta 添加一条具体说明:“”。

它是 JavaScript 语法的一部分，有效，甚至有一个特定的名称:[标签](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/label)。在 JavaScript 中，它用作循环和其它语言中的流控制，通常用作“`goto`”中的控制。

简而言之，每当在`$:`标志右侧使用变量、表达式或函数执行分配时，如果所用变量的值发生变化，则会重新计算或执行该分配。

### 店铺

与所有 UI 组件库一样，不能缺少状态管理器。而其固有的**商店是在本版本中开发出来的，已经考虑到了反应性，采用了开发人员所熟知的模式 **pub-sub** 。**

e 为简便起见，有一个*短手*(协助书写)，方便用户使用*商店*的数据，而不用担心 [*订阅*或*取消订阅*](https://svelte.dev/tutorial/auto-subscriptions)

#### 有效环

另一个我觉得很酷的地方是，即使直接处理天赋，相对于回圈，速度也会变得很快。当然，不幸的是，这不是自动的，因为快捷方式需要知道每个元素的关键点，但在“[”使用循环“](https://svelte.dev/docs#each)”时，您可以有效地工作，而无需进行修复。

### 并谈*短手】-我...。*

 *我很喜欢编写组件，对于最琐碎的任务，总是有一种较小的写作方式，例如一种‘t0’双向绑定；转发一个*事件*，无论是否个性化[；切换类别*CSS*；使用*【休息传播】*甚至-我...。](https://svelte.dev/docs#createEventDispatcher)

### 过渡与动画！

一件我没想到会来自敬酒的事。对于琐碎的动画、效果和过渡，fast 具有一个“T0”迷你库“”及其集合，并且易于实现。

也可以使用*的动画功能和*的个性化*功能，但是后来我的朋友:*

<figure>

[![Meme da Nazaré pensando e com as contas flutuando na tela](img/990511d0ccd041032325141e8f6e0eec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xO_3DoZ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://conteudo.imguol.com.br/c/entretenimento/5b/2016/10/14/nazare-tedesco-vila-de-senhora-do-destino-2004-vira-meme-mundial-1476485254931_1080x703.jpg)

<figcaption>...é muita matemática rs. Deixo esta parte pra galera que curte desenvolver jogos ou nunca faltou em uma aula de álgebra linear.</figcaption>

</figure>

### *特殊标记*

mais 还具有一些本地组件，可帮助您开发更具说明性的代码。[例如，获得视口宽度](https://svelte.dev/repl/c731dc73439f45c38448127485a4954a?version=3.6.8)是相当微不足道的。

在*特殊标记*中，有将*标记*改为*头部*等功能在鞋垫内，在组件本身中使用递归，使用动态组件-我...。

### 编者意见

编译器和语法的“*linters”*都会在组件未使用的三重辅助功能错误(不带“`alt`”的图像)和代码“`css`”时发出警告(至少在 Visual Studio 代码的 quick plug-in 中是这样说的)。

### 反应性(第 2 部分)

听到反应性，首先浮现在我脑海中的是**【xjs】**(我指的是*【lib】*-我...。

[![meme de uma pessoa americana olhando pra câmera e ficando confusa](img/8a17890a6ced2f7f2e4a393930282a3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xV8yyrer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.gifer.com/54fJ.gif)

我冷静地想:“如果我把原生的商店与*自动订阅*换成**可观察的**呢？”我读到两者之间有一定的兼容性，于是决定自己测试。令我惊讶的是:

[https://codesandbox.io/embed/svelte-with-rxjs-7uvxx](https://codesandbox.io/embed/svelte-with-rxjs-7uvxx)

<sup><sub>(对不起代码混乱)</sub></sup>

是的，速递与**xjs**很好地集成。

> **编辑**:不幸的是，上面的例子是和**一起运行的【快点 v 3.6】**。当升级到 v3.7 时，它最终会崩溃。现在，我将示例代码手册设置为快速版本 v3.6。再次感谢 [@kaisermann](https://github.com/kaisermann) 提醒我:d

**我留下一个提示:**速记员在收到数据流的任何东西之前读得不那么好(*超时*。为避免可能出现的错误，建议使用以下解决方案之一:与另一个流合并，该流包含值“*”无效的“*”并在响应前发出，这样您就可以在“`{#if}`”内检查是否已发出任何值，或将该流包含在“*”promise 中*

### 马斯...*运行时*？

好吧，当我说速递没有来自*运行时*的 *lib* 时，我撒谎了。当然，对于*虚拟礼物*是没有的，因为正如我所解释的那样，这将是多馀的。但是*运行时*却很干净。基本上是类似于**挂钩** do **React** 的功能。

> 文件

#### *生命周期*

`onMount` & `onDestroy`对用于渲染；`beforeUpdate`&`afterUpdate`对用于零部件更新。

#### *语境*

`getContext`&`getContext`。也和反应堆里的环境相似。

#### `preloading`

这是专属于**sapir**的。当在*链接*中使用指令，甚至在用户单击元素(在`mouseover`事件中，预测其动作)之前加载和运行数据时，就会执行此操作。

### E claro， <abbr title="Server Side Rendering">SSR</abbr> ！

由开发人员决定。如上所述， [Sapper](https://sapper.svelte.dev/) 在这方面有所帮助。如上所述，本工具灵感来源于 **Next.js** 。当然，您可以只使用快捷方式将所有内容合并成一个*捆绑包，并创建您的 <abbr title="Single Page Application">SPA</abbr> 。*

 *[![Apresentadora Oprah Winfrey distribuindo prêmios para a platéia](img/03f7af13cee646a7db56121b1936adf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DS543Yal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/b7fc556c96a04f7c0fcd7f5ef141c4bc/tenor.gif)

但是，使用 Sapper，您可以扩展您的应用程序功能，以使用带有“<abbr title="Server Side Rendering">SSR</abbr>”的服务器(甚至可以使用“[express](https://expressjs.com/pt-br/)”。

或充其量，使用`sapper export`(类似于[盖茨比](https://www.gatsbyjs.org/)简化)导出[【jam stack】](https://jamstack.org/)应用程序，生成静态工件，准备上传到 <abbr title="Content Delivery Network">CDN</abbr> 。fast 本身以*自动读取*与*水合*部分的应用。

### 我遇到的问题

当然不止是金发。我会告诉你一些经验:

#### 是

我在使用**笔**时遇到了一些问题。为了测试，它是用[自己的变压器](https://gist.github.com/ninetails/2f84baab9518006c5d45263f57726cc4)绕线的(在本例中，我只使用笔，但可以整合任何现有的。我建议使用此自定义变压器，因为“`jest-transformer-svelte`”和大多数其他类似软件包都依赖于“**roll up**”，我个人认为这是不必要的，并通过不引用原始文件来创建文件。尽管如此，我在此提醒您，对于快速文件来说，代码复盖范围有点模糊，否则就不存在，也就是说不太可靠。很快我会建议在快速项目中取消代码复盖。

#### 漂亮些

让我把预处理程序放在后台的一个原因是我在使用 Prettier 插件时遇到了很多问题。对于使用 CSS 编写的样式代码来说，此功能非常有用，但是与 Stylus 的集成没有很好地定义，并且我遇到了许多格式错误的问题。由于插件没有很好地与预处理程序集成，它最终会将代码转换为无法读取的东西，而不是“”可解析的东西，为了避免写很多“**”prettier-ignore“**”，建议避免使用预处理程序。

对于特定样式，我还看到，使用预处理程序时编译器无法检查和验证组件未使用的样式。

### ESLint

使用 **Sapper** 在斯洛文尼亚语中指出了[错误(已修正](https://github.com/eslint/eslint/issues/11940)。由于 **lint-staged** 传递文件名，且具有 *Sapper* 参数(以及下一个. js 9)的路由文件使用方括号，eslint 将方括号解释为 *Bash* 的搜索参数，而不是

### *HMR*

使用 **Sapper** 的另一个头疼是 *live reload* 在使用 *Sapper* 等*生命周期*功能时可能效果不佳如果您使用的是纯 caso，您将不会遇到任何问题，但如果您使用的是*sapper*中的**SSR**，则它会因伺服器端程式码的变更而遗失，且需要根据变更重新启动应用程式。

## 结论

<sup><sub>(如写作课所教，当然要有一个！)</sub></sup>

我不认为自己是*早期领养*-我...。*【hypera】*也许吧。但我的实验是积极的，比我预想的要好得多。如我所说，*摇动了我以前专属于**反应堆**的*的心。以上列出的许多功能在我最喜欢的*框架中实施起来并不轻松。*

它仍然是新的，这里没有*市场*可供使用(巴西)。但在我看来，它仍然有很大的潜力。当然，我所说的很多事都有我的偏见。即使在享受试剂的同时，我的简介也更倾向于尝试转向 SSR 和静态优化方面，而不是仅仅停留在 *SPA* 、 *mobile* (从而 *webcomponents* 和*不久，这个框架完全满足了我的期望。*

希望很快能有*的相遇，随着其采用的增长(我对 rs 抱有希望)。*

 ** * *

> 此帖子是响应的请求而创建的@ felicipifijo 用于[讲述我与速递](https://github.com/frontendbr/forum/issues/1199#issuecomment-512836768)的经历
> 
> 我想感谢[异议](https://discord.gg/Xf9zzW9)的狱方在本文的某些方面给予的帮助。
> 我也再次感谢 [@kaisermann](https://github.com/kaisermann) 帮助写这篇文章。

也是:

> 简短的非正式谈话和古老的迷因-我...。我很少写信给社区而不是在论坛上。我无意冒犯任何人。我也请求你不要在下面的评论中冒犯我如果你觉得这篇文章不好笑。尝试是为了让 post 的内容更容易阅读。我为错误道歉，主要是同意，因为我会根据我脑子里想的改变文字，而‘t0’并不总是和谐的。更重要的是，我把这段文字作为我在实验期间经历的一切的汇编。所表达的意见是针对个人和关于这一具体时刻的。永远不要把它们当作事实，我的观点和所描述的工具都可能改变和演变，将来会与本文产生分歧。*【btw】*我添加图片时并不担心标准化，或*【版权】*，因为它们只是说明性的(甚至可以停止工作，因为*非法链接*，这是一个很好的爱好者。这个*邮政*没有货币化(至少对我来说没有，作者-我...。页:1。此外，我要求在我对反应堆的立场的游戏中把它拿走。没有*更好的框架*，而最好的框架是最适合产品和团队的框架。

## 加入我们吧！

[![Imagem do Tio Sam usada no alistamento americano](img/e665e1d6eca7ac5002994c6b272a6b1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OS5M7rKB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://radioimg.s3.amazonaws.com/connectingvets/styles/nts_image_cover_tall_775x425/s3/poster.jpg%3F1OjrvU_4ezS4q42xtvfUeQ8DhRM3qrmP%26itok%3DfLua2OFq%26c%3De343bb5d7f1eb529d074ad2775302016)

*   [巴西论坛前端](https://github.com/frontendbr/forum/issues)
*   [不和苗条的巴西](https://discord.gg/Xf9zzW9)

## 参考文献

(唯一的英文内容我留了一面 unicode 横幅:∞)

### Meta 🇬🇧

*   [地点](https://svelte.dev/)
*   文件
*   [工兵](https://sapper.svelte.dev/)
*   [苗条@不和谐(官方)](https://svelte.dev/chat)

### 发帖者

*   [【为什么我不使用网络组件】](https://dev.to/richharris/why-i-don-t-use-web-components-2cia)，由[富哈里斯](https://dev.to/richharris)(邮件明显陈旧，但我建议阅读评论)
*   [快点:JavaScript 框架不是框架](https://blog.geekhunter.com.br/svelte-o-framework-javascript-que-nao-e-um-framework/)，由[冷 thiago](https://blog.geekhunter.com.br/author/thiago-frias/)
*   一个 dos 框架的时代
*   [快 3 + Firebase:用新的 JS 工具](https://escoladejavascript.com/crud-com-svelte-3-e-fireabase/)创建 CRUD，其中 [Sergio Lima](https://dev.to/sergiosouzalima) 教您如何制作与 Firebase 集成的应用程序
*   消失的框架🇬🇧
*   [苗条 3 🇬🇧入门](https://alligator.io/svelte/getting-started-with-svelte)
*   为缺乏耐心的(React)开发者🇬🇧 、波尔[瓦伦蒂诺·加利亚迪](https://dev.to/valentinogagliardi)准备的苗条 3 教程

有权翻译由 [Eduardo Rabelo](https://medium.com/@oieduardorabelo) 所作的最后两篇文章:

*   [快 3:从快 3](https://medium.com/@oieduardorabelo/svelte-3-come%C3%A7ando-com-svelte-3-1c38983850b6) 开始
*   [快点 3:开发人员指南(react)](https://medium.com/@oieduardorabelo/svelte-3-guia-para-o-desenvolvedor-impaciente-935389f0c08a)

### 视频

*   [里奇·哈里斯——反思🇬🇧的反应性](https://youtu.be/AdNJ3fydeao)
*   [电脑，给我造一个 app 丰富的 Harris - JSConf EU 2018 🇬🇧](https://youtu.be/qqt6YxAZoOc)

### 光标

*   [Svelte.js -完整指南🇬🇧](https://www.udemy.com/sveltejs-the-complete-guide/) ，波尔[马克西米利安·施瓦兹米勒](https://www.youtube.com/channel/UCSJbGtTlrDami-tDGPUV9-w)

## 变更日志

### 2019-08-06

*   增加了对 Storybook 的简短参考
*   固定 3.6 版中的快捷方式的代码手册补丁程序
*   参考资料中添加了 Sergio Lima 员额

### 2022-10-19

*   为图像添加说明(希望我没有跳过任何内容)
*   代词的变化和中性语言的使用(我也可能经历过某事)

## 本文本许可证

[啤酒](https://pt.wikipedia.org/wiki/Beerware)**********