# KnitZilla

> 原文：<https://dev.to/itminds/knitzilla-2lj0>

给老婆写针织 app！
**免责声明:这是无钩写的**

两周前，我发表了一篇关于作为语言的理性是什么的文章，并承诺我将展示一个使用该技术的真实应用。[这里](https://dev.to/itminds/what-is-the-reason-4ho4)

应用: [knitzilla](https://knitzilla.herokuapp.com)
(我在 heroku 上用的是免费的 dyno，所以第一次尝试打开会有点慢)。
源代码[此处](https://github.com/Simmetopia/knitcity)

首先，我想承认我有点“黑”了这个应用程序的开头。为了避免与 webpack 和服务人员等一起设置部署脚本，我选择尝试“Reason 很容易插入现有的 JavaScript 应用程序”这一卖点。我必须承认，我没有失望。

```
 import React from "react";
   import ReactDOM from "react-dom";
   // This is a reason component
   import App from "./BackgroundWrapper.bs";  
   import * as serviceWorker from "./serviceWorker";   
   import MuiThemeProvider from "@material-ui/core/styles/MuiThemeProvider";
   import theme from "./theme";   
   import CssBaseline from "@material-ui/core/CssBaseline/CssBaseline";      
   ReactDOM.render(      
     <MuiThemeProvider theme={theme}>
       <CssBaseline />   
       {/*Here we are rendering the reason component */}
       <App /> 
    </MuiThemeProvider>, document.getElementById("root")); serviceWorker.register(); 
```

所以我使用 JavaScript API 来创建我的素材 UI 主题，并注册应用程序根。
从`<App/>`开始的一切都是纯粹的、合理编写的代码。

在《理性的土地》中，我做了几件我认为很酷的事情:

1.  路由(内置)
2.  从浏览器本地存储器读写
3.  类型安全的 css-in-js (bs-css)。

## 路由

既然模式匹配很神奇，路由之类的事情突然就变得微不足道了

```
type routes = | Home | Calc;
type state = {
  activeRoute: routes,
  startClientX: int,
};

/* binding to the route works quite like hooks does,
 meaning you can subscribe and unsubscribe to events in the same function */

let didMount = self => {
let touchListen = ReasonReact.Router.watchUrl(url =>
  switch (url.path) {
    | ["calc"] => self.send(Route(Calc))
    | _ => self.send(Route(Home))
    }
  );

  self.onUnmount(() => ReasonReact.Router.unwatchUrl(touchListen));
},

/* Somewhere in rendering logic */
<div>
{switch (self.state.activeRoute) {
  | Home => <App />
  | Calc => <RowCalcRoot />
}}
</div> 
```

如果你打开应用程序或代码，你会注意到我也实现了一个“滑动”特性，它们被绑定在如上所示的相同的生命周期方法中，但为了简洁起见被省略了。

## 在理智中读写 JSON

当我们用 JavaScript 编写时，我们习惯于从 JavaScript 读取或写入 JSON 对象时基本上是零开销。

在大多数其他语言中，这需要一点工作。现在有一些库可以在 Reason 中帮助 JSON 解析，但是我采用了一种更本地的方法来更好地学习这种语言。

```
module RowCalcParser = {
  type rowCalc = {
      title: string,
      rows: int,
    };

  type rows = array(rowCalc);in e

  let decodeRow = jsonString => {
    Json.Decode.{
      title: jsonString |> field("title", string),
      rows: jsonString |> field("rows", int),
    };
  };

  let decode = jsonString => Json.Decode.(jsonString |> array(decodeRow));

  let encodeRow = row => {
    Json.Encode.(
      object_([("title", string(row.title)), ("rows", int(row.rows))])
    );
  };

  let encode = (rows: array(rowCalc)) => {
    Json.Encode.(rows |> array(encodeRow));
  };
}; 
```

因为 Reason 需要有 100%的类型覆盖率，我们需要声明如何解析 JSON 对象的每个部分。我做了一个行计算器解析器，用一组`rowCalc`类型来持久化和水合应用程序。

在代码中，我使用了 decode 和 encode 函数，这些函数使用 rowEncoders/decoders 来帮助解析数组中的对象。

编码器的用法如下:

```
Dom.Storage.(
  localStorage
    |> setItem( "rowCalcs",
                newSelf.state.rows |> RowCalcParser.encode |> Json.stringify)
); 
```

上面的语法可能看起来很奇怪。在我们谈过之后，你会发现它很棒(我希望如此)。

我们所说的是。存储命名空间我们要使用 localStorage，localStorage 被传入第一个参数`setItem`，`setItem`像在 JavaScript 中一样需要两个字符串，key 和值。
关键字是字符串 rowCalc，值是 state.rows 来自组件，这些行是原因列表类型，被传递到如上所示的 encode 函数中，该函数又可以被字符串化并保存。就像这样，当解码它们时，情况正好相反。

## BS-CSS

任何使用过 css-in-jss 的人都会同意，它为整个 css 体验提供了一些真正强大的功能。虽然当使用 css-in-jss 时，你需要传递 100%的值，但你必须传递一个字符串，比如`borderRadius: "100%"`你不会得到任何帮助来告诉你 100%是否是一个选项。

BS-CSS 将 css-in-jss 带到了一个新的高度，如下所示。

```
module Styles = {
  open Css;
  let floatingRightSideBack =
    style([position(absolute), top(`percent(50.0)), left(px(5))]);
  let numberWrapper =
    style([
      border(px(1), solid, rgba(0, 0, 0, 0.87)),
      borderRadius(px(5)),
      padding2(~v=px(12), ~h=px(24)),
    ]);
  let formControl =
    style([
      position(absolute),
      bottom(`percent(5.0)),
      right(`percent(50.0)),
      transform(translateX(`percent(50.0))),
    ]);
  let counterContainer =
    style([
      width(`percent(80.0)),
      overflowY(auto),
      height(`percent(60.0)),
    ]);
}; 
```

注意当使用尺寸时，我们是如何定义种类的，比如百分比，像素或者雷姆。有些值接受浮点数，如上面的 percent，有些值只接受整数，如 px。

注意在 padding2 键的 numberWrapper 中，这里你定义了用`~v`标注的垂直填充和用`~h`标注的水平填充，所以不用再把这两个搞混了，非常简洁！

## 结论

尽管 KnitZilla 是用现在遗留的 reason react API 编写的，但它仍然展示了用完全类型化语言编写 web 的好处。

此外，有一些特性是你在 JavaScript 中永远不会想到的，比如类型(du'h)和模式匹配。

如果您想了解更多有关原因和 React 的信息，请访问:
[react](https://reasonml.github.io/reason-react/)
[react](https://reasonml.github.io/)