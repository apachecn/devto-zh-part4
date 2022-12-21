# 单页应用程序中的页面标题和 A11y(尤其是。React & Vue)

> 原文：<https://dev.to/s_aitchison/page-titles-and-a11y-in-single-page-applications-esp-react-vue-4ok8>

(这篇文章的一个版本只详细描述了 React 的实现，最初发布在[Up Your A11y:Handling Page Titles in React](https://www.upyoura11y.com/page-titles/))

## 为什么页面标题对可访问性很重要？

简单来说，用户无需输入或导航页面内容就可以立即获得页面标题。为了感受一下这种可访问性的含义，现在试着打开你的屏幕阅读器(如果你用的是 Mac，Cmd + F5 会切换 VoiceOver 的开关)。

您应该会发现，这个页面的标题会立即显示出来，同时还会显示其他相关的浏览器信息。如果您打开了其他选项卡，请尝试使用 tab 键或单击它们，看看标题在了解该选项卡是否包含您要查找的内容方面有多大用处。

很明显，一个简洁的、描述性的页面标题对于屏幕阅读器用户来说是至关重要的。

## 如何给你的页面加标题

W3C 在这方面有一些优秀的简单技巧，总结如下。

**每个网页的标题应该:**

*   识别网页的主题
*   在站点地图或搜索结果列表中脱离上下文阅读时有意义，例如通过屏幕阅读器
*   短的

**这也可能有助于标题:**

*   标识网页所属的站点或其他资源
*   在网页所属的站点或其他资源中是唯一的

通常，最重要/最具体的信息排在最前面也是更好的选择，例如

> 单页应用程序中的页面标题:开发社区

而不是:

> 开发社区:单页应用程序中的页面标题

## 页面标题和单页应用

在单页面应用程序中(比如 React 和客户端渲染的 Vue 应用程序)，页面标题永远不会改变，除非它被专门管理。这使得你的用户在没有输入和阅读你的内容的情况下，很难找到他们当前正在浏览的页面的线索。好消息是有简单的方法来轻松管理标题。

## 管理 React 中的页面标题

对于 React 应用程序，我们可以简单地实现 [React 文档标题](https://github.com/gaearon/react-document-title)组件。

对于应用程序中的每个页面，只需将呈现的组件包装在 DocumentTitle 组件中，并传入页面标题，例如

```
class ExamplePage extends React.Component {
  render() {
    return (
      <DocumentTitle title="Example Title: Example Website Name">
        <main>
          ...
        </main>
      </DocumentTitle>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果需要，您甚至可以将页面标题绑定到组件状态中的属性，例如，如果您需要在组件挂载时从 API 或其他地方加载页面标题。

```
class ExamplePage extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      pageTitle: 'Loading Topics: Example Website Name',
    }
  }

  componentDidMount() {
    // Complete any tasks you need to complete before 
    // establishing page title

    this.setState({ pageTitle: 'Topics: Example Website Name' });
  }

  render() {
    const { pageTitle } = this.state;
    return (
      <DocumentTitle title={pageTitle}>
        <main>
          ...
        </main>
      </DocumentTitle>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 管理 Vue 中的页面标题

使用 Vue-Router，您可以添加一个简单的挂钩来管理每次路由器位置改变时的页面标题。首先，您需要在元数据中为您的每条路线配置页面标题，例如:

```
{
  path: '/',
  name: 'Home',
  meta: { 
    title: 'Home: MyApp',
  }, 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果页面标题取决于路线本身的一些信息，这可以修改为:

```
{
  path: '/',
  name: 'Home',
  meta: { 
    title: route => { 
      /* return custom title based on route/store/whatever  */ 
    },
  }, 
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经配置好了页面标题，我们只需要向 vue-router 添加一些代码，以确保标题在路由改变后得到更新。

这可以通过为路由器定义 afterEach 操作来实现，如下:

```
router.afterEach((to, from) => {
  Vue.nextTick(() => {
    document.title = to.meta.title(to)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意使用了`Vue.nextTick()`，而不是在 afterEach 回调中直接设置页面标题。这是为了确保页面标题仅在路由器导航完成后更新(包括更新浏览器历史)，从而确保您的浏览器历史与页面标题保持同步。

## 简单有效

我在这里详细描述了两个流行框架的一些基本实现，但是如果你有另一个单页面应用框架的实现，请在下面随意评论！

正如你在上面看到的，实现页面标题管理的步骤可能非常简单，但是这样做的影响是巨大的。好的页面标题将有助于所有用户浏览你的应用程序，同时给你的页面一个专业的外观和感觉。

* * *

你觉得这篇文章有用吗？请考虑[给我买杯咖啡](https://www.buymeacoffee.com/mgkZuRU)，这样我就可以继续制作内容🙂