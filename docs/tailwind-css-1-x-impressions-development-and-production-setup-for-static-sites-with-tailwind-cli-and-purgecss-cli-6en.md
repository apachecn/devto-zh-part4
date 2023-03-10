# 使用 Tailwind CLI 和 PurgeCSS CLI 为静态站点创建 Tailwind CSS 1.x 展示、开发和生产设置

> 原文：<https://dev.to/hugo__df/tailwind-css-1-x-impressions-development-and-production-setup-for-static-sites-with-tailwind-cli-and-purgecss-cli-6en>

> 如何用 Netlify 上的静态站点设置 Tailwind CSS

参见位于 github.com/HugoDF/netlify-lambda-tailwind-static-starter 的[的完整入门库](https://github.com/HugoDF/netlify-lambda-tailwind-static-starter)

Tailwind CSS 是“一个实用的 CSS 框架，用于快速构建定制设计”。我将介绍我对它的第一印象，以及如何利用它的 CLI(和 PurgeCSS CLI)将它添加到任何没有构建过程的项目中(例如 Webpack，Gulp)。

## 顺风 CSS 的第一印象

### 优点

集成的 CLI 工具意味着您不必设置自己的构建步骤。我发现对于开发来说，运行完整的顺风构建(不清除样式)是有益的，以至于我将它添加为`prestart` npm 脚本(意味着它在`yarn start`或`npm start`之前运行)。

Tailwind 1.x 利用 PostCSS，而不是基于 SCSS 或手写笔。尽管 node-sass 对于前端工具来说是一个巨大的进步，但它*是*一个链接依赖的事实会在改变节点版本或使用不同的操作系统时引起问题。

这是一股新鲜空气，让*而不是*不得不设置 node-sass，这是一项一旦构建并运行起来就会忘记的技能。

Tailwind 实用优先的方法意味着我几乎不写任何 CSS。我设法快速地将我的 CSS 知识映射到实用程序类上，这对于快速建立原型非常有用。Adam Wathan 的 [CSS 实用程序类和“关注点分离”](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/)是一本关于如何克服实用程序类“为什么有人会这样做”的好书。

一旦有效的 Tailwind 1.x 设置所必需的几个命令行工具串在一起(即 PurgeCSS ),感觉就很棒。

可复制粘贴的组件库也很棒。与 Bootstrap、Foundation 甚至 Material UI 或布尔玛等一些更现代的工具包不同，Tailwind CSS 组件都不依赖于 JavaScript。毕竟是 CSS 框架。因此，这些组件更像是一本食谱/食谱，可以复制粘贴和修改。

Tailwind 1.x 推出了预定义的调色板，并对允许的尺寸进行了合理化。没有更多的像素投掷，只有一个比例:`1 2 3 4 5 6 8 10 12 16 20 24 32 40 48 56 64`，你可以在[默认间距比例文档](https://tailwindcss.com/docs/customizing-spacing#default-spacing-scale)中阅读更多关于顺风 CSS 比例的信息。

### 弊

默认的包大小相当大，这可以通过使用 [PurgeCSS](https://www.purgecss.com/#cli) 或者配置构建来抵消(参见顺风 CSS 文档中的[控制文件大小)](https://tailwindcss.com/docs/controlling-file-size)

设置 PurgeCSS CLI 可能会很痛苦，您必须自由地加入白名单以获得 Tailwind CSS 修饰符实用程序。不过，这些都有很好的记录。

使用像 Tailwind CSS 这样的基于实用程序类的框架所涉及的重复一开始就有点乏味。

1.x 还引入了一堆额外的地方，你需要重复自己，所有的链接都需要有一个`class="text-blue-500 hover:text-blue-800”`，所有的标题都必须手动调整大小。从长远来看，这很好，因为这些样式会不断被覆盖，所以为什么不从一开始就让它们保持原样呢？

不再有 SCSS，甚至不再有 CSS，所有的都是实用类，这可能会影响到你。

### 裁决

我是一个粉丝，它很好，并且与我的所有其他堆栈选择正交:

我可以用它来做水疗

我可以用它在 Netlify 上做静态(HTML)演示，比如 Netlify Pocket Lambda 演示

我可以使用服务器页面设置

使用命令行工具，它甚至是语言不可知的。我不需要将 npm/Node 添加到部署的环境中，我可以预构建、使用 CDN 版本甚至将其签入 git。

## 使用 Yarn、Tailwind CLI 和 PurgeCSS CLI 为静态站点设置 Tailwind CSS v1.x

### 使用顺风 CLI 启动并运行

```
yarn add --dev tailwindcss
# or
npm install --save-dev tailwindcss 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到`package.json`。

```
{
  "scripts": {
    "build:css": "yarn build:tw",
    "build:tw": "tailwind build src/styles.css -o public/styles.css",
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用 PurgeCSS CLI 野蛮地修剪 CSS 规则

**重要提示**:这是清除 CSS 的幼稚方法。没有考虑到我们用的是顺风 CSS。因此，当使用这种方法时，一些实用程序将无法工作。请参阅顺风的 PurgeCSS 配置，了解正确的方法。

```
yarn add --dev purgecss
# or
npm install --save-dev purgecss 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到`package.json`。我们利用 npm 脚本“post”-hook 针对 Tailwind CLI 的输出运行 PurgeCSS。

因为`build:css`是顺风 CLI 生成命令，所以它是有效的。

我们使用`--css`参数将 PurgeCSS 指向生成的样式，使用`--content`参数指向我们要清除的 HTML。

`--out`标志使用与初始(pre-PurgeCSS) styles.css 相同的目录，这意味着我们正在覆盖它。

```
{
  "scripts": {
    "postbuild:css": "purgecss --css public/styles.css --content public/index.html --out public"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为开发而设置

对于开发，我们想使用一个完整的顺风 CSS 构建。

为了做到这一点，我们将利用我们倾向于使用`start`脚本运行应用程序的事实。

我们将再次利用 npm 脚本钩子，在本例中是一个“pre”钩子，来运行完整的 Tailwind CSS v1.x 构建。

`prestart`脚本简单地调用`yarn build:tw`。

将以下内容添加到您的脚本中:

```
"prestart": "yarn build:tw", 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您运行`yarn start`时，在运行`start`脚本之前，将运行一个完整的顺风构建。在 github.com/HugoDF/netlify-lambda-tailwind-static-starter[的](https://github.com/HugoDF/netlify-lambda-tailwind-static-starter)的 Netlify starter 项目中，`start`命令使用`netlify dev` ie。Netlify Dev 应该运行站点，就像它被部署到 Netlify 时一样。

### 顺风生产采购配置

正如在用 PurgeCSS CLI 大幅删减 CSS 规则中提到的，这种幼稚的方法不起作用。

以下是如何正确设置 PurgeCSS CLI 以感知 Tailwind v1.x，这取决于使用 Tailwind CSS 特定提取器(它只是一个正则表达式)。

我们正在利用提取器选项，如 PurgeCSS“配置文件”下记录的[，参见下面的`purgecss.config.js`](https://www.purgecss.com/configuration#configuration-file) 

```
module.exports = {
  content: ['path/to/content.html'],
  css: ['path/to/styles.css'],
  extractors: [
    {
      extractor: {
        extract: content => content.match(/[A-z0-9-:\/]+/g)
      },
      extensions: ['html']
    }
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

并将`postbuild:css`脚本更新为以下内容，以便使用`purgecss.config.js`文件并仍然输出到`public`目录(从而覆盖 intiail Tailwind CLI 输出)。

```
"postbuild:css": "purgecss -c purgecss.config.js -o public" 
```

Enter fullscreen mode Exit fullscreen mode

## 从 Netlify 上的静态网站的生产就绪的顺风 CSS 开始

在[github.com/HugoDF/netlify-lambda-tailwind-static-starter](https://github.com/HugoDF/netlify-lambda-tailwind-static-starter)查看并使用完整的入门库

塞斯·施威特