# 设置 Gulp 为你的前端做“所有的事情”

> 原文：<https://dev.to/epicosity/set-up-gulp-to-do-all-the-things-for-your-front-end-26h1>

最近，我开始尝试让 gulp 自动帮我做所有的前端“事情”。我以前一直使用 VSCode 任务运行器来传输我的 scss 或更少的文件。但是偶尔我会在网站上遇到这样的问题，另一个开发人员(甚至是我自己)忘记了我们正在使用预处理器，而是编辑了主 CSS 文件。然后，我们只是结束了一个混乱之间的预处理器文件和 transpiled 文件不同步。为什么不缩小呢？这对网站性能有好处，如果开发者打开 CSS 文件，他们会马上注意到它被最小化了，并去寻找 less/scss。如果我这么做了，为什么不添加一个自动修复程序呢？当你在浏览器中检查的时候，有一个 sourcemap 文件使它更容易总是很好的。

### 安装所有的东西

重要的事情先来。 [VSCode 有一点点关于一个人可能想要如何自动化一些过程](https://code.visualstudio.com/docs/languages/css#_automating-sassless-compilation)，第一步是安装 gulp。

一旦你安装了 gulp，你将需要安装下面的其他包:

[gulp-less](https://www.npmjs.com/package/gulp-less)

[gulp-sass](https://www.npmjs.com/package/gulp-sass)

[gulp-clean-CSS](https://www.npmjs.com/package/gulp-clean-css)

[gulp-source maps](https://www.npmjs.com/package/gulp-sourcemaps)

[gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer)

### 创建一个大口任务

现在是时候创建您的任务了。您需要创建一个名为“gulpfile.js”的文件。

#### 要求所有的东西

首先，在 gulpfile.js 中，您需要所有这些刚刚安装的漂亮的包:

[![](img/75d2dc8a397adf40a3cfa9e9f99c65bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q1bBqNKD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6OtPruSSlhCa1XU-1GhqAQ.png)

<figcaption>gulpfile . js</figcaption>

中需要的包列表

#### 东西去哪里了？

接下来使用几个变量来保存前端文件的源和输出位置:

[![](img/6a3b01f48802e2cbb818dec55ff78dc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BGmaD3LT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArB6nxU5ESvL0owMc2MdO1Q.png) 

<figcaption>你的源文件和输出文件的位置</figcaption>

#### 可选选项

或者，您可能希望定义一些选项:

[![](img/4545294a3c85476b1ac445bdba51df1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--woKVS6Ld--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AroISdbR3miCQ5ZixccgfRw.png) 

<figcaption>Sass 和自动修复选项</figcaption>

#### Sass 任务

现在我们需要设置一个任务来处理 sass 文件:

[![](img/2a0c07a254e91e09729a39f23d8496fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f47RoBZs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9RZrs5iTOA0OKTRxlj-cGA.png) 

<figcaption>萨斯任务</figcaption>

让我们一行一行地看这里发生了什么(这将类似于本文后面的 Less 任务)。

1.  首先，我们初始化 gulp 任务，并将其命名为‘sass’。
2.  在 return 语句之后是真正的工作:
3.  我们使用我们的‘sass input’变量来定义我们希望 gulp 任务从哪里获取源文件，换句话说，就是在哪里存储您的。scss 文件？
4.  接下来，我们启动 sourcemaps 包
5.  然后调用 sass 包，利用我们可能已经设置的任何选项，并记录任何错误
6.  autoprefixer 和它的选项在我们传输 sass 之后出现，这将简单地在你的最终 CSS 中添加一些浏览器前缀来帮助向后兼容。
7.  现在我们将缩小生成的 CSS，这个例子显示了调试选项，并在控制台中打印出 CSS 的开始和结束大小供您参考。
8.  接下来，我们编写我们的源地图文件，在这种情况下，我们将它保存在 CSS 目录下的一个名为“地图”的附加目录中
9.  最后，我们将所有这些更改写入我们定义的输出位置。

在所有这些之后，你应该得到一个很好的缩小的，带前缀的 CSS 文件和一个 sourcemap 文件。

#### 多为少

也许你用 less 代替 sass，或者你想两者都设置以防万一:

[![](img/93a97b7869c66970d8f505dd4f126c22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nwwi_ArI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArDSb34W1_MVWWPbFdMhJ6A.png) 

<figcaption>类似于上面的</figcaption>

的萨斯

Less tasks 仅在输入变量 lessinput、less package(第 5 行)和任务名称(第 1 行)上有所不同。

#### 观看事物

现在您已经设置了这些任务，您可以做几件不同的事情。

1.  您可以使用 VSCode 通过内置的任务运行器来运行这些任务([查看 VSCode 文档以了解那个](https://code.visualstudio.com/docs/editor/tasks))
2.  您可以使用 gulp 来自动监视源目录的变化。

[![](img/a07ea3805d0887f5722c8408ca985636.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MdpNm-vD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFNGZgxvnE_M0hgK9VDxBmA.png) 

<figcaption>守望任务</figcaption>

一饮而尽

第一个监视任务监视“sassinput”目录中的变化，如果发现文件发生变化，就运行“sass”任务。第二个对“lessinput”做同样的事情。

### 我们快跑吧！

要运行此观察任务，请执行以下操作:

1.  将这个新的“gulpfile.js”保存到本地 web 项目的根目录下
2.  在终端中，或者更方便的是，在 VSCode 的 terminal 选项卡中，键入“gulp watchsass”或“gulp watchless”(这取决于您当时正在处理的内容)。
3.  这些任务将监视你的目录是否有变化，并运行你刚刚编写的所有代码来转换文件，并在你每次进行更改并保存时缩小你的 CSS。
4.  如果你想停止这个任务，一个简单的 Ctrl-C 就可以了。

[![](img/c897d1ed5981f5189c07f4f6f2eb7de0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--99nThc7j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/281/1%2ASUF7gt9OXbVGuhT4eiyCYg.png) 

<figcaption>VSCode 终端显示大口任务正在运行</figcaption>

### 编码所有的东西！

现在你已经设置好了所有的东西，继续前进，编码所有的东西，保存你的文件，知道 gulp 正在为你处理所有的 nit 挑剔的东西，你就可以高枕无忧了。

我很想听听你在 gulpfile 中添加了哪些自动化任务？

* * *