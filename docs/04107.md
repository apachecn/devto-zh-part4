# 2019 年 JavaScript 的 36 个必备工具

> 原文：<https://dev.to/x-team/36-essential-tools-for-javascript-in-2019-172b>

JavaScript 仍然是世界上最流行的编程语言。考虑到您需要与许多框架和库进行交互，在 web 上设计任何东西都至少需要 JS 的工作知识。随着其向区块链等重要新技术领域的扩张，JS 似乎将在可预见的未来继续发挥作用。

JavaScript 的流行伴随着大量的工具，使得用 JS 编码变得更加容易。这是一个众所周知的和流行的 JavaScript 工具的列表，这些工具被分类，定义了开发过程的重要部分。

### IDEs &编辑

你写代码的地方很重要。有些开发人员喜欢编辑器，有些开发人员喜欢集成开发环境(ide)。虽然编辑器有助于提高性能，但 ide 通常用于更复杂的项目，因为它们提供调试功能和对 ALM 系统的支持，可以与版本控制平台集成，等等。

<figure>

[!["Laptop","Code"](img/42fcb8ca5a541653af1e29d64bba19aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CVj59i8_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Laptop-and-Code.jpg)

<figcaption>This is where I work</figcaption>

</figure>

*   **[Visual Studio 代码](https://code.visualstudio.com/)** :微软的代码编辑器。根据 Stack Overflow 2018 开发者调查，Visual Studio 代码是最受欢迎的开发者环境工具(34%的受访者使用它)。它包括对调试、Git 控制、语法高亮、代码重构等的支持。
*   **[括号](http://brackets.io/)** :一个开源的、轻量级的文本编辑器。括号有可视化工具和预处理器支持。它有一个内联编辑器，你可以实时预览代码，看到你的变化立即反映在浏览器中。
*   **[Atom](https://atom.io/)** :这是 GitHub 那边的人写的。这是一个文本编辑器，有很多现成的特性:跨平台编辑、内置的包管理器、智能自动完成、查找和替换等...Atom 提供了大量的主题和选项，可以根据开发人员的需要进行定制。
*   **[WebStorm](https://www.jetbrains.com/webstorm/)**:JetBrains 做的 JS IDE。WebStorm 支持智能代码完成、实时错误检测、JS、TypeScript、样式表语言和最流行的框架的导航和重构。

### 任务运行器&模块打包器

就在几年前，学习如何使用任务运行器和模块捆绑器是浪费时间，而 web 应用和 JS 项目总体上已经变得更加复杂。因此，这些工具已经成为任何 JS 开发人员工作流程中的必要组件。

此外，它们有助于完成枯燥乏味的任务，如缩小、单元测试和网页刷新。虽然一些构建工具有一个陡峭的学习曲线，但它们可以帮助自动化编程中不可避免的琐碎任务。

<figure>

[!["Lego", "Build"](img/8a69a9c8efcb8be6356ce605c6228e82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---TzUhrRP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Lego.jpg)

<figcaption>Let's connect all the little blocks and make something beautiful</figcaption>

</figure>

*   **[Grunt](https://gruntjs.com/)** :一个 JavaScript 任务运行器，自动执行重复的任务，如缩小、编译、林挺、单元测试等等。Grunt 拥有超过 6000 个插件的庞大生态系统。
*   **[Gulp](https://gulpjs.com/)** :和 Grunt 一样，Gulp 定义并运行耗时的任务。但与 Grunt 不同的是，Gulp 将任务定义为 JavaScript 函数，而不是配置对象。
*   **[早午餐](https://brunch.io/)** :侧重于简单和快捷。它有一个简单的配置和详细的文档。因为它对您的项目做了一些假设(或限制，取决于您如何看待它)，所以使用 Brunch 来设置项目通常比使用任何其他构建工具更容易。
*   **[Webpack](https://webpack.js.org/)** :捆绑你所有 JavaScript 应用，以及各种不同资产如图像、字体、样式表的软件。支持 ESM 和 CommonJS。
*   **[browser ify](http://browserify.org/)**:JS 工具，你可以在其中编写 Node.js 风格的模块并编译它们以便在浏览器中使用。将所有需要的模块打包成一个名为`bundle.js`的文件。
*   **[约曼](https://yeoman.io/)** :现代网络应用的网络脚手架工具。这是一个规定最佳实践并提供模块化架构的工具，因此您可以快速扩展项目。约曼支持林挺，测试，缩小，等等。
*   **[包](https://parceljs.org/)** :这个模块捆绑器支持多核编译，速度快得惊人。开箱即用，Parcel 支持 JS、CSS、HTML 和文件资产。

### 文档软件

没有文档的软件是任何开发人员的噩梦。文档写得不好的软件可能更糟糕。文档解释了软件如何运行以及应该如何使用。

但是编写文档是困难的:它复杂、耗时，而且很无聊。幸运的是，软件文档工具减轻了编写文档的负担，并使过程更快。

<figure>

[![Docs](img/e336510d19d71727f8edfd1627baa44b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NuwurmFh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Docs.jpg)

<figcaption>I made nice little booklets of the docs, said no developer ever</figcaption>

</figure>

*   这是一个用 CoffeeScript 编写的文档生成器。它生成一个 HTML 文档，显示您的注释和代码。所有注释都通过 Markdown 传递，代码通过 Highlight.js 语法突出显示传递。
*   :帮助贯穿整个 API 生命周期，从设计到文档。它是一套描述 API 的规则和工具。它是语言不可知的，人和机器都可读。
*   **[YUIDoc](https://yui.github.io/yuidoc/)** :从源代码中的注释生成 API 文档的 Node.js app。YUIDoc 支持广泛的 JS 编码风格。它的输出是格式化为一组 HTML 页面的 API 文档。
*   **[JSDoc](http://usejsdoc.org/)** :一种用于注释 JS 源代码文件的标记语言，然后用于生成 HTML、RTF 等格式的文档。

### 测试框架

软件测试是开发过程中不可避免的一部分。你需要弄清楚你的代码是否能在不同的环境下工作。与实际需求相比，它是您识别错误、差距或缺失需求的方式。

软件测试工具派上了用场，因为应用程序越来越复杂，手动测试软件变得越来越麻烦。测试工具运行测试脚本并自动生成测试结果。

<figure>

[![Testing](img/5770cab7db4ff369d2ac0129729b6262.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vjdkfn-Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Testing.jpg)

<figcaption>Please let it turn out okay</figcaption>

</figure>

*   **[Mocha](https://mochajs.org/)** :托管在 GitHub 上的 JS 测试框架，Mocha 受到很多开发者的欢迎。它在 Node.js 和浏览器中运行，并且它的测试是串行运行的。常与断言库[柴](https://www.chaijs.com/)配对。
*   **[量角器](https://www.protractortest.org/)**:Angular 和 AngularJS 应用的端到端测试框架。该工具在真实的浏览器中运行测试，通过本机事件和特定于浏览器的驱动程序，像最终用户一样与您的应用程序进行交互。
*   **[Jasmine](https://jasmine.github.io/)** :测试 JS 代码的行为驱动开发框架。Jasmine 不依赖于任何其他 JavaScript 框架，也不需要 DOM 来工作。它有清晰易懂的语法，这使得编写测试很容易。
*   **[Jest](https://jestjs.io/)** :把大胆的“令人愉快的 JavaScript 测试”作为他们的口号。Jest 被脸书用来测试所有 JS 代码(包括他们的 React 应用)，它希望提供一个集成的零配置体验。

### 林挺软件

虽然从技术上来说也是测试的一部分，但林挺是如此重要，它应该有自己的一个类别。这意味着运行一个程序来检查你的代码的风格和编程错误。

每个 JS 开发者都应该使用林挺软件来保持他们代码的质量。它提高了代码的可读性，识别了结构问题，找到了那些难看的语法错误，并且通常允许您发现 JS 代码中的问题，而不必执行它。

<figure>

[![Linter at Work](img/bda836273fe3c1f396d5f9216df2dfc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b_ovAmRl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Linter-at-Work.jpg)

<figcaption>Linter at work</figcaption>

</figure>

*   **[ESLint](https://eslint.org/)** :这个是 X-Team 开发者的最爱。ESLint 是 JS 和 JSX 的开源插件，主要用于命令行。所有的规则都是可插拔的，所以开发者可以创建他们自己的林挺规则。
*   **[TSLINT](https://palantir.github.io/tslint/)** :另一个 X-Team 的最爱，TSLINT 检查类型脚本代码的可读性、可维护性和功能性错误。就像 ESLint 一样，它也可以使用自己的 Lint 规则进行定制。
*   **[JSHint](https://jshint.com/)** :一个社区驱动的工具，检测 JS 代码中的错误和问题。JSHint 还可以用来加强编码约定和样式指南。
*   **[流程](https://flow.org/en/)** :脸书开发的 JS 代码检查器。通过静态类型注释检查代码中的错误，但只需要最少的此类描述。心流会推断出其余的。
*   :全自动风格指南，让风格辩论留在门口，让你可以专注于更重要的决定。

### 调试器

啊，难道你不喜欢用你写的代码发现和解决问题的过程吗？你放在七百行代码中的那个逗号？让你的机器陷入无限循环的不合逻辑的 if-then 语句？

调试工具减少了调试的耗时和费力，并帮助开发人员获得更准确的结果。在令人沮丧的时候，调试器工具可以成为你最好的朋友。

<figure>

[![Debugging](img/516e9f2cde1b3d60b6179f3daf7c9ff6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JMnOasv4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Debugging.jpg)

<figcaption>Time to start debugging</figcaption>

</figure>

*   **[Chrome 开发者工具](https://developers.google.com/web/tools/chrome-devtools/)** :一套直接内置于谷歌 Chrome 浏览器的工具，Chrome 开发者工具拥有多个实用程序，帮助你一步一步调试 JS 代码。
*   **[JavaScript 调试器](https://developer.mozilla.org/en-US/docs/Tools/Debugger)** :由 Mozilla 开发者网开发，JavaScript 调试器可以用于在 Firefox 中调试，甚至可以作为独立的 web app 用于调试其他浏览器或 Node.js 中的代码
*   **[【JavaScript 开发工具(JSDT)](https://www.eclipse.org/webtools/jsdt/)** :提供支持使用 Rhino 和 Crossfire 调试 JavaScript 的插件。它可以在所有主流浏览器中调试 JS。
*   **[Node Inspect](https://nodejs.org/en/docs/guides/debugging-getting-started/)** :类似 Chrome 开发者工具，但是针对你的 app 在 Node.js 上运行的时候

### 安全分析器

网络安全已经成为公司和国家的头等大事。黑客可以利用软件中的漏洞来勒索受害者。虽然开源代码对创新至关重要，并且有助于免费加速开发过程，但它也带来了不可低估的安全风险。

安全分析器研究软件的依赖性，帮助降低安全风险。

<figure>

[![Security](img/54f240736bb1247455603e4cc4961a58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WlazlSkI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Security.jpg)

<figcaption>What exactly are you doing here?</figcaption>

</figure>

*   **[Snyk](https://snyk.io)** :开源安全平台，为 npm、Maven、NuGet、RubyGems、PyPi 等查找并修复漏洞。Snyk 被 Salesforce、谷歌、微软、ASOS 和其他大公司使用。
*   **[retire js](https://retirejs.github.io/retire.js/)**:retire js 有助于检测您是否拥有已知存在安全漏洞的依赖版本。这是一个命令行扫描器，有一个 Grunt 插件，一个 Chrome / Firefox 扩展，和一个 Burp / OWASP Zap 插件。
*   **[OWASP 依赖检查](https://www.owasp.org/index.php/OWASP_Dependency_Check)** :与 RetireJS 类似，OWASP 识别你的项目依赖，检查是否存在已知的、公开披露的漏洞。
*   :抓取你的网站，执行黑盒和灰盒黑客技术，寻找漏洞。扫描前端和服务器端应用程序。
*   **[源码清晰](https://www.sourceclear.com/)** :支持 Node.js，也支持 Ruby、Java、Python。Source Clear 帮助您找到经过验证的公共和私有漏洞。
*   **[OSS Index](https://ossindex.sonatype.org/)** :免费服务，查找开源依赖，了解哪里有已知的、公开披露的漏洞。支持多种生态系统:npm、Bower、Drupal、NuGet 等等。

### 包管理器

可以自动安装、升级、配置和删除存储库中维护的软件包的软件。但这还不是全部，包管理器还会查看你的依赖项，并确保新版本的包不会破坏你的代码。

[![Packages](img/5f7ba9b3a9586fc2f36b95e32dc3a962.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c9Jmkl22--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dukp6c7f7/image/upload/f_auto%2Cfl_lossy%2Cq_auto/s3-ghost/2018/12/Packages.jpg)

*   **[NPM](https://www.npmjs.com/)**:node . js 的默认包管理器，虽然也可以用于前端开发。它是世界上最大的软件注册中心，拥有超过 350，000 个软件包。
*   **[纱](https://yarnpkg.com)**:NPM 的主要竞争对手，以至于我[写了一篇关于它的帖子](https://x-team.com/blog/yarn-vs-npm/)。由脸书开发的 Yarn 在 2016 年发布时提供了一些优于 npm 的显著优势，尽管 npm 后来已经迎头赶上。

* * *

这么多工具！当然，虽然这些工具中有些是必不可少的，但有些却不是。哪些工具最适合你取决于你的客户、你的项目和你的工作方式。

每个工具都有一个学习曲线，很容易完成不了多少工作，因为你还在弄清楚新工具是如何工作的。这就是为什么你真的应该只考虑一个新工具，如果你相信它会让你的生活或你的项目变得更好。

肯定有我忘了包括的神奇软件。这个列表中有什么明显的漏洞吗？请在评论中告诉我✍.