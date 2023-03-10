# 如何在 React 中使用故事书

> 原文：<https://dev.to/tducasse/how-to-use-storybook-with-react-10g1>

*简化您的 UI 组件开发工作流程*

## 什么是故事书，我为什么要用它？

来自 [Storybook 的网站](https://storybook.js.org/)本身:

> Storybook 是一个用于独立开发 UI 组件的开源工具

### 可复用组件

React 允许我们编写我们称之为“可重用组件”的东西。如果您不知道什么是可重用组件，请以按钮为例:

*   您可以有不同的变体:
    *   红色背景的`primary`按钮
    *   绿色背景的`secondary`按钮
*   还可以有不同的状态:如果表单正在发送，按钮可以是`disabled`

在 React 中，处理它的一个非常简单的方法是使用一个接受不同参数的`Button`组件:

*   一个叫`disabled`的道具，不是`true`就是`false`
*   一个叫`variant`的道具，不是`primary`就是`secondary`。

但是假设你正在写这个代码，你想看看它是什么样子的。通常的方法是进入你的应用程序的一个页面，然后在中间的某个地方包含这个按钮，向它传递道具，然后看看它看起来像什么。

### 来了故事书

这就是 Storybook 发挥作用的地方:它基本上允许您并行运行第二个应用程序，您可以使用按钮组件，而不必将其包含在您的应用程序中。你可以**独立开发你的组件**。

现在，假设支持团队中的某个人正在与一个无法登录的客户交谈。他们过来问你:“嘿，你能给我看看这个错误屏幕吗？”。

如果没有 Storybook，答案包括必须启动应用程序，尝试重放用户所做的事情，阅读一些代码以理解如何让它出现，等等。

有了 Storybook，你只需在搜索栏中输入“错误屏幕”，你就能立即看到它！

## 在 React 应用中设置故事书

此时，您将需要一个 React 应用程序。如果你没有，可以随意克隆这个，或者按照[这篇文章](https://dev.tothis%20article)中的步骤来创建一个。本教程将假设你使用[创建-反应-应用](https://github.com/facebook/create-react-app)。

Storybook 使设置一切变得非常容易。在您的终端中，只需运行:

```
npx -p @storybook/cli sb init 
```

这将基本上检查您的`package.json`以确定您正在使用的框架，然后为您的项目生成正确的配置。

该命令应该已经更新了您的`package.json`脚本，添加了:

```
"storybook":  "start-storybook -p 9009 -s public",  "build-storybook":  "build-storybook -s public" 
```

第一个是我们感兴趣的。运行:

```
npm run storybook 
```

这应该会在您的浏览器中打开类似这样的内容(如果没有，只需导航到`localhost:9009` ):
[![storybook-home.png](img/857f06b0a880b55b009200d5f2ca8a99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qMSZrTqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039162383/EMeJv4krE.png)

让我们看看这里的界面:

*   左边是侧边栏:这是你找到组件的地方。点击`Button`，看看有什么！
*   在底部，一些看起来像控制台的东西:这实际上是“插件”部分。Storybook 有很多插件，可以让你在开发组件时增强体验:动态改变道具，日志输出，切换语言等。

那么这些组件来自哪里呢？当我们安装 Storybook 时，它会生成这些“演示”故事。他们在`src/stories/index.js` :

```
import React from "react";

import { storiesOf } from "@storybook/react";
import { action } from "@storybook/addon-actions";
import { linkTo } from "@storybook/addon-links";
// Importing the demo components from storybook itself
import { Button, Welcome } from "@storybook/react/demo";

storiesOf("Welcome", module).add("to Storybook", () => (
  <Welcome showApp={linkTo("Button")} />
));

storiesOf("Button", module)
  .add("with text", () => (
    <Button onClick={action("clicked")}>Hello Button</Button>
  ))
  .add("with some emoji", () => (
    <Button onClick={action("clicked")}>
      <span role="img" aria-label="so cool">
        😀 😎 👍 💯
      </span>
    </Button>
  )); 
```

将它们添加到故事书的魔力在`.storybook/config.js` :

```
import { configure } from '@storybook/react';

function loadStories() {
  require('../src/stories');
}

configure(loadStories, module); 
```

## 写出你的第一个故事

### 配置故事书

我们需要做的第一件事是去掉这些演示故事，并改变故事加入故事书的方式。完全删除`src/stories/`文件夹，我们不需要它。

将`.storybook/config.js`中的所有内容替换为:

```
import { configure } from '@storybook/react';

const req = require.context('../src/', true, /\.stories\.js$/);

function loadStories() {
  req.keys().forEach(filename => req(filename));
}

configure(loadStories, module); 
```

这将告诉 Storybook 选择每一个以`.stories.js`结尾的文件。您将会看到(一般来说)让故事靠近它们测试的组件要容易得多。

### 一个简单的按钮组件

现在让我们写我们的第一个故事。如果您使用的是我的 github 示例，请转到`src/components/atoms`并创建以下文件:

```
|––atoms
  |––Button
    |––index.js
    |––Button.js
    |––Button.stories.js 
```

`Button.js` :

```
import React from "react";

const Button = props => {
  const { variant, disabled, children } = props;

  // This is the default style
  let backgroundColor = "white";
  let color = "black";

  // Which variant do we want?
  switch (variant) {
    case "primary":
      backgroundColor = "red";
      color = "white";
      break;
    case "secondary":
      backgroundColor = "green";
      color = "white";
      break;
    default:
      break;
  }

  // Let's build the style based on the variant
  // We also add properties depending on the `disabled` state
  const style = {
    backgroundColor,
    color,
    cursor: disabled ? "not-allowed" : "pointer",
    opacity: disabled ? 0.5 : 1
  };

  return (
    <button disabled={disabled} style={style}>
      {children}
    </button>
  );
};

export default Button; 
```

`Button.stories.js` :

```
import React from "react";
import { storiesOf } from "@storybook/react";
import Button from "./Button";

// You can see this as "folders" in Storybook's sidebar
const stories = storiesOf("atoms/Button", module);

// Every story represents a state for our Button component
stories.add("default", () => <Button>Button</Button>);
stories.add("default disabled", () => <Button disabled>Button</Button>);
stories.add("primary", () => <Button variant="primary">Button</Button>);
// Passing a prop without a value is basically the same as passing `true`
stories.add("primary disabled", () => (
  <Button variant="primary" disabled>
    Button
  </Button>
));
stories.add("secondary", () => <Button variant="secondary">Button</Button>);
stories.add("secondary disabled", () => (
  <Button variant="secondary" disabled>
    Button
  </Button>
)); 
```

`index.js` :

```
// This allows us to import `src/components/Button` directly,
// without having to go all the way to `src/components/Button/Button`
export { default } from "./Button"; 
```

现在再去故事书，看看你的故事创造了什么:
[![storybook-button.png](img/906fc09f608807c81f24d6a29d2b4641.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzAcUsQ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039170087/YVsdE-HUs.png)

浏览我们创建的不同故事，注意按钮是如何变化的。

### 一切都是实时的

Storybook 有一个*非常*快速热重装机制。这意味着你可以转到你的组件，将“红色”改为蓝色，Storybook 立即重新编译你的故事，以包含你的变化！

## 插件

Storybook 提供了[各种非常方便的插件](https://storybook.js.org/addons/)来帮助我们自信地独立开发组件。让我们设置其中的一些。

### 附加信息

有时候当你浏览故事书的时候，你想读一个特定故事的代码。这正是`info`插件所做的。要安装它:

```
npm i -D @storybook/addon-info 
```

通过编辑`.storybook/config.js` :
全局添加插件

```
...
import { addDecorator } from '@storybook/react';
import { withInfo } from '@storybook/addon-info';

addDecorator(withInfo);
... 
```

这将在你的故事右上角增加一个`show info`按钮，显示关于故事的一些信息:
[![addon-info.png](img/5fd2d78d763e8a7272afc8154326e0a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4wmT96WT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039243071/-ifi4_la0.png)

### 插件-动作

当一个动作在我们的组件中发生时，记录它可能是有用的。比方说，我们修改了按钮组件，使其接受一个`onClick`属性:

```
...
const Button = props => {
  const { variant, disabled, children, onClick } = props;
  ...
return (
    <button onClick={onClick} disabled={disabled} style={style}>
      {children}
    </button>
  ); 
```

我们如何测试点击按钮会触发`onClick`处理程序？Storybook 提供了一个已经安装的官方插件，可以帮助解决这个问题。在你的故事中，导入`action`，然后添加`onClick`道具:

```
import { action } from "@storybook/addon-actions";
...
stories.add("default", () => (
  <Button onClick={action("clicked!")}>Button</Button>
));
stories.add("default disabled", () => (
  <Button onClick={action("clicked!")} disabled>
    Button
  </Button>
));
stories.add("primary", () => (
  <Button onClick={action("clicked!")} variant="primary">
    Button
  </Button>
));
stories.add("primary disabled", () => (
  <Button onClick={action("clicked!")} variant="primary" disabled>
    Button
  </Button>
));
stories.add("secondary", () => (
  <Button onClick={action("clicked!")} variant="secondary">
    Button
  </Button>
));
stories.add("secondary disabled", () => (
  <Button onClick={action("clicked!")} variant="secondary" disabled>
    Button
  </Button>
)); 
```

现在，每次点击按钮，Storybook 都会打印一个新的日志:
[![clicked.png](img/d2255d1ff1f64b69db4b05c5db48ba65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ecbj7Ai6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039262725/WLV9ZKEN4.png)

### 附加按钮

现在，我们需要为同一个组件编写许多不同的故事，因为我们需要处理每个道具组合。如果我们能在故事书里实时编辑道具会怎么样？解决方案是[附加按钮](https://github.com/storybookjs/storybook/tree/next/addons/knobs)，它极大地简化了我们写故事的方式。

首先，用
安装插件

```
npm i -D @storybook/addon-knobs 
```

然后，将其添加到`.storybook/addons.js` :

```
import '@storybook/addon-actions/register';
import '@storybook/addon-links/register';
// add this line
import '@storybook/addon-knobs/register'; 
```

重写你的故事来使用新的插件:

```
import React from "react";
import { storiesOf } from "@storybook/react";
import Button from "./Button";
import { action } from "@storybook/addon-actions";
// add this line
import { withKnobs, select, boolean } from "@storybook/addon-knobs";

// You can see this as "folders" in Storybook's sidebar
const stories = storiesOf("atoms/Button", module);

// add this line
stories.addDecorator(withKnobs);

// ---- add this block
const variantOptions = {
  none: "",
  primary: "primary",
  secondary: "secondary"
};
// ----

stories.add("with knobs", () => (
  <Button
    onClick={action("clicked!")}
    // ---- and this one
    // syntax is (name, options, default)
    variant={select("variant", variantOptions, "")}
    // syntax is (name, default)
    disabled={boolean("disabled", false)}
    // ----
  >
    Button
  </Button>
)); 
```

现在，当你进入你的故事，在插件部分，你可以看到一个新的标签，叫做旋钮，你可以通过玩它们来改变你的组件的道具:
[![knobs.png](img/d3f21d5ab4ec5e4c747bbb8d2bdde809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SHSepFHT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039273640/JeDLmitXG.png)

更酷的是`addon-info`和这些道具保持同步！
[![knobs-sync.png](img/b94f2eec874e52459d05bb87d5ccc94d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xhbv4guT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039288563/OZ9Tqm4xD.png)

## 快照测试

因为 React 组件是可重用的，所以一个组件包含在许多其他组件中是很常见的。跟踪组件成为依赖项的所有地方并估计一个小变化的影响会变得非常困难。Storybook 使得设置**快照测试**，结合 [jest](https://jestjs.io/) 变得非常容易(create-react-app 已经自带)。

首先，安装所需的依赖项:

```
npm i -D @storybook/addon-storyshots react-test-renderer require-context.macro 
```

然后，在`.storybook/config.js` :

```
import requireContext from 'require-context.macro';

// const req = require.context('../src', true, /\.stories\.js$/); <-- replaced
const req = requireContext('../src', true, /\.stories\.js$/); 
```

在`src`中创建以下结构:

```
|––test
  |––storyshots.test.js 
```

并把这个加到`storyshots.test.js`

```
import initStoryshots from '@storybook/addon-storyshots';

initStoryshots(); 
```

最后，运行`npm run test`(或`npm test`简写)。这将在`src/test/__snapshots__/storyshots.test.js.snap`创建一个快照文件。

现在，当您运行测试时，Storybook 将呈现每个故事，并将其与之前创建的快照进行比较。尝试更改按钮组件中的某些内容，然后再次运行测试，例如:

```
switch (variant) {
    case "primary":
      backgroundColor = "red";
      color = "white";
      break;
    case "secondary":
      // change this...
      //backgroundColor = "green";
      // ...into this
      backgroundColor = "gray";
      color = "white";
      break;
    default:
      break;
  } 
```

Jest 会抱怨快照不对，给你一个很有用的报告:
[![snapshot-failed.png](img/e4d272d0e00d9415e838c793b8d1c598.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P46EdGXH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1561039306247/vMBUxMic4.png)

您可以检查更改，并确定您是否破坏了某些东西，或者每个更改是否都是有意的。如果一切正常，您可以使用:
更新快照

```
npm run test -- -u 
```

在开发一个大的特性之后运行快照测试对于回顾您所做的工作和您的变更的影响是非常有帮助的。

*在 [GitHub](https://github.com/tducasse/setup-storybook) 上找到最终代码。*