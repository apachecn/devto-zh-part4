# 州还是这个州？

> 原文：<https://dev.to/jasterix/state-or-this-state-4a80>

这篇博文的原标题是“构造函数实际上是做什么的？”但当我努力把我的想法缩短到 1 分钟或更短时，我创建一个诗歌应用程序的惨淡尝试帮助我明确了我想要解决的问题。

#### 如果是构造函数做什么的问题，答案是:

*   初始化您的组件

但是为什么有人会使用构造函数而不是初始化`state ={}`就不太清楚了。虽然我已经研究了这个话题，但我仍然抓住了**的细微差别，当**是选择其中一个的最佳时间

我个人喜欢使用没有道具的类组件，因为:

*   这是更简单/更短的代码
*   它从 React 继承方法。成分
*   也继承了构造函数，你不需要**编写它(尽管你仍然可以选择)**

#### 创建无道具组件

```
class MyComponent extends Component {
state = {

}
... 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，很多网上资源都是使用功能组件，或者使用带道具的类。这意味着您必须:

*   多写几行代码
*   创建一个带道具的构造器
*   然后将 props 值从构造函数传递给 super
*   调用`super(props)`以便可以使用`this.props`

#### 用道具创建组件

```
class MyComponent extends React.Component {
    constructor() {
        super();
        this.state={

        }
     }
... 
```

Enter fullscreen mode Exit fullscreen mode

显然，我需要更多地阅读这个话题。但是因为这是我必须花更多时间去做的事情，所以分享一下我所学到的似乎会有所帮助。

和往常一样，[这里有一个 StackOverflow 链接](https://stackoverflow.com/questions/51118103/this-state-vs-state-in-react)是我在谷歌搜索时遇到的