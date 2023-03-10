# ui book——通过媒体查询对 React 组件进行可视化测试的工具

> 原文：<https://dev.to/amplifr/uibook-a-tool-for-visual-testing-of-react-components-with-media-queries-2a3o>

TL；DR:ui book——一个简单的工具,用真实的媒体查询对 React 组件进行可视化测试。

[![](img/a19490f613e8c2510dcdc079a726886f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9XLYmdL0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpn7w8mnrc7agcbhp3m7.gif)

嗨！我叫维塔利·张承，是 Amplifr 的前端开发人员。我将告诉你 Uibook 以及它如何帮助你。

## 我们为什么这么做，有什么意义

我们有很多状态丰富的组件，有必要不断测试一个应用程序的移动和桌面版本。在固定一个组件时，也很容易损坏另一个组件。

这就是为什么我们决定创建一个可以快速测试组件的页面。

Uibook 可以让你快速看到所有状态的组件和道具组合。由于支持媒体查询，开发人员可以在单个页面上显示桌面和移动版本的组件。但是 Uibook 不仅仅面向开发者:

*   设计人员可以在他们的设备上查看组件的所有状态，而无需设置本地服务器。
*   经理们发现，即使是一个乍看起来简单的弹出窗口，也可能包含一系列开发人员必须考虑的边界状态——这有助于他们更好地理解产品的设计。
*   编辑人员可以使用实时文本编辑模式检查真实组件中的文本，使其看起来完美无瑕。

## Uibook 与类似物的比较

[![](img/a5816c7274a8d98c8f2e34db09f0e0c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gKtl_PWc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qn5a7hm18kf42cafzkg9.png)

有 Storybook，Styleguidist，也有其他类似的解决方案，但 Uibook 采取了不同的方法。我可以指出三个主要区别:

*   Uibook 支持开箱即用的媒体查询组件，以检查处于移动状态的组件。
*   Uibook 不需要单独的构建器，可以轻松集成到现有项目中。
*   我想你的 Uibook 测试将会对你的客户公开。任何用户都可以找到 bug 并留下反馈。

尽管在 Uibook 中开发 UI 组件也很方便，但我们主要需要一个可视化测试工具，而不是开发工具。您需要对项目进行全局更改吗？浏览所有页面，确保所有组件都正确显示。

[![](img/2419fa6f90cac07c242daf8262c964a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QX6VsQwK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/00tore2n7b4xfwa27jz6.png)

## 技术实现

Uibook 是一款 React 应用。开发人员创建一个页面——这是一个特定格式的文件，包含组件的所有状态。单个状态称为 Case。如果您想描述一个复杂的案例，每个案例都包含组件或 JSX 的道具。您还可以为每种情况定义屏幕宽度和高度。

Uibook 使用两个控制器在屏幕上呈现所选页面:有和没有媒体请求。

因为不可能用 CSS 和 JavaScript 模拟媒体查询，所以我们决定在一个`<iframe>`中呈现组件，如果用户已经指定了屏幕的宽度或高度。

主控制器可选地将组件放在用户的包装器中。它允许用户在顶部导航栏中选择传递给包装器的值。根控制器还增加了热键和实时文本编辑模式。

我不想让单独的捆扎机用于项目和视觉测试。在另一种情况下，您必须存储更多的文件和依赖项。配置、运行、构建和部署需要更多时间。Uibook 作为 Webpack 插件集成到项目的构建器中:

```
plugins: [
 …
 new UibookPlugin({
   controller: path.join(__dirname, '../controllers/uibook.js')
 })
] 
```

*网页包. config.js*

Uibook 添加了一个单独的块，并且不会增加主应用程序的大小。它使用网络包的`SingleEntryPlugin`或`MultiEntryPlugin`工作。它包括 CSS 和脚本从主要应用程序考虑缓存克星。下面是插件获取文件列表的方式:

```
 let files = compilation.chunks.find(function (i) {
   return i.name === 'uibook'
 }).files 
```

下一步是生成一个 HTML 文件。Uibook 在这一步没有使用额外的依赖，因为这很容易做到:获取一个模板，添加导入，将其添加到输出:

```
 compilation.assets[outputPath + '/index.html'] = { … } 
```

有`HtmlWebpackPlugin`就要排除`uibook`组块。Uibook 会很好地提醒你，因为 DX 很重要。

[![](img/edbeffede522dd787a1a6c9035cc97d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UfsglL0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cf4gc3ppub5ibom852jj.png)

## Uibook 很简单

只有三个依赖项:React、Webpack 和`create-react-class`。它是用 ES5 写的，所以即使你的项目中没有巴别塔，它也能工作。另外，如果配置文件有问题，Uibook 有内置提示。

[![](img/b3161767252793146eb3e08983c3d394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J7YgzpMl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/31qad5ta2qov4gg9akl6.png)

## Uibook 灵活

[![](img/8d57f496b8ebe9487f684173008e012f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fx9gdFW---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x92clfvyose762dffe8r.gif)

您可以将所有组件包装在您的控制器中。它可能是 Redux、Context 或两者的包装器。下面是一个新的上下文 API 的例子:

```
export default UibookStarter({
 wrapper: (children, props) =>
   <Context.Provider value={ props }>
     { children }
   </Context.Provider>,
 values: {
   locale: ['en', 'de'],
   theme: ['dark', 'light']
 },
 …
}) 
```

Uibook 在顶部导航菜单中显示用户密钥及其值的列表。

## 如何将 Uibook 集成到项目中

例如，我们想在 Uibook 中添加按钮组件(`src/button.js`)。我们需要首先安装`uibook`包，然后创建一个控制器文件和一个页面文件。控制器用于导入所有的 Uibook 测试，页面是一组案例，或者是组件的参数组合。

以下是如何做到这一点:

1.  开始吧，`npm install uibook --save`；
2.  您可以在这里使用`npm init uibook`命令来创建示例文件，或者您可以手动执行所有操作。您将获得以下结构:

```
your-project
├── uibook
│   ├── button.uibook.js
│   └── uibook-controller.js
├── src
│   └── button.js
├── webpack.config.js
└── package.json 
```

1.  在 Webpack 配置文件中添加插件:

*webpack.config.js*

```
let UibookPlugin = require('uibook/plugin')

module.exports = {
 …
 plugins: [
   new UibookPlugin({
     controller: path.join(__dirname, '../src/uibook-controller.js'),
   })
 ],
} 
```

1.  让我们把测试(或页面)写在`uibook/button.uibook.js`里。如果您已经利用了一个`init`命令，那么您已经有了这个例子:

```
import UibookCase from 'uibook/case'
import Button from '../src/button.js'

const PROPS = {
 onClick: UibookCase.event('onClick')
}

const ButtonUibook = {
 component: Button,
 name: 'Button',
 cases: [
     () => <UibookCase props={{ ...PROPS, isLarge: true }}>
       Large Button
     </UibookCase>,
     () => <UibookCase props={{ ...PROPS, isDisabled: true }}>
       Disabled Button
     </UibookCase>
 ]
}

export default ButtonUibook 
```

1.  导入此 Uibook 页面并将其传递给控制器:

```
import UibookStarter from 'uibook/starter'
import ButtonUibook from './button.uibook'

export default UibookStarter({
 pages: {
   Button: ButtonUibook,
 }
}) 
```

1.  搞定了。您可以照常启动项目(例如，`npm run start`)并在浏览器中打开`/uibook`。你会看到三种情况下的按钮(如果你有一个组件`/src/button.js`，当然):

[![](img/9aad0eafd39cf61bf7168a1d14cf55c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f1hhSWBD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a3ph9p7shqbmhmybko5o.png)

## ui book 如何帮助我们？

我们在工作场所使用 Uibook 已经有一年多了。前端开发人员仅通过 Uibook 创建新组件，同时创建一个带有边界属性的测试文件。这比编写一个控制器来查看真实 web 应用程序中的组件要快得多。此外，在任何全局更改之后，您可以使用这个测试文件进行进一步的可视化测试。

邪恶火星人的前端负责人 Andrey Sitnik 在迁移到 Uibook 后不再担心`node_modules`更新:

> Uibook 终于给了我们信心`normalize.css`更新不会坏任何事。Uibook 为我提供了一种在一行中查看所有状态的所有组件的方法。`@media`支持在这项任务中很有帮助。我们可以在一页上看到所有的州。开发人员的顾虑更少，管理人员看到的 bug 也更少。大家都很开心。

它还简化了整个测试过程。当开发人员需要创建一个 React 组件时，他们会创建一个包含所有可能属性的 Uibook 页面。您可以在编写内部业务逻辑(智能组件、商店或传奇)之前开始测试 UI。并且您可以部署组件，而无需将其导入到主应用程序中。

其他开发人员使用本地或生产 Uibook 审查组件。他们可以点击所有的按钮，并检查它调用回调。

Amplifr 的前端开发人员 Damir Melnikov 喜欢 Uibook 如何改善设计师和编辑之间的沟通过程:

> Uibook 允许我快速创建和修改组件。我可以检查新的样式，检查移动版本，检查不同边界条件下的组件。此外，Uibook 允许您立即与设计师、内容编辑和其他前端开发人员共享您的工作。

Amplifr 的内容主管 Alexander Marfitsin 注意到 Uibook 的界面文本编写过程:

> 在编写界面文本时，你经常盲目地工作，看不到“活的”产品中的内容会是什么样子。Uibook 解决了这个问题。通过在真实的界面中尝试，您可以校对现有组件和新组件中的内容。所有文本元素都是可编辑的，因此您可以获得从标题到最小标签的完整结果。
> 
> 此外，您可以更好地理解产品以及好的文本对于 Uibook 界面的重要性。

⌘⌘⌘

我希望你决定尝试 Uibook，并且你可以直接看到它如何增强你的项目。如有疑问，请参考 Github 资源库中的[详细说明](https://github.com/vrizo/uibook/)。或者[发微博](https://twitter.com/vitaliirizo) / [发邮件给我](//mailto:kb@kernel-it.ru)。