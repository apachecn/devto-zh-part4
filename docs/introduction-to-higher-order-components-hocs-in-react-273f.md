# React 中的高阶组件(hoc)介绍

> 原文：<https://dev.to/ogwurujohnson/introduction-to-higher-order-components-hocs-in-react-273f>

> 过去的一周让我学到了很多东西，很多我很想分享的概念。我将从讨论 react 中的高阶函数开始。

为了能够理解 react 中的高阶组件，我们首先需要理解 JavaScript 中的高阶函数是什么。Javascript 中的高阶函数是以其他函数为参数，返回另一个函数的函数。高阶函数的例子有:`.map`、`.filter`等。我在这篇文章中提到了其中的一些:

[![ogwurujohnson](img/5a9b1758658be88756f92622cacdc96a.png)](/ogwurujohnson) [## 。forEach()，。map()，。过滤器()....有什么区别？

### 约翰逊·奥格武鲁 5 月 11 日 181 分钟阅读

#javascript #programming #tutorial](/ogwurujohnson/foreach-map-filter--whats-the-difference-304l)

> 需要注意的是，我假设你事先了解 React，如果你不了解，下面是 traverse media 提供的一个很棒的教程。撇开那件事，让我们直接开始吧。

[https://www.youtube.com/embed/sBws8MSXN7A](https://www.youtube.com/embed/sBws8MSXN7A)

高阶组件是将另一个组件或多个组件作为参数并返回新组件的组件。可以比作高阶函数，这就是这个名字的由来。这是 react 中重用组件逻辑的一种高级技术。当我们想从站点的其他组件中抽象出一个登录组件时，可以使用 HOC，我将演示如何实现。hoc 通常用作高级的 react 模式，用于设计一些共享某些行为的组件，使它们的连接方式不同于普通的 state-> props 模式。HOCs 作为一种高级的 react 模式，允许我们重用组件逻辑。想想我提到的登录组件的功能。登录可能只是一个表单，它将用户名和密码作为值，并根据数据库或任何存储方式执行检查(如果用户存在于系统中)。如果用户存在，并且输入的密码是正确的，那么应用程序组件将呈现内容组件，但是如果用户不存在，应用程序将呈现登录组件，这同样适用于检查站点上的用户是否仍然具有有效的身份验证。该示例可以使用高阶组件来执行。但是如何，下面的代码演示了如何。

假设我们有一个这样的应用组件

```
 import React, { Component } from 'react'

   //below is the app component that would render to the DOM
   export default class App extends Component {
     render() {
       return (
          <div>
            <h1> Hello world </h1>
          </div>
       )
     }
   } 
```

Enter fullscreen mode Exit fullscreen mode

要创建一个更高阶的组件来执行我们想要的功能，即根据用户登录会话的状态来决定呈现哪个组件，我们首先必须创建两个组件。

> 请注意，为了使这个解释尽可能简单，我将在 app.js 文件中创建所有组件。此外，我们将利用创建-反应-应用程序，请设置它

下面，我们将创建登录和内容组件，这两个组件实际上都是虚拟组件，只是在一个`h1`标签中调用它们的名字。

```
 import React, { Component } from 'react'

   // Login Component
   export default function Login {
      return (
         <div>
           <h1>Hi i'm login</h1>
         </div>
      )
   }

   //Content component
   export default function Content {
       return (
          <div>
            <h1>Hi i'm Content</h1>
          </div>
       )
   }

   //App component
   export default class App extends Component {
     render() {
       return (
          <div>
            //Add component to render here
          </div>
       )
     }
   } 
```

Enter fullscreen mode Exit fullscreen mode

构建完各个组件后，是时候创建我们的 HOC 了。

> 请注意，您可以选择在不同的文件中创建它，但是为了使这篇文章简单明了，我将从一个文件中做所有的事情。

将下面的代码复制并粘贴到紧挨着`Content`组件的下面和`App`组件的前面。

```
 const withAuthentication = Component1 => Component2 =>
     class extends Component {
       constructor(props) {
         super(props);
         this.state = {
          isLoggedin: false,
         };
       }

       render(){
          if(this.state.isLoggedin) {
            return <Component1 />
          } else {
            return <Component2 />
          }

       } 
```

Enter fullscreen mode Exit fullscreen mode

上面是最简单的高阶组件。

> 我们注意到`HOCs`是如何命名的，这一点至关重要，一般认为它们是以`with`开始命名的，然后不管它们的具体工作是什么，在我们的例子中是身份验证。这样，任何看你代码的人不用看代码就知道它是一个高阶组件，这在`HOC`放在一个单独的文件中时尤其重要。

来解释代码。我们使用 JavaScript 中被称为`curring`的东西来接收提供给`HOC`的参数。当我们把这两个参数原样传递给`HOC`时，我们就是这样做的；`Component1 => Component2 =>`，实际上相当于我们声明了一个方法，并给它传递了一个参数，正常的 Javascript 方式。

再往下，HOC 返回一个匿名类，该类有一个 state 对象，包含了`isLoggedin`属性，在开发应用程序的过程中，您可以随意操作该属性，以确定用户何时登录，何时未登录。

进一步深入到`render()`方法，我们添加了一个条件来检查`isLoggedin`的值的状态，因此无论值是什么，都分别返回一个组件。

现在，你脑海中的问题很明显是你如何使用它。密切注意。将下面的代码直接复制粘贴到我们刚刚写的`HOC withAuthentication()`下面。

```
 const DoAuthenticate = withAuthentication(Content, Login); 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过将我们的`HOC withAuthenticate`赋值给函数`DoAuthenticate`，我们将在 HOC 中返回的函数设置为`DoAuthenticate`，从它的名字来看，它是一个组件。

> 由于我们的 HOC 是如何组织的，我们需要确保`Content`组件停留在`HOC`中的`Component1`位置，因为我们希望它在用户登录时返回，并且`Login`组件占据`HOC`中定义的`Component2`的位置。

现在，我们不必考虑通过应用程序组件将哪个组件呈现给`DOM`，我们只需呈现`DoAuthenticate`，并让`HOC`为我们考虑。最后，我们的代码应该是这样的。

```
 import React, { Component } from 'react'

   // Login Component
   export default function Login {
      return (
         <div>
           <h1>Hi i'm login</h1>
         </div>
      )
   }

   //Content component
   export default function Content {
       return (
          <div>
            <h1>Hi i'm Content</h1>
          </div>
       )
   }

   // Higher order Component
   const withAuthentication = Component1 => Component2 =>
     class extends Component {
       constructor(props) {
         super(props);
         this.state = {
          isLoggedin: false,
         };
       }

       render(){
          if(this.state.isLoggedin) {
            return <Component1 />
          } else {
            return <Component2 />
          }

       }

   const DoAuthenticate = withAuthentication(Content, Login);

   //App component
   export default class App extends Component {
     render() {
       return (
          <div>
            <DoAuthenticate />
          </div>
       )
     }
   } 
```

Enter fullscreen mode Exit fullscreen mode

> 学习 HOCs 对我来说真的很有趣，我在一周内申请了两个应用程序，以加深我对它的理解。以下是应用程序的链接。

Instagram 应用程序的一个克隆(虽然不是一个完整的克隆...lol)和 [LambdaTimeX](https://lambdatimex.netlify.com/) 。会在评论区删除他们的回复链接。

为了加深你的知识，我建议你试试这篇由 [Sitepen](https://www.sitepen.com/blog/higher-order-components-in-react/) 撰写的关于 HOCs 的文章。🥂🥂🥂🥂