# 充当状态管理库

> 原文：<https://dev.to/aziziyazit/state-management-in-react-4op9>

## 状态

在用户界面(UI)的上下文中，状态是保存影响渲染输出的当前信息的数据。随着时间的推移，它发生了变化。

状态是用户定义，所以它不仅仅与组件或 UI 控件相关。

## 状态管理库

状态管理指的是管理 UI 状态。随着应用程序的增长，这可能会成为 UI 开发中最复杂的问题之一。

状态管理库为我们提供了存储状态的能力，并为跨应用程序的 UI 组件提供了访问、添加和更新状态的 API。状态管理库的例子是 React(这不是打字错误)和 Redux。

[![](img/fb85d67454c2d755842b26ad87d53b33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G7l8frI8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tc2tdn7eurdhzej3bwle.png)

### Redux

Redux 试图通过对更新发生的方式和时间施加某些限制来使状态突变可预测。这些限制反映在 Redux 的三个原则中(真值的单一来源、状态是只读的和用纯函数进行更改)。

### 反应为状态管理库

> 如果您正在使用 React 构建一个应用程序，那么您的应用程序中已经安装了一个状态管理库。你甚至不需要 npm 安装(或纱添加)它。它不需要为你的用户花费额外的字节，它与 npm 上的所有 React 包集成，并且它已经被 React 团队很好地记录了。它自己起反应了。
> ~肯特·c·多兹

现在的问题是，我们需要 Redux 吗？
你可以在 React doc 中找到答案

[![](img/13b142651415de0dd0c8f39bc749d0f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2bbG-Uqi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ukrwc7xxe2t5fi9ydmxr.png)

选择正确的状态管理库是一个非常关键的决定。要为您的场景选择最佳方案，需要了解“状态”

### 组件状态

当地使用的州。

例如，按钮组件使用其单击状态来显示加载器和图标(加载器和图标都是按钮的子组件)

按钮组件可以在这里用[表示](https://codepen.io/joshuaward/pen/oGZrWN)

使用 useState 钩子可以很容易地管理组件状态。

### 模块状态

在模块范围内跨不同组件使用的状态，它们(组件)被调用。

例如具有表格组件和分页组件的列表模块。表使用分页页面选择状态来更改其数据显示。

清单模块可以在[这里](https://codepen.io/yasser-mas/pen/pyWPJd)举例说明

为了管理模块状态，[我们可以使用带有 useReducer](https://dev.to/aziziyazit/create-module-in-react-5gg) 的上下文 API

### 页面状态

在页面范围内跨不同模块使用的状态。

例如登录模块和注册模块，可以根据用户的交互来切换它们的按钮

登录/注册模块可以在这里用[表示](https://codepen.io/hk95/pen/Kyrvrj)

React 有可靠的技术来管理名为[的页面中的状态，提升状态](https://dev.to/aziziyazit/create-page-in-react-1mj6)

到目前为止，我们使用**React**管理 3 种状态(组件、模块和页面)都没有问题。左边是另一种状态，**全局状态**。

### 全局状态

> 用户在登录页面提交用户名和密码(本地状态),登录状态(本地状态)用于显示加载程序，并使用 userId(全局状态？)一路走过。菜单模块用来获取用户角色并根据提供的角色显示菜单项的 userId。

**userId** 可以被认为是全局状态吗？
不会。因为它不会随着时间而改变，可以作为道具传递。

像 **userId** 等数据(同类型)被错误归类为全局状态。

> 如果你的数据从不改变，你就不需要 Redux。它的全部意义在于管理变化。
> ~丹·阿布拉莫夫

当钻柱问题可以通过使用 React(上下文 API)解决时，我认为使用 Redux 唯一有用的事情就是管理全局状态。但是当你的应用程序是由提供者(模块)组成的时候，它们有自己的缩减器来管理模块范围内的状态，看起来我们不需要全局状态。换句话说，我们不再需要 Redux 了。

在这里可以看到大量的信息、技巧和解决方案。参与讨论的人有 Kent C Dodds、Mark Erikson (Redux 维护者)、Brian Lonsdorf 和许多其他伟大的 React 开发人员。