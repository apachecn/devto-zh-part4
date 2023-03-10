# 在 Webpacker 中控制包

> 原文：<https://dev.to/prathamesh/mastering-packs-in-webpacker-116g>

在[之前的文章](https://dev.to/prathamesh/understanding-webpacker-in-rails-6-l4i)中，我们看到了什么是 Webpacker 以及 Rails 6 是如何集成它的。在本文中，我们将了解如何使用**包。**

一个新的 Rails 6 应用程序在`app/javascript`下创建了以下文件，这是编写 JavaScript 代码的新目的地。

```
Projects/scratch/better_hn master ✗ 2.6.3 ◒
▶ tree app/javascript
app/javascript
├── channels
│   ├── consumer.js
│   └── index.js
└── packs
    └── application.js

2 directories, 3 files 
```

Enter fullscreen mode Exit fullscreen mode

packs 目录包含 webpack 开始编译过程的 ***入口点*** 。这个文件在一个新的 Rails 6 应用中的内容如下。

```
require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels") 
```

Enter fullscreen mode Exit fullscreen mode

> 入口点类似于资产管道生成的`app/assets/application.js`文件。

这里的`require`声明与来自资产管道的*要求指令*不同。这个`require`语句可能需要 NPM 包以及我们代码中的本地模块。例如，在这种情况下，上面的前三行需要三个 NPM 包——Rails UJS、Turbolinks 和 Active Storage，而最后一行需要`app/javascript/channels/index.js`。

> Webpack 有一个在我们试图要求的目录名下寻找`index.js`文件的约定。

Webpacker 和 asset pipeline 之间要求代码的方式的主要区别在于，我们不使用 Webpacker 包中的指令。我们只是用包名或者目录名直接调用`require`或者`import`。

```
// app/javascript/packs/application.js

import React from 'react'
import ReactDOM from 'react-dom' 
```

Enter fullscreen mode Exit fullscreen mode

pack 文件也可以包含与应用程序相关的实际 JavaScript 代码，但保持 pack 文件干净是一个好习惯，只使用最少的代码导入模块和包，并将实际的应用程序相关代码放在`app/javascript/packs`之外。

> 保持包文件最小化，并导入其中的实际代码。

所以对于一个 React 项目，我们只需将我们的基本组件添加到包文件中的 DOM，并管理`pack`目录之外的`app/javascript`下的其余组件。

```
// app/javascript/packs/application.js
import React from 'react'
import ReactDOM from 'react-dom'
import PropTypes from 'prop-types'
import HelloWorld from '../components/hello'

document.addEventListener('DOMContentLoaded', () => {
  ReactDOM.render(
    <HelloWorld name="Webpack" />,
    document.body.appendChild(document.createElement('div')),
  )
})

// app/javascript/components/hello.js

const HelloWorld = props => (
  <div>Hello {props.name}!</div>
)

Hello.defaultProps = {
  name: 'David'
}

Hello.propTypes = {
  name: PropTypes.string
}

module.exports HelloWorld 
```

Enter fullscreen mode Exit fullscreen mode

就如何组织 JavaScript 代码而言，Webpacker 相当开明。唯一的规则是`packs`目录是特殊的，被 Webpacker 当作入口点。剩下的就看你自己了。我更喜欢为所有的 React 组件添加一个`components`目录。我的应用程序将`application`包作为默认包，而`admin`包只包含与管理员用户相关的代码。

在链轮世界中，我们必须在资产预编译列表中添加我们希望 Rails 预编译的每个定制文件。

```
Rails.application.config.assets.precompile += %w[admin.js] 
```

Enter fullscreen mode Exit fullscreen mode

但是因为整个`packs`目录被 Webpacker 视为入口点，所以我们不需要向预编译列表中添加任何定制包。就是管用！

一般来说，我会说我们应该将`app/javascript`目录视为应用程序本身中的一个*应用程序，并相应地组织代码。在我的一个应用程序中，`app/javascript`的目录结构如下。*

```
app/javascript
├── admin
├── channels
├── login
└── packs
    ├── admin.js
    ├── application.js
    └── login.js 
```

Enter fullscreen mode Exit fullscreen mode

当我们编译这个 JavaScript 时，输出如下所示。

```
▶ ./bin/webpack-dev-server
ℹ ｢wds｣: Project is running at http://localhost:3035/
ℹ ｢wds｣: webpack output is served from /packs/
ℹ ｢wds｣: Content not from webpack is served from /Users/prathamesh/Projects/scratch/better_hn/public/packs
ℹ ｢wds｣: 404s will fallback to /index.html
ℹ ｢wdm｣: Hash: 5387bbdba96d7150c792
Version: webpack 4.39.2
Time: 2753ms
Built at: 09/24/2019 12:23:20 AM
                                     Asset Size Chunks Chunk Names
          js/admin-67dd60bc5c69e9e06cc3.js 385 KiB admin [emitted] admin
      js/admin-67dd60bc5c69e9e06cc3.js.map 434 KiB admin [emitted] admin
    js/application-d351b587b51ad82444e4.js 505 KiB application [emitted] application
js/application-d351b587b51ad82444e4.js.map 569 KiB application [emitted] application
          js/login-1c7b2341998332589ec0.js 385 KiB login [emitted] login
      js/login-1c7b2341998332589ec0.js.map 434 KiB login [emitted] login
                             manifest.json 958 bytes [emitted] 
```

Enter fullscreen mode Exit fullscreen mode

除了生成指纹文件和源映射文件之外，它还生成一个`manifest.json`,其中列出了由编译过程生成的所有文件的信息。Rails 使用这个文件将对`javascript_pack_tag`中资产的引用转换成实际编译的文件。因为`javascript_pack_tag('admin')`将被转换为`js/admin-67dd60bc5c69e9e06cc3.js`。一个样品`manifest.json`看起来像[这个](https://gist.github.com/prathamesh-sonpatki/25c8657fc811680af37cf79fcb3c3792)。

现在我们已经准备好了包，我们将按照布局文件中的要求使用它们。在我的例子中，`login`包只在`login`布局中使用，与用户登录后使用的`application`包是分开的。对于`admin`布局，除了`application`包之外，使用单独的`admin`包。我们可以通过将它包含在布局文件中来使用任何包。

```
<body>
  <%= javascript_pack_tag "application" %>
  <%= javascript_pack_tag "admin" %>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

所以总结一下:

*   尽量减少打包文件，只从其他文件导入所需的代码。
*   只有包文件必须放入`app/javascript/packs`
*   在`app/javascript`中，您可以根据自己的意愿自由组织其余的 JavaScript 代码。
*   密切关注 Webpack 的输出，以监控包的大小。
*   根据您的要求组织包文件，并根据它们将提供的功能来管理包。

如果你有兴趣了解更多关于 Webpacker 和 Rails 6 的信息，[和我一起踏上 Rails 6 之路](https://prathamesh.tech/road-to-rails-6/)。