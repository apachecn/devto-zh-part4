# 质子原生 vs 武伊多 vs NodeGUI

> 原文：<https://dev.to/michaeljota/proton-native-vs-vuido-vs-nodegui-30j2>

*图片由 [LUM3N](https://pixabay.com/users/LUM3N-1066559/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2303849) 发自[Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2303849)T5】*

去年，我们欢迎了一个新的库，它使用名为 [`libui`](https://github.com/andlabs/libui) 的本地小部件来制作 UI 组件。它是用 C++编写的，与 Node 绑定，名为 [`libui-node`](https://github.com/parro-it/libui-node) ，有效地允许用 Javascript 开发桌面原生应用。使用`libui-node`两个项目上线，一个支持`Vue` ( [Vuido](https://github.com/mimecorg/vuido) )，另一个支持`React` ( [质子原生](https://github.com/kusti8/proton-native))。两者都使用相同的库，但是都不是为同一个团队开发的。每个人都有不同的团队和不同的方法。

最近，宣布了一个新的节点库，它具有用于创建 UI 组件的节点绑定， [`NodeGUI`](https://github.com/nodegui/nodegui) 。但是这个团队并没有创建一个新的部件库，相反，他们的目标是绑定 Qt5 部件。然而，它并不是第一个在 Node 中绑定 Qt widgets 的项目，那个荣誉属于 [`node-qt`](https://github.com/arturadib/node-qt) ，但这个项目被留了下来。同一个团队提供了一个 React 库来处理 NodeGUI，方便地命名为 [React NodeGUI](https://github.com/nodegui/react-nodegui) 。此外，另一名开发人员正在开发一个库，使 NodeGUI 能够与 Angular、 [ng-qt](https://github.com/marcus-sa/ng-qt) 一起工作。

# 目标

## libi/node-libi

`libui`的座右铭是*一个用于 C 的可移植 GUI 库*，所以这可能会给我们一个关于这个的想法。它在每个系统上使用本机小部件，并具有预期的性能。它有几个平台的绑定，包括。NET、Node、Kotlin 和 Go，但只有最后一个是由同一个人开发的，( [ui](https://github.com/andlabs/ui) )。所有其他的都是由社区开发的，我认为这不如由同一个家伙/团队开发的好，但也不错。正如我前面所说，进行绑定的节点库是`libui-node`，它的目标似乎是为`libui`的 C++小部件提供一个节点包装器。因为`libui`提供了本地窗口小部件，`libui-node`只需要普通的节点运行时就可以调用这些窗口小部件。

## NodeGUI

我认为`NodeGUI`背后的团队更加谦虚，因为他们有一个明确的目标，将 Qt5 小部件与 node 绑定。然而，他们似乎在推动一种以最佳性能方式实现这一点的方法。它们有一个常规节点运行时的分支，即合并 Qt 和 Node 的事件循环，称为 [`qode`](https://github.com/nodegui/qode) 。这样，您可以使用两个事件循环的能力。

# 状态

## libi/node-libi

正如 readme 所说，它应该是 pre-alpha，意思是不稳定，并且该项目已经有一段时间没有更新了。它目前有 3944 个提交、138 个未完成的发布和 63 个未完成的拉取请求。中期的时候，`node-libui`和它大哥一样稳。尽管如此，你还是可以围绕它做点什么。我没有试过任何大的东西，但它似乎很好。

## NodeGUI

它本身是最近的，目前正在积极更新，但很难说它是否会继续这样下去。该项目似乎比`node-libui`更稳定，但这也是因为他们使用了稳定的 widget 平台。我认为真正的交易是`Qode`，一个驱动这一切的工具。它目前有 323 个提交、9 个未决问题和 1 个未决 PR。此外，它还有一个项目板，里面有 6 个待办任务。

# 附加渲染器

能够只使用 JavaScript 进行渲染很好，但是能够使用您已经习惯的相同平台，这无疑要好得多。

## 节点-礼部

### 鳏夫

这是由 [mimecorg](https://github.com/mimecorg) 开发的用于 Vue 的`node-libui`渲染器。它支持声明自定义元素，使用`as`语法。不得不说，那是我尝试的第一个，刚灭的时候我就做了。它易于使用，并且与 Vue 集成得非常好。它有一个 CLI 模板，允许您使用`@vue/cli`来创建项目。

### 质子原生

不知道为什么这么叫，是渲染器反应。它是由 [kusti8](https://github.com/kusti8) 开发的。它似乎支持所有的 React 工具，像 Redux 之类的。它有一个名为`create-proton-app`的 CLI 工具，类似于`create-react-app`工具。

## NodeGUI

### 反应节点 GUI

这是 React 和 NodeGUI 的官方库。这里没有 CLI，所以您必须克隆一个空的存储库来开始使用它。它支持使用类似 CSS 的语法进行样式化，并且应该可以与所有的 React 插件一起工作。它还支持开箱即用的 TS。

### NG-QT

它是由马库斯-萨开发的。它的目标是成为一个与 NodeGUI 兼容的角度渲染器。到目前为止，它支持一些组件，但仍然有一些问题需要解决。

# 构建工具

## 节点-礼部

Proton-Native 有一个基于`electron-builder`的构建系统，但它对我不起作用，因为它不支持 Windows。Vuido 背后的团队开发了一个名为`launchui-packager`的构建工具。它确实工作了，但只适用于基于 Vuido 的项目，尽管它应该适用于 Proton-Native，但它没有。不知道是不是因为 Proton-Native 本身不支持 Windows，那应该。这两种方法都只是用一个嵌入式节点运行时和来自`libui`的原生模块来打包项目

## NodeGUI

该团队正在开发构建系统。我认为这更难实现，但它可能是比两个`node-libui`更好的系统，因为它可能是一个完整的构建工具，并嵌入了 Qode 运行时。

# 选什么？

我认为你不能选择任何一个。这两个都是非常实验性的项目，看起来更像是一个意向声明，而不是可以用于生产应用程序的东西。然而，就开发而言，NodeGUI 似乎更快，但同样，它缺乏一个合适的构建系统来分发您的应用程序。

# 那都是乡亲们！

感谢您的阅读。我希望你喜欢它，你会发现它很有帮助。

你试验过我提到的任何技术吗？请在下面的评论中告诉我你的经历。