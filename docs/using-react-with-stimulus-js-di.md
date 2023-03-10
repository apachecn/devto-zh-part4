# 使用与刺激反应 JS

> 原文：<https://dev.to/dropconfig/using-react-with-stimulus-js-di>

我写过几篇关于我有多喜欢服务器端渲染(SSR)和使用 [Stimulusjs](https://stimulusjs.org/) 的文章。

但是有时候你需要反应的力量来帮助你。我们如何在需要的时候加入 React，而在不需要的时候不影响我们的网站呢？

实际上，在 Webpack 或 parceljs 中使用内置的代码分割并不困难。所以让我们过一遍最基本的。

### 我们的刺激控制器

```
import {Controller} from "stimulus";

export default class extends Controller {
    //Load the React code when we initialize
    initialize(){
        this.reactComponentPromise = import("./reactComponent");
    }
    connect(){
        this.reactComponent = await this.reactComponentPromise;

        const reactRoot = this.targets.find("reactRoot");
        const props = {
            onChange: this.onChange.bind(this)
        }
        this.reactComponent.render(reactRoot, props);

    }
    onChange(){
        //Some sort of thing to do with the changes.
        //Hit the API?
        //IDK you decide
    }
    disconnect(){
        const reactRoot = this.targets.find("reactRoot");

        this.reactComponent.destroy(reactRoot);
    }
} 
```

这很简单。但是现在，我们可以加载一个 react 组件，享受代码分割，而不会拖累我们的应用程序！

这在 React 端看起来像什么？

### React 代码

```
 import ReactDOM from "react-dom";

    import Something from "./Something";

    function render(node, props){
        ReactDOM.render(node, <Something {...props} />      
    }

    function destroy(node){
        ReactDOM.unmountComponentAtNode(node);
    }

    export {
        render,
        destroy
    } 
```

这真的是全部了。

请记住，React 非常聪明。你可以用刺激控制器的新道具呼叫`render`，它不会破坏任何东西。

### HTML

最后 html 把它放在一起。没什么大事

```
<div data-controller="react-controller">
    <div data-target="react-controller.reactRoot"></div>
</div> 
```

## 结论

有一个很好的例子，在你的应用中混合使用合适的技术。

你可以很容易地将 React 和 Stimulus 混合在一起，在你需要做一些繁重的工作时，仍然让你的应用程序在你的页面的其余部分主要是 SSR。

如果你有任何问题让我知道！