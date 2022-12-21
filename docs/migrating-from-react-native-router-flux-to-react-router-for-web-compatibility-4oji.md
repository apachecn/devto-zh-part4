# 从 react-native-router-flux 迁移到 react-router 以实现 web 兼容性

> 原文：<https://dev.to/ziaulrehman40/migrating-from-react-native-router-flux-to-react-router-for-web-compatibility-4oji>

我们努力的一部分是[为 web 编译我们的 react-native 应用程序，所以我们可以在 react-native 中内置一个 PWA，它可以在 iOS、android 和 app 上运行，所有 3 个都有相同的代码库。](https://dev.to/ziaulrehman40/part-1-converting-react-native-app-to-react-native-web-react-pwa-in-monorepo-architecture-4nhi-temp-slug-9666105)

**我们做了这次迁移，尽可能保持界面与`react-native-router-flux`接近，因此应用程序**中需要的更改很少，在某些情况下，我们只需更改导入即可。

## 试图逃避更换

**我们在 RN(0.59.9)** 应用中使用的是`react-native-router-flux` v4.0，这个版本基于`react-navigation` v2。在为 web 编译时，我们显然需要兼容的库。虽然`react-navigation`在 v2 中没有正式支持 web，但我们尝试了一下，很多东西都工作正常，我们可以很好地导航。**但问题是，导航时没有改变 url 路径(以及其他一些问题)**。我们需要向用户显示网址，这样用户就可以用这些网址做书签、分享或任何他们想做的事情。

#### 我们升级试试？

[`react-navigation`【v3】现在正式支持 web](https://reactnavigation.org/docs/en/web-support.html)，虽然在早期阶段，已经为 web 支持做了很多工作，并且努力仍在进行中，[但是 web 部分的进展似乎有点慢](https://github.com/react-navigation/web)(在撰写本文时)，许多重要的可用性相关问题尚未解决，几乎没有努力解决这些问题。但是由于升级我们现有的库版本是最容易的，我们尝试了一下。

v4.1(在撰写本文时还在测试阶段)使用的是`react-navigation`的 v3，所以我们升级到了 v3，但问题仍然存在。像这样的问题:

*   没有网址路径，只有域名一直显示在所有网页上。
*   使用底部导航时，网页不可滚动。

因此，由于这种设置工作的可能性很大，我们决定只改变库并选择一个替代方案。

## 为什么是`react-router`？

**`react-router`是最成熟的支持本地和 web** 的库，虽然针对 web 和本地的包不同，但 API 几乎相同，这是一个巨大的优势，因为它允许我们有条件地导入(基于操作系统)并忘记其余的，因为 API 是相同的(您将在一分钟内看到这一点)。

对于跨平台兼容性来说，这也是一个很好的选择，因为与`react-navigation`不同，它只关心导航，为底部或顶部或任何导航构建 UI 不是它的工作，这减少了需要为 web 修复的部分，使兼容性变得更容易。

## 明白了

在我们开始转换之前，让我们先讨论一些问题:

1.  根据您的应用程序的大小，**这可能需要大量的返工，**我们花了近 2 周的时间来设置我们的应用程序，使其与这个新库完全兼容。
2.  您**不能为下一个路由**传递函数(回调等)或任何其他处于状态的可序列化对象。
3.  你将不得不从`react-navigation`开始重做你正在使用的任何非路由功能(我指的是`react-navigation`，因为`react-native-router-flux`基于此，它给我们的所有功能都基于`react-navigation`)。我所说的非路由特性是指为底部导航构建 UI 之类的特性。
4.  **为了保持 API 不变，我们将使用渲染属性模式**将初始属性/状态甚至 url 参数传播到任何特定路径上正在渲染的组件。

## 实际换算

首先，添加这些包:`react-router-dom`和`react-router-native`，确保它们的版本匹配。

现在，我们将需要编写几个特殊的文件，其中将包含平台特定的条件导入等。然后，我们将使用这些文件来导入相关的组件，而不是从实际的库中导入。你可以把它看作一种包装纸。

我们将采用增量方法，因此我们不会在 start 中删除路由器流量，而是保留它，直到我们完成整个过程。

让我们从魔术文件开始(你可以照原样复制这些)。

### 魔法文件

#### 1。`Router.js`文件:

```
import { Platform } from 'react-native'; 

const isWeb = Platform.OS === 'web'; 

const RouterPackage = isWeb ? require('react-router-dom') : require('react-router-native'); 

export const Router = RouterPackage.Router; 
export default RouterPackage; 
```

_ **免责声明:**这段(和其他一些)代码是从网上的某个地方截取的(可能是 github 的问题之类的)_。

把这个文件放在你喜欢的位置(我们暂时把它放在根目录下)。这个文件没有做任何花哨的事情，它只是有条件地导入正确的库，当它是 web 时，我们从`react-router-dom`导入，否则我们从`react-router-native`导入。

#### 2。`RouteHistory`文件:

为此，我们需要创建两个文件，首先是`RouteHistory.js` :

```
import { createMemoryHistory } from "history"; 
export default createMemoryHistory(); 
```

而对于 web 另一个文件，`RouteHistory.web.js` :

```
import { createBrowserHistory } from "history"; 
export default createBrowserHistory(); 
```

*如果您不知道这个`.web.js`扩展名是什么，并且您没有处理将编译成 web 的项目，您可以跳过 web.js 文件。简而言之，如果可以的话，在网上使用的是`.web.js`文件而不是`.js`文件。继续前进。*

**这些文件所做的只是创建一个历史对象**，我们将把它传递给`react-router`，以便根据平台使用合适的历史策略，`memoryHistory`在移动设备上，`browserHistory`在 web 上。

### 路线映射

现在我们已经准备好了魔术文件，现在我们将开始实际的转换过程。

对于路由定义，我创建了 3 个文件:

#### 1。`urls.js`文件:

这个文件最简单，它只包含 url 定义:

```
export default {
    applyJob: '/job',
    browse:   '/job/:job_id'
}; 
```

#### 2。`RouteActions.js`文件:

这个文件将用于导航到不同的路线，它将用类似于`import Actions from '../RouteActions'`的内容替换`import { Actions } from 'react-native-router-flux'`。因此，在保持 API 与 router-flux 的动作相同方面，它将走在前列。

```
import { Platform } from 'react-native';
import history      from './RouteHistory';
import urls         from './urls.js';
import { generatePath } from "react-router";

export default class RouteActions {
  static pop() {
    setTimeout(() => {
      history.goBack();
    }, 1);
    return null;
  }

  static base(componentProps = {}) {
    history.push('', componentProps);
  }

  static post(componentProps) {
    const url = generatePath(urls.post, {postId: componentProps.postId});
    history.push(url, componentProps);
  }

  static posts(componentProps = {}) {
    history.push(urls.posts, componentProps);
  }

  static refresh(componentProps = {}) {
    history.replace(history.location.pathname, componentProps);
  }

  static replace(route, componentProps = {}) {
    history.replace(route, componentProps);
  }

  static jump(route, componentProps) {
    history.push(route, componentProps);
  }
} 
```

请注意，有一些**额外的路线**，如`pop`、`refresh`、`replace`、`jump`和`base`，这些路线是在我们的应用程序从`react-native-router-flux`开始大量使用这些路线时实现的，我们试图尽可能多地维护接口，以匹配`react-native-router-flux`中的`Actions`。因此，我们必须实现这些功能。

其次，我们正在接收`componentProps`，并在 [`push`和`replace`函数](https://github.com/ReactTraining/history#navigation)中传递它们。这也是为了匹配`react-native-router-flux`中的`Actions`行为，你会在`Routes.js`文件描述中了解更多细节。

您可能还注意到，`componentProps`在所有其他路线中都是可选的，但在`post`功能/路线中不是。为什么？

这是因为我们期待一个对象**将被**传递，其中**将**至少包含`postId`，并且可以包含它想要传递给正在渲染的组件的任何其他道具。

花点时间尝试理解这个文件。然后继续。

#### 3。`Routes.js`文件:

**该文件将替换旧的`react-native-router-flux`路由定义**，无论它是在一个单独的文件中还是在`App.js`中混合，该新文件将替换那些路由定义，并将实际导入到`App.js`中，以呈现替代 router-flux 导入的路由。

为了简单起见，我们假设只有三条路线需要绘制:

1.  `/posts`(登录后是主页)
2.  `/post/:post_id`
3.  呈现主页的空/根路由

它看起来会像:

```
import React, {Component} from 'react';
import Routing, { Router } from './wrappers/Router/Router';
import history from './RouteHistory';
import urls from './urls.js';

//Screens
import Posts from './screens/Posts';
import Post from './screens/Post';
import Home from './screens/Home';

const { Route, Switch, Redirect } = Routing;

class Routes extends Component {
    render() {
        return (
            <Router history={history}>
                <Switch>
                    <Route exact path='' component={Home}/>
                    <Route exact path={urls.posts}
                        component={(props) => <Posts {...props} {...props.location.state}/>}
                    />
                    <Route exact path={urls.post}
                        component={(props) => <Post {...props} {...props.location.state}/>}
                    />
                    <Redirect to="/" />
                </Switch>
            </Router>
        );
    }
}

export default Routes; 
```

我们正在导入我们的三个屏幕，并基于 url 呈现它们，让我们进一步细分:

`Switch`是一个组件，用于根据某些条件渲染某些东西，当它找到第一个匹配时就停止寻找匹配，[你可以阅读文档了解更多信息。](https://reacttraining.com/react-router/web/guides/basic-components)

如果我们在根路径上，没有任何子路径，它将呈现`Home`组件，正如您在代码片段中看到的。

如果我们在`/posts`路线，它会渲染`Posts`组件，但那有点不同，它在这里使用的是[渲染道具图案](https://reactjs.org/docs/render-props.html)，我们来讨论一下为什么。

你可以注意到我们在渲染组件的时候展开了一些东西:

```
component={(props) => <Post {...props} {...props.location.state} {...props.match.params}/>} 
```

这是为了配合`react-native-router-flux`的导航界面(类似于

`Actions.post({postId: 100, someOthrProps: 'value'})`

1.  `{…props}`:这是从 react-router 传播的默认道具，包括`history`、`location`和`match`(可能更多)。详情请浏览[文档](https://reacttraining.com/react-router/web/guides/quick-start)。
2.  `{…props.location.state}`这将传播我们在用`Actions.post`路由时传递的任何值，就像`react-native-router-flux`所做的一样，[在它的文档中调用这个参数](https://github.com/aksonov/react-native-router-flux/blob/master/README2.md#usage)。所以从我们的例子`Actions.post({postId: 100, someOthrProp: 'value'})`来看，post 组件会收到两个道具`postId`和`someOthrProp`。*这是路由器通量的相同概念，如果你在这里阅读这篇关于替换通量的文档，我假设你已经研究过路由器通量，并且会理解我在这里谈论的内容。*

#### 让我们使用这个新的路由器

现在我们可以打开我们的`App.js`文件并开始使用这个路由器，就像这样(当然，它被简化了很多):

```
import React, {Component} from 'react';
import { View } from 'react-native';
import Routes from './Routes';

export default class App extends Component {
    render() {
        return (
            <View>
                <Routes/>
            </View>
        );
    }
} 
```

更不用说，你需要在这里去掉`react-native-router-flux`的导入和初始化部分。

#### 最后，替换进口:

在这个阶段，你的所有路由都将被中断，你将会很好地到达基本/主路由，并且任何链接或重定向都将抛出错误，这是因为我们已经从`App.js`移除了 flux 初始化，并且仍然尝试使用 flux(在其他组件中)进行路由，让我们从:
开始修复我们的导入

```
import { Actions } from 'react-native-router-flux' 
```

致:

```
import Actions from '../RouteActions'; //fix path obviously 
```

考虑到你已经在所有 3 个文件中映射了你的所有 URL，`urls.js`、`Routes.js`和`RouteActions.js`，大多数的路径应该是可行的。

*(这 3 个文件结构显然是可选的。)*

寻找和解决问题，并逐步检查和修复适当文件中的所有链接/重定向映射路径。

#### 我们完了吗？

如果你的应用程序中有`deeplinking`或通用链接，那就不一样了，在这种情况下可能需要做一些额外的工作。我们在这里不讨论这个问题。你得自己去弄清楚。

我们完了。

感谢您的耐心，如果这篇文章中的某些部分难以理解，请告诉我(我知道可能会有，但对于与`react-native-router-flux`合作过的人来说，这应该不难理解，但我仍然愿意接受改进建议。)_

干杯！