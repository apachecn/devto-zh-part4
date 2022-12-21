# 这是我的 VSCode 扩展列表

> 原文：<https://dev.to/michaeljota/this-is-my-vscode-extensions-list-35c9>

*图片由 [rawpixel](https://pixabay.com/users/rawpixel-4283981/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3656074) 发自[Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3656074)T5】*

我是一个 Angular 开发者，每天都在用 VSCode。如果我需要的话，这个扩展列表恰好是给我未来的自己的，但是我希望它也能对其他人有用。请记住，这个列表实际上关注的是角度扩展，但也有几个是与语言无关的。

# IDE 体验

## 通用

*   代码拼写检查器这个我用来验证我总是写得正确。除了美国英语，它还有另外一个扩展来支持其他语言。
*   [VS 代码的编辑器配置](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)
*   到目前为止，我很喜欢这张图。我使用源码树已经有一段时间了，但是我发现我只用它来做一些基本的事情，比如拉、推、提交、重定基础以及查看 git 树形图。VSCode 已经有了 pull、push 和 commit，有了它，我可以很容易地重定基础，还可以绘制交互式 git 图。
*   Git lens——Git super charged 这是对 power git 用户的另一个扩展。它在 VSCode 中添加了如此多的选项，比如 git 责备、历史和简单的 diff 选择。
*   我搜索这个是因为 C#扩展有这个。它显示了变量的引用，类，类型，几乎所有的东西。真的有用。
*   [Version Lens](https://marketplace.visualstudio.com/items?itemName=pflannery.vscode-versionlens) 这个显示的信息和上面的一样，但是有依赖性。我只在 npm 包文件中使用过它，但我认为它支持其他类型依赖管理器。
*   [Visual Studio IntelliCode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode) 利用 IA powered 建议改进您的编码。

## Javascript/Typescript

*   [JS 参数注释](https://marketplace.visualstudio.com/items?itemName=lannonbr.vscode-js-annotations)JS/TS 文件中模拟命名参数的参数注释。

## 棱角分明

*   Angular 语言服务由 Angular 团队提供支持，这个扩展允许你编辑组件的 HTML，进行验证，并对你正在使用的属性和方法提出建议。
*   [角度示意图](https://marketplace.visualstudio.com/items?itemName=cyrilletuzi.angular-schematics)直接从 VSCode 使用角度 CLI 的更好方法。
*   [角度支持](https://marketplace.visualstudio.com/items?itemName=vismalietuva.vscode-angular-support)增加了对插值、变量、文件定义等的定位/查看支持。

## CSS/SCSS

*   [HTML 中 CSS 类名的智能感知](https://marketplace.visualstudio.com/items?itemName=Zignd.html-css-class-completion)
*   [Sass](https://marketplace.visualstudio.com/items?itemName=Syler.sass-indented)

# 调试

我正在尝试基于 Chrome 的新 Edge。到目前为止，一切顺利。所以，我现在只使用它。在此之前，我用的是普通的 Chrome，带有普通的 Chrome 扩展。

*   [边缘调试器](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)

# 测试

*   [有片段](https://marketplace.visualstudio.com/items?-itemName=andys8.jest-snippets)
*   Jest 运行 Jest 的所有测试，并直接在编辑器中给你一个报告，每个测试旁边有一个小球，绿色表示通过，红色表示失败。此外，还允许您在测试失败时选择和调试测试。

如果你使用类似于 [@angular-builders/jest](https://github.com/just-jeb/angular-builders/tree/master/packages/jest) 的东西，你必须配置 Jest 才能工作。

在您的设置中:

```
"jest.pathToJest":  "ng test {{ your angular project name }}" 
```

Enter fullscreen mode Exit fullscreen mode

在你的`launch.json`

```
{  "type":  "node",  "request":  "launch",  "name":  "vscode-jest-tests",  "program":  "${workspaceFolder}/node_modules/@angular/cli/bin/ng",  "args":  [  "test",  "{{ your angular project name }}",  "--runInBand",  "--testPathPattern"  ],  "console":  "integratedTerminal",  "internalConsoleOptions":  "neverOpen"  } 
```

Enter fullscreen mode Exit fullscreen mode

# 林挺/格式化

*   [更漂亮的代码格式化程序](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
*   [丝带](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)

# 定制

*   [熊猫主题](https://marketplace.visualstudio.com/items?itemName=tinkertrain.theme-panda)
*   [虚拟代码图标](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)

# 其他

*   [直播服务器](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
*   [波兰语](https://marketplace.visualstudio.com/items?itemName=pnp.polacode)