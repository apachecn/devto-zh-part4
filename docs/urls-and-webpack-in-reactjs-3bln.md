# ReactJS 中的 URL 和 Webpack

> 原文：<https://dev.to/dillionmegida/urls-and-webpack-in-reactjs-3bln>

我在反应堆里还是个新手。我一直在关注一系列关于这个框架的教程和文章，并决定开始将我到目前为止学到的东西付诸实践。

我的网站，[dillionmegida.com](http://dillionmegida.com)是用 PHP 建立的。你可以去看看，因为我非常欣赏评论。虽然我渴望成为一名全栈 javascript 开发人员，所以我在某种程度上试图脱离 PHP:)

我决定用 React 复制我的主页，并扩展我使用组件的技能。

它进行得相当成功，直到我尝试使用一个`<img>` JSX 元素。
我用的像；

```
import React from 'react'

let dpStyle = {
    // Some styles
}

let Profilepic = () => (
    <div className={dpStyle}>
        <img src='../img/deee.jpeg' alt='My profile picture'/>
    </div> )
export default Profilepic; 
```

Enter fullscreen mode Exit fullscreen mode

img 文件夹是 src 文件夹的子目录。

我在这里的目的是让我的个人资料图片作为一个组件与一些风格用于我的主页和任何其他期望的页面。
img 标签的 src 使用不当，因为我的图像没有显示。

我停下来思考问题，检查我的页面，发现显示的 src 和我输入的完全一样。我真傻:(
我做了一些研究，这帮助我记住了 JSX 元素的大多数属性并不经常在 HTML 中使用，而是必须用花括号括起来。

```
import React from 'react'

let dpStyle = {
    // Some styles
}

let Profilepic = () => (
    <div className={dpStyle}>
        <img src={'../img/deee.jpeg'} alt='My profile picture'/>
    </div> )
export default Profilepic; 
```

Enter fullscreen mode Exit fullscreen mode

我再次尝试渲染页面，但我的图像仍然没有显示。

够搞笑的，我很快就想到了一招(第一次)；

```
...
import Dp from '../img/deee.jpeg'
...
let Profilepic = () => (
    <div className={dpStyle}>
        <img src={Dp} alt='My profile picture'/>
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

令我惊讶的是，它成功了。我很兴奋，同时也很难过，有点内疚。我不知道为什么。lol。
我对自己说，
‘我做的不对:(’
也问自己，
‘反应有这么疯狂吗？’

我回到谷歌做了进一步的研究，发现自动安装的 React(`create-react-app`)web pack 已经被配置为使用 public 文件夹(与 src 文件夹在同一级别)作为相对 URL(比如我的图片 URL)。

使用...`<img src={'../img/deee.jpeg'} />`...React 实际上是在检查公共文件夹中找不到的图像子目录。

### 解

##### 1。更改图像文件夹的位置

我改变了图像文件夹的位置，使它成为公共目录下的一个子目录，它像预期的那样工作。

##### 2。使用 require 关键字

正如 import 关键字用于相对 URL 一样，require 关键字也是如此。所以，我能够做到这一点；

```
import React from 'react'

let dpStyle = {
    // Some styles
}

let Profilepic = () => (
    <div className={dpStyle}>
        <img src={require('../img/deee.jpeg')} alt='My profile picture'/>
    </div> )
export default Profilepic; 
```

Enter fullscreen mode Exit fullscreen mode

在其他需要并使用了`Profilepic`组件的页面中，我的图像显示得非常完美。:D

就像我说的，我仍然是 ReactJS 的新手，但我希望这个小小的经历也能对你有所帮助。网络包还有其他的超能力，我仍在学习中。
请在评论区分享对这篇文章的修改和有用的提示。

我将尽可能多地记录我在 ReactJS 旅程中的经历。
敬请关注，感谢阅读。