# 设置角度项目以使用 tailwindcss

> 原文：<https://dev.to/mauro_codes/setup-your-angular-project-to-work-with-tailwindcss-1p5f>

# 什么是 tailwindcss？

引用亚当·瓦森的话:

> tailwindcss 是一个高度可定制的低级 css 框架，它为您提供了构建定制设计所需的所有构件，而没有您必须努力克服的任何恼人的固执己见的风格

## tailwindcss 和其他 css 框架有什么区别？

tailwindcss 没有任何主题或内置的现成 UI 组件。此外，Tailwind 并不固执己见，这意味着它提供了高度可组合的、低级的实用程序类，使构建独特的、完全响应的用户界面变得容易，而无需离开 HTML 文件。

## 我为什么选择 tailwindcss？

我喜欢从头开始构建自己的用户界面的过程，但我不想处理来自编写自定义 CSS 或思考每个元素的最佳名称的痛苦。借助 tailwind，你可以轻而易举地构建复杂的组件，这要归功于像[伪类变体](https://tailwindcss.com/docs/pseudo-class-variants)和[响应实用程序变体](https://tailwindcss.com/docs/responsive-design)这样的特性

好吧，这看起来很神奇，但是...我可以在我的角度项目中使用它吗？

# 角度积分

tailwindcss 最好的特性是在构建过程中发现的，这可以方便地使用(大多数情况下) [postCSS](https://postcss.org/) 插件自动完成。不幸的是，Angular CLI 目前不允许开发人员访问正在使用的 webpack 配置(由 CLI“幕后”管理)，所以您运气不好。除非您使用 ng-eject 完全自定义您的 Angular CLI 版本😖。但是等等！如果您退出项目，您还会失去 Angular CLI 提供的一些功能，例如:

*   给你的角度项目添加特征:[https://angular.io/cli/generate](https://angular.io/cli/generate)
*   运行您的应用程序单元测试:[https://angular.io/cli/test](https://angular.io/cli/test)
*   为生产构建您的应用:[https://angular.io/cli/build](https://angular.io/cli/build)
*   使用 Schematics 构建你自己的基于模板的代码生成器:[https://angular.io/guide/schematics](https://angular.io/guide/schematics)

## ng-tailwindcss 前来救援👀

ng-tailwindcss 是一个 CLI 工具，用于将 tailwindcss 集成到 Angular-CLI 项目中，并尽可能减少痛苦。多亏了这个神奇的资源库，你不需要弹出你的项目。

### 安装

1.  启动新的 angular-cli 项目后，运行以下命令:

```
npm i ng-tailwindcss -g
npm i tailwindcss -D 
```

Enter fullscreen mode Exit fullscreen mode

1.  然后，我们将生成一个顺风配置文件:

```
npx tailwind init 
```

Enter fullscreen mode Exit fullscreen mode

1.  在 src 文件夹中创建一个空的 tailwind.css 文件，并使用 [@tailwind](https://dev.to/tailwind) 指令将 tailwind 的基础、组件和实用程序样式注入 css:

```
/* tailwind.css file */
@tailwind base;
@tailwind components;
@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

1.  配置源/目标/配置文件:

```
ngtw configure 
```

Enter fullscreen mode Exit fullscreen mode

这将在项目的根目录下生成一个 ng-tailwind.js 文件。

1.  通过运行以下命令，更新 package.json 脚本以包含 tailwind 编译:

```
ngtw scripts 
```

Enter fullscreen mode Exit fullscreen mode

现在，将`npm run start`用于开发服务器将确保您的顺风文件与您的项目一起被查看和构建，并且您仍然可以依赖 angular-cli 来处理其他任何事情。

### 测试您的设置

转到您的 app.component.html 文件并粘贴以下代码:

```
<div class="max-w-sm rounded overflow-hidden shadow-lg">
    <img class="w-full" src="https://tailwindcss.com//img/card-top.jpg" alt="Sunset in the mountains">
    <div class="px-6 py-4">
        <div class="font-bold text-xl mb-2 text-black">The Coldest Sunset</div>
        <p class="text-gray-700 text-base">
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Voluptatibus quia, nulla! Maiores et
                perferendis eaque, exercitationem praesentium nihil.
        </p>
    </div>
    <div class="px-6 py-4">
        <span class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2">#photography</span>
        <span class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2">#travel</span>
        <span class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700">#winter</span>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该会看到下面的卡片:

[![tailwindcss example card](img/a19b24aa04fddbdf0c97449e5ff149e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--olYVn1ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gG64zco.png)

如果你想看看 tailwindcss 的运行，请查看我在 github 上的 tailwind-CSS-snippets repo:[https://github.com/mauro-codes/tailwind-css-snippets](https://github.com/mauro-codes/tailwind-css-snippets)

[![tailwindcss snippets](img/709052363cd07bfbdfbe1f96f2cadcff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j-ghqloa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/6xnlwfj.png)

另外，查看官方的 tailwindcss 文档[这里](https://tailwindcss.com/docs/installation)

一旦开始玩顺风，就没有回头路了。所以，请小心😀