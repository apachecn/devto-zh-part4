# 从头开始构建一个用户界面，响应侧边栏和标题

> 原文：<https://dev.to/llorentegerman/building-a-ui-from-scratch-responsive-sidebar-and-header-443g>

这是`Building a UI from scratch`系列的第二篇文章:

*   第 1 部分:[基于 ReactJS](https://dev.to/llorentegerman/building-a-ui-from-scratch-based-on-a-design-with-reactjs-3l1e) 的设计，从头开始构建 UI。
*   第 2 部分:[从头开始构建 UI，响应式侧边栏和标题](https://dev.to/llorentegerman/building-a-ui-from-scratch-responsive-sidebar-and-header-443g)。
*   第 3 部分:[从头开始构建 UI，响应内容](https://dev.to/llorentegerman/building-a-ui-from-scratch-responsive-content-6b9)。

**现场演示**:【https://llorentegerman.github.io/react-admin-dashboard/ T2】

**储存库**:【https://github.com/llorentegerman/react-admin-dashboard】T2

## 响应式设计

目前，我们的 UI 没有响应，我们希望它看起来像这样:
[![](img/be9c056a6b654f1a381cd570a92c4910.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tuLylcxa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://g.recordit.co/QzcP9sRsKH.gif)

由于我们没有一个响应式设计可以遵循，我们将保持简单，只有一个断点在 **768px** 。所以任何小于`768px`的屏幕都会被认为是`mobile`。
`Sidebar`将被隔离，在这个组件上将包括:`Burger button`、`Desktop Sidebar`和`Mobile Sidebar`。
`SidebarComponent`对于`Desktop`已经解释过了。在本文中，我们将看到如何在响应式侧边栏中转换它。
在手机屏幕中(宽度< = 768px) `SidebarComponent`可能有两种不同的状态:`collapsed`(默认)或`expanded`。

### 倒塌了:

在这种状态下，整个侧边栏将被隐藏，然后`mainBlock` ( [参见 App.js](https://github.com/llorentegerman/react-admin-dashboard/blob/v1-1-0/src/App.js) )将填充整个屏幕宽度。
我们需要一个按钮来`expand``Sidebar`，我们将为此使用一个`BurgerIcon`(要复制汉堡图标[单击此处](https://github.com/llorentegerman/react-admin-dashboard/blob/v1-1-0/src/assets/icon-burger.js))。那个按钮将处于绝对位置，在`header` :
[![](img/8303679fe3f90185e662de0e791626e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DROzKSrG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/9Xg1mzv2/screenshotmobile1.png) 的上方

### 展开了

在这种状态下，我们将显示`Sidebar`和一个`outsideLayer`，它将用半透明的背景填充屏幕的其余部分，如果你点击它，`Sidebar`将被关闭:
[![](img/7c29f8fe7836392ff56c7e405e0a9878.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B7F__xYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/sgQfVfDN/screenshotmobile3.png)

#### [标题组件. js](#headercomponentjs)

由于`Burger button`将超过`header`，我们需要给`Header Title`添加一些`left-margin`来避免这种情况:
[![](img/12cc3b868774c74333e0c584712a3a35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lLwINeSR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/D0NcBYq0/screenshotmobile2.png)

这些是`HeaderComponent.js`新样式中最重要的部分，正如你所看到的，我已经包括了媒体查询，以应用一些特殊的移动屏幕样式:

```
name: {
    ...,
    '@media (max-width: 768px)': {
        display: 'none' // <--- don't show the name on mobile
    }
},
separator: {
    ...,
    '@media (max-width: 768px)': {
        marginLeft: 12, // <--- less separation on mobile
        marginRight: 12
    }
},
title: {
    ...,
    '@media (max-width: 768px)': {
        marginLeft: 36 <--- to avoid overlapping with Burger button
    },
    '@media (max-width: 468px)': {
        fontSize: 20 <--- new fontSize for small devices. 
    }
} 
```

我还为图标包装添加了新的样式。

[查看更改:HeaderComponent.js](https://github.com/llorentegerman/react-admin-dashboard/compare/v1-0-0...v1-1-0#diff-9e795f3f52ccd33b20abf31c022ec55f)

[查看完整文件:HeaderComponent.js](https://github.com/llorentegerman/react-admin-dashboard/blob/v1-1-0/src/components/header/HeaderComponent.js)

#### SidebarComponent.js

该组件包含所有逻辑，它将根据以下两个变量而变化:

*   `expanded`:存储在`state`中
*   `isMobile` : `true`当`window.innerWidth <= 768`

当`Sidebar`展开时，有两种不同的方法可以折叠它，点击某个`MenuItem`或者点击`outsideLayer`。管理这种行为有两种方法:

```
onItemClicked = (item) => {
    this.setState({ expanded: false });
    return this.props.onChange(item);
}

toggleMenu = () => this.setState(prevState => ({ expanded: !prevState.expanded })); 
```

当你点击`Burger button`(如果工具条被折叠)或者当你点击`outsideLayer`(如果工具条被展开)时`toggleMenu`将被触发。

这里是`SidebarComponent` :
的新版本