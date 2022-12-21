# 在 ReasonML 中绑定到“aws-amplify-react”中的“withAuthenticator”

> 原文：<https://dev.to/idkjs/binding-to-withauthenticator-in-aws-amplify-react-in-reasonml-52fh>

感谢 [Reason 的不和谐频道](https://discordapp.com/invite/reasonml)上的@bloodyOwl、 [stackoverflow](https://stackoverflow.com/questions/57548611/reasonml-binding-to-higher-order-functions/57561307#57561307) 上的@glennsl 和 [Reason.chat](https://reasonml.chat/t/resolving-error-this-call-is-missing-an-argument-of-type-authstate-option-authenticator-authdata-react-element/1620) 上的 [@hoichi](https://dev.to/hoichi) 的帮助，我终于弄明白了一些事情，尽管我还不明白所有的细微差别。

这个社区是最好的。

[![Alt Text](img/1a24b706dd8486f2387be42b049e3749.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M_HyAlG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zr6yh8emobdcfdp0o9cc.png)

# 装订

比方说，你致力于做`ReasonML`中的每一件事，因为你学到的每一件事都可以应用到其他地方....像我一样。很久以前，我使用 Reason 的 javascript interop 实现了这一点，但我必须让它在 Reason 中运行，这样我才能知道我能做到。在这里，我以#notetoself 的身份分享这个答案，以免给你带来任何痛苦。

难以置信，就这么简单。你和我需要研究的是不同的方式，包括用`include App`打开模块和用`open App`打开模块是不同的。不在这里做，因为我不知道，但我告诉你怎么做。

```
[@bs.module "aws-amplify-react"]
external withAuthenticator:
  // takes a react component and returns a react component with the same signature
  (React.component('props), bool) => React.component('props) =
  "withAuthenticator";

module App = {
  [@react.component]
  let make = (~message) => <div> message->React.string </div>;
};

module WrappedApp = {
  include App;
  let make = withAuthenticator(make,true);
}; 
```

在`index.re`中，使用如下绑定:

```
open BloodyOwlDemo;
ReactDOMRe.renderToElementWithId(<WrappedApp message="Thanks" />, "root"); 
```

## 注

如果在`WrappedApp`中不使用`include App`，而是直接调用`App.make`，像这样:

```
module WrappedApp = {
  // include App;
  let make = withAuthenticator(App.make,true);
}; 
```

您将得到以下错误:

```
>>>> Start compiling
[1/1] Building src/index-ReactHooksTemplate.cmj

  We've found a bug for you!
  /Users/prisc_000/working/DEMOS/bloody-authenticator/src/index.re 12:35-44

  10 │
  11 │ open BloodyOwlDemo;
  12 │ ReactDOMRe.renderToElementWithId(<WrappedApp message="Thanks" />, "roo
       t");

  The value makeProps can't be found in WrappedApp

>>>> Finish compiling(exit: 1) 
```

`include App`将 app 复制到`WrappedApp`模块，而不是打开它，这有一些影响，我需要研究一下。

文件中的[`include`](https://reasonml.github.io/docs/en/module#extending-modules)

在与@ glennsl 进一步讨论后，他解释了如何在不使用 include which 的情况下复制所有的内容。

他说，“基本上是一样的，除了没有包含应用程序；您将手动定义 let makeProps，就像从 js 导入组件一样。或者如果`WrappedApp`应该有和 App 一模一样的道具，你也可以把`App`中的`makeProps`别名为`let makeProps = App.makeProps;`。那正是`include App`；同样如此，除了它还包括`App`中的所有其他内容。

以下是@ glennsl 在教我们的。这只使用了我们的`App`模块中的`makeProps`。

```
module WrappedApp = {
  let makeProps = App.makeProps;
  let make = withAuthenticator(App.make,true);
}; 
```

[链接](https://github.com/idkjs/bloody-authenticator/blob/9e9248a7693a7ae62c86ae6dd59565c314cdcc8e/src/GlennslDemo.re#L16-L19)

## 转载本 App

这是一个应用程序，是从 AWS 的演示文档中找到的:[https://aws-amplify.github.io/docs/js/react.](https://aws-amplify.github.io/docs/js/react.)

然后添加 run `yarn add bs-platform -D`和`yarn add reason-react reason-scripts`。也运行`touch index.re`，在这里做你的`amplify configure`工作，因为你在`js`版本中有它。可以删除`js`版本。这个项目中有一个绑定[在这里](https://github.com/idkjs/bloody-authenticator/blob/master/src/aws/Amplify.re)。

然后`touch bsconfig.json`并添加以下内容。

```
{  "name":  "react-hooks-template",  "reason":  {  "react-jsx":  3  },  "sources":  {  "dir"  :  "src",  "subdirs"  :  true  },  "package-specs":  [{  "module":  "commonjs",  "in-source":  true  }],  "suffix":  ".bs.js",  "namespace":  true,  "bs-dependencies":  [  "reason-react"  ],  "gentypeconfig":  {  "language":  "untyped",  "module":  "es6",  "importPath":  "relative",  "reasonReactPath":  "reason-react/src/ReasonReact.js",  "exportInterfaces":  false,  "shims":  {},  "debug":  {  "all":  false,  "basic":  false  }  },  "refmt":  3  } 
```

给我看看[码](https://github.com/idkjs/bloody-authenticator)。