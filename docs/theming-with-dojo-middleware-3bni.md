# 使用 Dojo 中间件进行主题化

> 原文：<https://dev.to/odoenet/theming-with-dojo-middleware-3bni>

最近，我们看了 Dojo 中可用的新的[小部件中间件](https://learn-dojo.com/dojo-widget-middleware/)。除了维护小部件状态或应用程序商店，您还可以使用`theme`中间件来[定制您的小部件](https://dojo.io/learn/styling/introduction)。

让我们举一个[之前的例子](https://codesandbox.io/s/dojo-function-based-widgets-94eyy)，它使用了基于功能的小部件，并为其添加了一个主题。

## 主题

主题是构建可供他人使用的应用程序和 widgts 的好方法。你可以建立一个[小部件库](https://github.com/dojo/cli-build-widget#building)与他人分享。您可以发布带有默认通用主题的小部件，但也允许其他人提供他们自己的主题。这使得开发人员可以为博客、仪表盘或其他应用程序重新设计小部件和应用程序，并让它们使用统一的主题。

你甚至可以让你的应用程序的用户选择他们的主题，比如提供一个黑暗和光明的主题。

为了给我们自己的应用程序添加主题，我们可以添加一个带有`dark`和`light`文件夹的`themes`文件夹来组织主题。然后我们可以为每个主题化小部件添加一个`Users`文件夹。为了使 css 作为主题可用，我们需要创建一个模块来导出我们的主题。

```
/* src/themes/dark/Users/theme.ts */
/* src/themes/light/Users/theme.ts */
import * as css from "./Users.m.css";

export default {
  "dojo-function-based-widgets-themeable/Users": css
}; 
```

Enter fullscreen mode Exit fullscreen mode

要导出主题，您需要导出一个带有[小部件主题关键字](https://dojo.io/learn/styling/working-with-themes#widget-theme-keys)和引用的 css 的对象。注意格式如下。

`{package-name}/{widget-css-module-name}: {css}`

现在，我们可以更新我们的小部件，使其主题化。

## 主题化小工具

这是最初的小部件的样子。

```
// src/widgets/Users/Users.tsx
...
export default render(function Users({ middleware: { store } }) {
  const { get, path, executor } = store;
  const users = get(path("users"));
  if (!users) {
    executor(fetchUsersProcess)(null);
    return <em>Loading users...</em>;
  }

  return (
    <div classes={[css.root]}>
      <h1>Users</h1>
      <ul classes={[css.list]}>{userList(users)}</ul>
    </div>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用`theme`中间件来更新它。

```
import { create, tsx } from "@dojo/framework/core/vdom";
// theme middleware
import theme from "@dojo/framework/core/middleware/theme";
import icache from "@dojo/framework/core/middleware/icache";

// dojo theme and dojo checkbox
import dojoTheme from "@dojo/themes/dojo";
import Checkbox, { Mode } from "@dojo/widgets/checkbox";

// base css and themes
import * as css from "./Users.m.css";
import dark from "../../themes/dark/theme";
import light from "../../themes/light/theme";

...

// add the theme middleware to the widget
const render = create({ icache, store, theme });

...

export default render(function Users({ middleware: { icache, store, theme } }) {
  const { get, path, executor } = store;
  const users = get(path("users"));
  if (!users) {
    executor(fetchUsersProcess)(null);
    return <em>Loading users...</em>;
  }
  const checked = icache.getOrSet("checked", false);
  // if no theme set, default to the light theme
  if (!theme.get()) {
    theme.set(light);
  }
  // extract the themed css to use
  const themedCss = theme.classes(css);
  return (
    <div classes={[themedCss.root]}>
      <Checkbox
        theme={dojoTheme}
        mode={Mode.toggle}
        checked={checked}
        onChange={() => {
          // use checkbox to toggle theme
          icache.set("checked", !checked);
          if (!checked) {
            theme.set(dark);
          } else {
            theme.set(light);
          }
        }}
      />
      <h1>Users</h1>
      <ul classes={[themedCss.list]}>{userList(users, themedCss)}</ul>
    </div>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里有点不对劲。我们正在增加一些新的进口商品。

```
// theme middleware
import theme from "@dojo/framework/core/middleware/theme";
import icache from "@dojo/framework/core/middleware/icache";

// dojo theme and dojo checkbox
import dojoTheme from "@dojo/themes/dojo";
import Checkbox, { Mode } from "@dojo/widgets/checkbox";

// base css and themes
import * as css from "./Users.m.css";
import dark from "../../themes/dark/theme";
import light from "../../themes/light/theme";

const render = create({ icache, store, theme }); 
```

Enter fullscreen mode Exit fullscreen mode

我们为 Dojo 复选框添加了`theme`中间件和`icache`,这样我们就可以切换主题。然后我们导入基本的 css 和我们的明暗主题。然后，我们将这些作为中间件提供给基于功能的小部件。

然后我们需要在小部件中使用它们。

```
export default render(function Users({ middleware: { icache, store, theme } }) {
  ...
  const checked = icache.getOrSet("checked", false);
  // if no theme set, default to the light theme
  if (!theme.get()) {
    theme.set(light);
  }
  // extract the themed css to use
  const themedCss = theme.classes(css);
  return (
    <div classes={[themedCss.root]}>
      <Checkbox
        theme={dojoTheme}
        mode={Mode.toggle}
        checked={checked}
        onChange={() => {
          // use checkbox to toggle theme
          icache.set("checked", !checked);
          if (!checked) {
            theme.set(dark);
          } else {
            theme.set(light);
          }
        }}
      />
      <h1>Users</h1>
      <ul classes={[themedCss.list]}>{userList(users, themedCss)}</ul>
    </div>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过使用`theme.set(customTheme)`切换主题，然后使用`const themedCss = theme.classes(css)`让我们的主题 css 类应用到小部件。这个`themedCss`就是我们可以用来将我们的 css 类名应用到小部件的东西。

你可以在下面看到结果。使用切换按钮在亮色和暗色主题之间切换。

[https://codesandbox.io/embed/dojo-function-based-widgets-themeable-k8913?module=/src/widgets/Users/Users.tsx](https://codesandbox.io/embed/dojo-function-based-widgets-themeable-k8913?module=/src/widgets/Users/Users.tsx)

一旦你理解了这个模式，将主题应用到你的小部件上会很有趣。

如果你想在自己的应用程序中提供明暗主题，你甚至可以使用 Mac OS 用户设置的偏好。你可以用 [`matchMedia`](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) 来检测`prefers-color-scheme`。

```
if (window.matchMedia("(prefers-color-scheme: dark)").matches) {
  theme.set(dark);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 要记住的事情

总的来说，要真正理解 Dojo 中的主题对我来说太难了，我想我终于很好地理解了它。

以下是一些需要记住的事情。

*   主题类名必须与小部件默认类名相匹配。
    *   主题将只应用于你的小部件的默认`css`中的类名，即使它们是空的，也要确保它们匹配。
*   `{package-name}/{widget-css-module-name}` -这是 [widget 主题键](https://dojo.io/learn/styling/working-with-themes#widget-theme-keys)。
    *   这让我有点迷惑，感谢 [discord](https://discord.gg/M7yRngE) 上的道场团队帮我抓住了这个！
*   使用一个`variables.css`来维护[公共主题属性](https://dojo.io/learn/styling/introduction#abstracting-common-theme-properties)。
*   阅读[文档](https://dojo.io/learn/styling/introduction)。
    *   它写得非常好，包含了你需要的所有细节。

## 总结

对小部件进行主题化会很有趣，你会发现自己陷入了一个调整和 css 黑客的兔子洞，做出了一些非常酷的东西。别忘了你可以使用 [`@dojo/cli-create-theme`](https://dojo.io/learn/styling/working-with-themes#scaffolding-themes-for-third-party-widgets) 为`@dojo/widgets`搭建主题。这将让您挑选想要应用主题的小部件。

构建主题化小部件还允许您构建更多可重用的小部件。这样你就可以把你的小部件放到任何其他的应用程序中，并且快速提供新的主题，而不需要花费太多的精力从零开始！