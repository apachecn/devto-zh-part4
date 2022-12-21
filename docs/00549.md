# 让我们来介绍一下无缝 ui🚀

> 原文：<https://dev.to/julienrioux/let-s-introduce-flawwwless-ui-1205>

Flawwwless ui 是一个新的开源 React.js 组件库。Flawwwless ui 的主要目标是构建一个免费的组件库，帮助开发人员轻松创建高质量的企业应用程序。考虑到这一点，我们还希望构建一个组件库，它可以轻松集成和定制，以匹配任何现有的应用程序，而不会影响现有的样式。

本文简要概述了无缝 ui 中的一些可用组件。关于图书馆更深入的信息，你可以在 https://ui.flawwwless.com 找到完整的官方文档。

注意:在本教程中，我们也将使用包名`flwww`来指定无瑕疵 ui 库。

* * *

先决条件
你需要在全球范围内安装 create-react-app，并对 React.js 有一个基本的了解(如果已经安装，跳过)

`npm install -g create-react-app`

* * *

# 我们出发吧🚀

安装
让我们开始使用`create-react-app`创建一个新的应用程序，并使用您的终端在您新创建的应用程序中下载 Flawwwless ui:

`create-react-app flwww-intro`

一旦你的应用程序创建完成，跳转到应用程序的根目录，使用 yarn 安装无缝 ui:

`cd flwww-intro && yarn add flwww`

* * *

# 按钮

使用 flwww 创建默认按钮非常简单。从库中导入组件后，您可以像使用原始按钮一样使用它:

你可以在你的应用程序中使用另外 3 种按钮类型:主要、成功和危险。要更改按钮类型，您需要将按钮的类型作为字符串存储在类型 props 中:

`<Button type="success" />`

```
import React from "react";

// Import the flwww Button
import { Button } from "flwww";

function App() {
  return (
    <div style={{ marginTop: "2rem", textAlign: "center" }}>
      <Button onClick={ () => alert("Submitting...") }>Default</Button>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

有关 flwww 按钮(轮廓、圆形、自定义颜色、加载等)的更多信息和自定义选项。)，参观:[https://ui.flawwwless.com/documentation/button](https://ui.flawwwless.com/documentation/button)

* * *

# 输入

要创建一个基本的文本输入，您可以像使用原始输入一样使用它:
*注意:输入占用其父元素宽度的 100%。

```
import React from "react";

// Import the flwww Input
import { Input } from "flwww";

function App() {
  return (
    <div style={{ width: "500px" }}>
      <Input placeholder="Simple input" />
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

有关 flww 输入的更多信息和定制选项，请访问:[https://ui.flawwwless.com/documentation/input](https://ui.flawwwless.com/documentation/input)

* * *

# 图标

要创建一个图标，您需要指定哪个图标要用作该图标的类型属性中的字符串。
在你的应用程序中实现一个简单的方法:
访问 https://ui.flawwwless.com/documentation/iconT2
找到你想要使用的图标
点击它复制组件
最后，直接粘贴到你的代码中！

你可以使用尺寸属性来指定你的图标的尺寸，也可以使用颜色属性来指定它的颜色。如果你没有指定一个尺寸&/或者一个颜色属性，图标将会得到最近的父图标的尺寸&/或者颜色。例如，如果您将一个图标放在 h1 标记中，而没有指定它的大小和颜色，该图标将与 h1 文本具有相同的大小和颜色。
这就是你如何创建一个拇指向上的图标，大小为 5rem，颜色为#03a87c :

```
import React from "react";

// Import the flwww Button
import { Icon } from "flwww";

function App() {
  return (
    <div style={{ marginTop: "2rem", textAlign: "center" }}>
      <Icon
        type="thumbUp"
        size="5rem"
        color="#03a87c" />
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

有关 flww 图标的更多信息和定制选项，请访问:[https://ui.flawwwless.com/documentation/icon](https://ui.flawwwless.com/documentation/icon)。

* * *

# 消息

用完美的用户界面发送信息也很容易。从 flwww 导入消息组件后，您可以将其用作一个简单的函数，向用户发送警报消息。该函数将消息附加到 DOM，几秒钟后将其删除。下面是一个当有人点击“发送消息”按钮时，向用户发送消息的例子:

```
import React from "react";

// Import the flwww Button and message
import { Button, message } from "flwww";

function App() {
  return (
    <div style={{ marginTop: "10rem", textAlign: "center" }}>
      <Button onClick={ () => message("This is a default message.") }>Send message</Button>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

有关 flww 消息(类型、持续时间等)的更多信息和定制选项。)，参观:[https://ui.flawwwless.com/documentation/message](https://ui.flawwwless.com/documentation/message)。

* * *

# 情态动词

向现有的 React.js 应用程序添加漂亮的动画模态可能是一件乏味的事情。幸运的是，完美的 ui 优雅地解决了这个问题！首先，你可以将任何有效的 JSX 代码放入模态组件中。
要控制模态的可见性，只需使用布尔值(通常存储在状态中)更改 isVisible 属性。
要关闭模态，你需要传递一个函数来改变控制 toggleModal 属性中模态可见性的状态(例如，一个在组件状态中切换可见性的函数)。
下面是一个简单的情态动词的例子:

```
import React, { Component } from "react";

// Import the flwww Button and message
import { Modal, Button } from "flwww";

class App extends Component {
    state = {
        modalIsVisible: false  // This control if the modal is visible or not
    }

    toggleModal = () => {
        // This function change the state of the modal visibility (e.g. this.state.modalIsVisible)
        this.setState(prevState => ({ modalIsVisible: !prevState.modalIsVisible }));
    }

    render(){
        const { modalIsVisible } = this.state;

        return (
            <div>
            <Button onClick={ this.toggleModal }>Show the modal</Button>

            <Modal
            isVisible={ modalIsVisible }
            toggleModal={ this.toggleModal }>

                <h3>Put anything you want inside!</h3>
                <p>You only have to put JSX code inside the Modal component!</p>
                <p>To control the visibility of the Modal, you have to change the isVisible props.</p>
                <p>To close the Modal, you need to pass a function that change the state that control the visibility of the modal (e.g. toggle Modal function).</p>

                <Button onClick={ this.toggleModal } type="primary">Continue</Button>

            </Modal>
            </div>
        )
    }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

前往[https://ui.flawwwless.com/documentation/modal](https://ui.flawwwless.com/documentation/modal)了解更多信息，以及您的模态组件的定制选项。

* * *

# 干得漂亮🙌

现在已经向您介绍了无 Flawwwless ui 库。要了解更多关于本文中介绍的组件的信息或查看 flwww 中所有可用的组件(菜单、抽屉、表格、布局等)。)，你会在 https://ui.flawwwless.com/找到官方文件。

我希望完美的用户界面能帮助你更容易、更快地创建令人惊叹的应用程序🏎️!