# 如何在一个普通的 JavaScript 项目中使用字体很棒的图标

> 原文：<https://dev.to/joeattardi/how-to-use-font-awesome-icons-in-a-vanilla-javascript-project-5b5b>

这篇文章最初发布在我的博客上。

我最近在做一个普通的 JavaScript 项目，想添加一些字体很棒的图标。以前我在 React 或 Angular 项目中使用过字体很棒的图标，但从来没有在普通的 JavaScript 中使用过。事实证明这很容易。

## 安装包

首先，安装所需的软件包。需要`fontawesome-svg-core`包。除此之外，您只需要安装包含您想要使用的图标的图标包。在本例中，我们将安装常规图标和实心图标。

```
npm install --save @fortawesome/fontawesome-svg-core \
                   @fortawesome/free-regular-svg-icons \
                   @fortawesome/free-solid-svg-icons 
```

Enter fullscreen mode Exit fullscreen mode

## 使用图标

您需要从`@fortawesome/fontawesome-svg-core`导入两件东西:

*   图标的一般集合。图标必须先添加到库中，然后才能使用。
*   `icon`:生成图标数据的功能。

```
import { library, icon } from '@fortawesome/fontawesome-svg-core'; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，导入你想要的图标并添加到库中:

```
import { faCat } from '@fortawesome/free-solid-svg-icons';

library.add(faCat); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以创建图标数据，其中包含要插入到 DOM 中的 SVG 标记。您需要向`icon`函数传递两条信息:

*   `prefix`:图标前缀，由图标所在的图标集合决定(纯色或常规)。通常这将是固体的`fas`或常规的`far`。
*   `iconName`:图标名称(不带`fa`前缀)。

这些选项作为对象传递给`icon`函数。返回的对象有一个包含我们正在寻找的标记的`html`属性。然后我们可以将这个标记添加到 DOM:

```
const iconContainer = document.createElement('span');
iconContainer.innerHTML = icon({ prefix: 'fas', iconName: 'cat' }).html; 
```

Enter fullscreen mode Exit fullscreen mode

现在可以将`iconContainer`添加到文档中，您的页面中将会有一个猫图标！

```
someDiv.appendChild(iconContainer); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个更实质性的例子。在我的项目[表情按钮](https://github.com/joeattardi/emoji-button)中，我把我所有的图标都放在一个文件中，这个文件可以被导入到任何需要图标的地方。我的图标集包括来自实体库和常规库的图标。

```
import { library, icon } from '@fortawesome/fontawesome-svg-core';
import { faCat, faCoffee, faFutbol, faHistory, faMusic, faSearch, faTimes } from '@fortawesome/free-solid-svg-icons';
import { faBuilding, faFlag, faFrown, faLightbulb, faSmile } from '@fortawesome/free-regular-svg-icons';

library.add(
  faBuilding,
  faCat,
  faCoffee,
  faFlag,
  faFrown,
  faFutbol,
  faHistory,
  faLightbulb,
  faMusic,
  faSearch,
  faSmile,
  faTimes
);

export const building = icon({ prefix: 'far', iconName: 'building' }).html;
export const cat = icon({ prefix: 'fas', iconName: 'cat' }).html;
export const coffee = icon({ prefix: 'fas', iconName: 'coffee' }).html;
export const flag = icon({ prefix: 'far', iconName: 'flag' }).html;
export const futbol = icon({ prefix: 'fas', iconName: 'futbol' }).html;
export const frown = icon({ prefix: 'far', iconName: 'frown' }).html;
export const history = icon({ prefix: 'fas', iconName: 'history' }).html;
export const lightbulb = icon({ prefix: 'far', iconName: 'lightbulb' }).html;
export const music = icon({ prefix: 'fas', iconName: 'music' }).html;
export const search = icon({ prefix: 'fas', iconName: 'search' }).html;
export const smile = icon({ prefix: 'far', iconName: 'smile' }).html;
export const times = icon({ prefix: 'fas', iconName: 'times' }).html; 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以`import`这些图标中的任何一个，并将它们添加到我的 UI 中任何需要的地方。