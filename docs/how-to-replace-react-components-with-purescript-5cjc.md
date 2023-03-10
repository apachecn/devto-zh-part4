# 如何用 PureScript 替换 React 组件

> 原文：<https://dev.to/thomashoneyman/how-to-replace-react-components-with-purescript-5cjc>

我有两次用 PureScript 替换大型 JavaScript 应用的经历:第一次是在 CitizenNet，我们用 Halogen 替换了 Angular，第二次是在 Awake Security，我们用 PureScript React 替换了 React 应用的大部分。两家公司的软件缺陷都急剧减少。

将任何重要的应用程序从一种语言改写成另一种语言的最佳方式是，在它继续运行的同时，一点一点地递增。起初，这种新语言可以简单地接管应用程序逻辑上独立的部分:管理仪表板、聊天窗口或大表单。但是您最终会想要混合这些语言:一个用 PureScript 编写但以 JavaScript 形式使用的 autocomplete，或者一个 PureScript 组件作为子组件被传递来自两种语言的混合组件，或者一个共享的全局状态。

[Original:用 PureScript 的 React 库替换 React 组件](https://thomashoneyman.com/articles/replace-react-components-with-purescript/)

在这一点上，新语言必须足够灵活，能够将两种语言的代码混合在一起，而不仅仅是接管应用程序的一部分。幸运的是，您可以将惯用的 PureScript 的接口转换成惯用的 JavaScript(反之亦然)。用任何领先的 PureScript UI 库编写的组件都可以与用 Angular 和 React 等 JavaScript 框架编写的组件交错使用。

由于其`react`和`react-basic`库，用 PureScript 替换 React 应用程序相对容易。使用相同的底层框架意味着应用相同的习惯用法，组件可以共享，几乎不需要修改。我们不仅可以共享孤立的组件；在 Awake Security，我们共享国际化、Redux 存储和中间件，以及混合代码库中的其他全局上下文，PureScript 定期导入 JavaScript，JavaScript 定期导入 PureScript。

在本文中，我将演示如何用 PureScript 编写的简单组件替换 React 应用程序的一部分。在这个过程中，我将分享使这个互操作变得方便和可靠的最佳实践。这些例子很简单，但是同样的技术也适用于复杂的组件。

#### 章节

我们将一起:

1.  [用 JavaScript 写一个小小的 React 应用](#let-s-write-a-react-app-in-javascript)
2.  [更新应用程序以支持 PureScript](#setting-up-a-shared-purescript-javascript-project)
3.  [用 PureScript React 替换 React 组件，其接口和行为与原始组件相同](#replacing-a-react-component-with-purescript-react)
4.  [再次用 React Basic 替换组件](#replacing-a-react-component-with-purescript-react-basic)

我鼓励你跟着这篇文章一起编码；没有代码被省略，依赖项被固定，以帮助确保示例是可重复的。这段代码使用全球安装的节点`v11.1.0`、纱线`v1.12.0`和 NPX `v6.5.0`，以及本地安装的 PureScript 工具。您还可以查看[原文 purescript react 文章](https://thomashoneyman.com/articles/replace-react-components-with-purescript/)。

## 我们用 JavaScript 写个 React app 吧

我们将编写一个显示一些计数器的微型 React 应用程序，然后用 PureScript 替换它的组件。除了导入之外，最终的 JavaScript 代码将与原始代码无法区分，但它仍然是纯脚本。

让我们遵循官方的 React 文档，使用`create-react-app`来初始化项目，然后将我们的源代码削减到最少。

```
# Create the app
npx create-react-app my-app && cd my-app 
```

在写的时候，`create-react-app`产生了这些 React 依赖:

```
"dependencies":  {  "react":  "^16.8.6",  "react-dom":  "^16.8.6",  "react-scripts":  "3.0.1"  } 
```

在`src`下我们有一些源文件，但是我们的应用程序只需要其中的两个:`index.js`，Webpack 的入口点，和`App.js`，我们应用程序的根组件。剩下的我们可以删掉:

```
# Delete all the source files except for the entrypoint and
# root app component
find src -type f -not \( -name 'index.js' -or -name 'App.js' \) -delete 
```

最后，让我们用本文所需的最少内容替换这两个文件的内容。从现在开始，我将提供一些不同的东西，你可以提供给`git apply`来应用和我一样的改变。

首先，我们的切入点:

```
// src/index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root")); 
```

然后我们的主要 app 组件:

```
// src/App.js
import React from "react";

function App() {
  return (
    <div>
      <h1>My App</h1>
    </div>
  );
}

export default App; 
```

### 编写 React 组件

让我们编写第一个 React 组件:一个计数器。这可能是您遇到的第一个 React 组件示例；这也是 PureScript React 库中的第一个例子。它也足够小和简单，在本文中可以被替换两次。

```
touch src/Counter.js 
```

计数器将是一个按钮，保持它被点击的次数。它将接受一个显示在按钮上的标签，作为它唯一的道具。

```
// src/Counter.js
import React from "react";

class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <button onClick={() => this.setState({ count: this.state.count + 1 })}>
        {this.props.label}: {this.state.count}
      </button>
    );
  }
}

export default Counter; 
```

然后，我们将把新的计数器导入到我们的主应用程序中:

```
--- a/src/App.js +++ b/src/App.js @@ -1,9 +1,13 @@
 import React from "react";
+import Counter from "./Counter"; 
 function App() {
   return (
     <div>
       <h1>My App</h1>
+      <Counter label="Count" />
+      <Counter label="Clicks" />
+      <Counter label="Interactions" />
     </div>
   );
 } 
```

使用`yarn start`,我们可以运行开发服务器并查看我们的应用程序的运行情况。

[![running app](img/641238d24d5320392e99f0336bd4de85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w93eGEkz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thomashoneyman.cimg/2019/running-app.gif)

## 建立一个共享的 PureScript & JavaScript 项目

我们已经写了太多的 JavaScript。让我们在这个项目中也支持 PureScript。我们的目标是用任何一种语言编写代码，并且可以自由地向任何一个方向导入，而不会产生摩擦。为此，我们将安装 PureScript 工具，创建一个单独的 PureScript 源目录，并依靠编译器生成 JavaScript 代码。

### 1。安装编译器和包管理器

首先我们必须安装 PureScript 工具。我推荐使用 Yarn 来安装与本文中使用的编译器和 Spago(一个包管理器和构建工具)相匹配的本地版本。我将使用 NPX 来确保所有命令都使用该软件的本地副本运行。

```
# Install the compiler and the Spago package manager
yarn add -D purescript@0.13.0 spago@0.8.4 
```

### 2。初始化项目和包集

我们可以用`spago init`创建一个新的 PureScript 项目。从版本 0.8.4 开始，Spago 总是使用相同的包集进行初始化，这意味着您应该拥有与撰写本文所用的包相同的包版本。我用的是`psc-0.13.0-20190607`套装。

```
# npx ensures we're using our local copy of Spago installed in node_modules.
npx spago init 
```

Spago 已经创建了一个`packages.dhall`文件，它指向可以安装的软件包集合，还有一个`spago.dhall`文件，它列出了我们实际安装的软件包。我们现在可以安装我们需要的任何依赖项，并且我们将确定这些版本都是兼容的。

在安装任何东西之前，让我们更新现有的`.gitignore`文件来覆盖 PureScript。对于一个基于 Spago 的项目，这将工作:

```
--- a/.gitignore +++ b/.gitignore @@ -21,3 +21,9 @@
 npm-debug.log*
 yarn-debug.log*
 yarn-error.log*
+
+# purescript
+output
+.psc*
+.purs*
+.spago 
```

### 3。调整目录结构

最后，让我们来组织我们的源代码。除了为 PureScript 编写 FFI 文件时，通常将 JavaScript 源代码与 PureScript 源代码分开。因为我们在这个项目中没有这样做，我们的源文件将被完全分离。让我们将所有 JavaScript 代码移动到一个`javascript`子目录中，并在它旁边创建一个新的`purescript`文件夹。

```
mkdir src/javascript src/purescript
mv src/App.js src/Counter.js src/javascript 
```

接下来，我们将调整`index.js`到我们根组件的新位置:

```
--- a/src/index.js +++ b/src/index.js @@ -1,5 +1,5 @@
 import React from "react";
 import ReactDOM from "react-dom";
-import App from "./App"; +import App from "./javascript/App"; 
 ReactDOM.render(<App />, document.getElementById("root")); 
```

我们只剩下一项任务了。PureScript 编译器将 JavaScript 生成到项目根目录下名为`output`的目录中。但是`create-react-app`禁止导入`src`目录之外的任何内容。虽然有更好的解决方案，但对于这个项目，我们将通过将`output`目录符号链接到`src`目录来绕过限制。

```
# we can now import compiled PureScript from src/output/...
ln -s $PWD/output $PWD/src 
```

您的`src`目录现在应该是这样的:

```
src
├── index.js
├── javascript
│ ├── App.js
│ └── Counter.js
├── output -> ../output
└── purescript 
```

## 用 PureScript React 替换 React 组件

当用 PureScript 组件替换 JavaScript React 组件时，我喜欢遵循四个简单的步骤:

1.  用惯用的 PureScript 编写组件。
2.  为组件编写单独的互操作模块。这个模块提供了 JavaScript 接口以及 PureScript 和 JavaScript 类型和习惯用法之间的转换功能。
3.  使用 PureScript 编译器生成 JavaScript
4.  导入结果代码，就像它是一个普通的 JavaScript React 组件一样。

我们将从在 Awake Security 中使用的`react`库开始。它类似于`react-basic`,但是更直接地映射到底层的 React 代码，并且不那么固执己见。稍后，我们将切换到`react-basic`，这将展示它们之间的一些差异。

随着我们在此过程中的每一步，我将更多地解释为什么它是必要的，以及一些需要记住的最佳实践。让我们开始:安装`react`库并准备编写我们的组件:

```
# install the purescript-react library
npx spago install react

# build the project so editors can pick up the `output` directory
npx spago build

# create the component source file
touch src/purescript/Counter.purs 
```

### 1。用惯用的 PureScript 编写 React 组件

即使我们正在编写一个从 JavaScript 使用的组件，我们仍然应该编写普通的 PureScript。我们很快就会看到，可以只调整 JavaScript 组件的接口，而不改变内部结构。如果这个组件既要由 PureScript 使用，又要由 JavaScript 使用，这一点尤其重要；我们不想给任何一个代码库带来任何与互操作相关的尴尬。

下面，我用同样的道具、状态和渲染写了一个组件版本。将其内容复制到`src/purescript/Counter.purs`中。

注意:当创建一个组件时，没有必要注释`this`，但是如果你做错了什么，这样做可以提高错误的质量。

```
module Counter where

import Prelude

import React (ReactClass, ReactElement, ReactThis, component, createLeafElement, getProps, getState, setState)
import React.DOM as D
import React.DOM.Props as P

type Props = { label :: String }

type State = { count :: Int }

counter :: Props -> ReactElement
counter = createLeafElement counterClass

counterClass :: ReactClass Props
counterClass = component "Counter" \(this :: ReactThis Props State) -> do
  let
    render = do
      state <- getState this
      props <- getProps this
      pure $ D.button
        [ P.onClick \_ -> setState this { count: state.count + 1 } ]
        [ D.text $ props.label <> ": " <> show state.count ]

  pure
    { state: { count: 0 }
    , render
    } 
```

在 PureScript 代码库中，这就是我们所需要的；我们可以通过导入`counter`并为其提供道具
来使用这个组件

```
-- compare to our JavaScript main app
import Counter (counter)

renderApp :: ReactElement
renderApp =
  div'
    [ h1' [ text "My App" ]
    , counter { label: "Count" }
    , counter { label: "Count" }
    , counter { label: "Count" }
    ] 
```

我们也可以从 JavaScript 中使用这个组件。`react`库将从这段代码中生成一个可用的 React 组件，我们可以像导入任何其他 JavaScript React 组件一样导入它。让我们先试一试，然后再做一些改进。

首先，我们将编译项目:

```
npx spago build 
```

然后我们将导入组件。请注意，我们的实现非常接近，我们只需更改导入，其他什么都不用做！PureScript 将在`output`中生成文件，因此我们的计数器组件现在驻留在`output/Counter`中。

```
--- a/src/javascript/App.js +++ b/src/javascript/App.js @@ -1,5 +1,5 @@
 import React from "react";
-import Counter from "./Counter"; +import { counter as Counter } from "../output/Counter"; 
 function App() {
   return ( 
```

运行`yarn start`,您应该会看到与之前完全相同的一组计数器。有了 PureScript 实现的组件，我们不再需要 JavaScript 版本:

```
rm src/javascript/Counter.js 
```

我们已经成功地用 PureScript 接管了部分 JavaScript 应用。

### 2。为组件编写一个互操作模块

我们很幸运，我们的组件马上就工作了。事实上，它之所以有效，是因为到目前为止我们使用的是简单的 JavaScript 类型，我们的计数器组件的用户是值得信赖的，并且没有省略我们认为必需的 label prop。我们可以在 PureScript 中强制正确的类型和没有缺失的值，但是在 JavaScript 中不行。

如果用户忘记给组件提供标签会发生什么？

[![forgotten label prop](img/43958dc9e4481ac5cc2e913e092e7b31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6aeA8C_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thomashoneyman.cimg/2019/forgot-prop.png)

嗯，将`undefined`设置为标签不是*好*，但也没有整个应用崩溃那么糟糕——如果你试图对你假装为`String`的值使用 PureScript 函数，就会发生这种情况。问题是`String`类型并没有很好地捕捉到 JavaScript 可能产生的值。一般来说，我希望人们按照他们通常的方式编写 JavaScript，这意味着使用内置类型、常规的无约束函数，有时会省略信息并提供`null`或`undefined`来代替。这就是为什么在 Awake Security，我们通常为将在 JavaScript 代码中使用的组件提供一个互操作模块，它:

1.  提供组件中使用的 PureScript 类型和简单 JavaScript 表示之间的映射
2.  通过用`Nullable`类型标记所有可能是`null`或`undefined`的输入，增加了一层安全性，这有助于我们的代码优雅地处理缺失值
3.  将形式化的函数转换成普通的 JavaScript 函数，并将有效的函数(在生成的代码中表示为 thunks)转换成调用时立即运行的函数
4.  作为 PureScript 代码中影响相关 JavaScript 代码的变化的金丝雀，因此您可以格外小心

在本文的剩余部分，我们将探索每一种技术。现在，我们需要做的就是将输入字符串标记为`Nullable`，并显式地处理当它被省略时会发生什么。

让我们为名为`Counter.Interop` :
的组件创建一个互操作模块

```
mkdir src/purescript/Counter
touch src/purescript/Counter/Interop.purs 
```

通常，每个互操作模块至少包含三样东西:

1.  新的兼容 JavaScript 的界面(`JSProps`)
2.  从新类型转换为纯脚本类型的函数(`jsPropsToProps`)
3.  通过转换函数(`jsComponentName`)使用新的 JavaScript 兼容类型的新组件

行动中:

```
module Counter.Interop where

import Prelude

import Counter (Props, counter)
import Data.Maybe (fromMaybe)
import Data.Nullable (Nullable, toMaybe)
import React (ReactElement)

type JSProps = { label :: Nullable String }

jsPropsToProps :: JSProps -> Props
jsPropsToProps { label } = { label: fromMaybe "Count" $ toMaybe label }

jsCounter :: JSProps -> ReactElement
jsCounter = counter <<< jsPropsToProps 
```

我们为组件创建了一个新的接口`JSProps`，它将在 JavaScript 中使用，而不是我们的 PureScript 接口`Props`。我们还创建了一个在两个接口之间进行翻译的函数，并生成了一个使用 JavaScript 接口而不是 PureScript 接口的新组件。

将`label`属性标记为`Nullable`使得编译器意识到该字符串可能不存在。然后，它迫使我们显式地处理`null`或`undefined`的情况，然后我们才能把道具当作通常的`String`。为了将新的`JSProps`类型映射到组件预期的`Props`类型，我们需要处理 null 情况。为此，我们将`Nullable`转换为`Maybe`，然后在道具不存在时提供一个回退值。

`Nullable`类型明确用于与 JavaScript 的互操作，但它并不总是以您期望的方式运行。它不直接映射到普通的`Maybe`类型。你通常应该尽快将任何`Nullable`类型转换为`Maybe`。[如果你想了解更多关于这个](https://github.com/purescript-contrib/purescript-nullable)的信息，请查看可空库。

让我们更改`App.js`中的导入，并验证省略的标签是否处理得当。

```
--- a/src/javascript/App.js +++ b/src/javascript/App.js @@ -1,5 +1,5 @@
 import React from "react";
-import { counter as Counter } from "../output/Counter"; +import { jsCounter as Counter } from "../output/Counter.Interop"; 
 function App() {
   return ( 
```

现在，省略的道具仍然呈现出合理的标签:

[![forgot props fixed](img/00f2c784511c717d4b93057136cbdd74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V8Lndm12--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thomashoneyman.cimg/2019/forgot-prop-fixed.png)

在这种情况下，我们的互操作模块简单地将单个字段标记为`Nullable`。但是 JavaScript 接口与它所翻译的 PureScript 接口略有不同是很常见的。保持一个独立的互操作模块可以很容易地做到这一点，而不会影响核心组件。

它还确保对底层组件的任何更改都反映为互操作文件中的类型错误，而不是(潜在地)悄悄地破坏 JavaScript 代码。当你习惯于编译器警告你一个文件中的改变会对另一个文件产生影响时，你很容易变得懒惰！

如果你使用 TypeScript，Justin Woo 已经从 PureScript 写了一篇关于透明共享类型的文章，值得一读。

## 用 PureScript React Basic 替换 React 组件

让我们再次尝试替换计数器，但是这次用更新的、更固执己见的`react-basic`库。在这个过程中，我们将使用一些更复杂的类型，并构建一个更复杂的互操作模块。

安装`react-basic` :

```
npx spago install react-basic 
```

接下来，用用`react-basic` :
编写的相同实现替换`Counter`的内容

```
module Counter where

import Prelude

import React.Basic (JSX, createComponent, make)
import React.Basic.DOM as R
import React.Basic.DOM.Events (capture_)

type Props = { label :: String }

counter :: Props -> JSX
counter = make (createComponent "Counter") { initialState, render }
  where
  initialState = { count: 0 }

  render self =
    R.button
      { onClick:
          capture_ $ self.setState \s -> s { count = s.count + 1 }
      , children:
          [ R.text $ self.props.label <> " " <> show self.state.count ]
      } 
```

两个 React 库不共享类型，所以我们将改变我们的 interop 模块来描述产生`JSX`而不是`ReactElement`。

```
--- a/src/purescript/Counter/Interop.purs +++ b/src/purescript/Counter/Interop.purs @@ -5,13 +5,13 @@ import Prelude
 import Counter (Props, counter)
 import Data.Maybe (fromMaybe)
 import Data.Nullable (Nullable, toMaybe)
-import React (ReactElement) +import React.Basic (JSX) 
 type JSProps = { label :: Nullable String }

 jsPropsToProps :: JSProps -> Props
 jsPropsToProps { label } = { label: fromMaybe "Count" $ toMaybe label }

-jsCounter :: JSProps -> ReactElement
+jsCounter :: JSProps -> JSX
 jsCounter = counter <<< jsPropsToProps 
```

### 使其在 JavaScript 中可用

这个组件在 PureScript 代码库中工作得非常好。然而，与我们的`react`组件不同，我们的`react-basic`组件也不会自动在 JavaScript 代码中工作。相反，我们需要使用`make`为 PureScript 构建一个组件，使用`toReactComponent`为 JavaScript 构建一个组件。

不过，这两个函数使用相同的组件规范类型，所以新的限制很容易解决。我们将简单地把`initialState`和`render`移到模块范围之外。这样我们就可以将它们直接导入到我们的互操作模块中，提供给`toReactComponent`。

```
--- a/src/purescript/Counter.purs +++ b/src/purescript/Counter.purs @@ -2,21 +2,28 @@ module Counter where

 import Prelude

-import React.Basic (JSX, createComponent, make)
+import React.Basic (Component, JSX, Self, createComponent, make)
 import React.Basic.DOM as R
 import React.Basic.DOM.Events (capture_)

 type Props = { label :: String }

+type State = { count :: Int }
+
+component :: Component Props
+component = createComponent "Counter"
+
 counter :: Props -> JSX
-counter = make (createComponent "Counter") { initialState, render }
-  where
-  initialState = { count: 0 }
-
-  render self =
-    R.button
-      { onClick:
-          capture_ $ self.setState \s -> s { count = s.count + 1 }
-      , children:
-          [ R.text $ self.props.label <> " " <> show self.state.count ]
-      } +counter = make component { initialState, render }
+
+initialState :: State
+initialState = { count: 0 }
+
+render :: Self Props State -> JSX
+render self =
+  R.button
+    { onClick:
+        capture_ $ self.setState \s -> s { count = s.count + 1 }
+    , children:
+        [ R.text $ self.props.label <> " " <> show self.state.count ]
+    } 
```

我们将保持代码不变。接下来，让我们转向互操作模块。它现在应该使用`toReactComponent`来创建一个可从 JavaScript 使用的组件。这个函数接受组件和组件规范，与`make`完全一样，但是它*也*接受一个额外的参数:我们的`jsPropsToProps`函数。

`react-basic`库使互操作比`react`更加明确，但最终我们将编写几乎相同的互操作代码。

```
--- a/src/purescript/Counter/Interop.purs +++ b/src/purescript/Counter/Interop.purs @@ -2,16 +2,15 @@ module Counter.Interop where

 import Prelude

-import Counter (Props, counter)
+import Counter (Props, component, initialState, render)
 import Data.Maybe (fromMaybe)
 import Data.Nullable (Nullable, toMaybe)
-import React (ReactElement)
-import React.Basic (JSX) +import React.Basic (ReactComponent, toReactComponent) 
 type JSProps = { label :: Nullable String }

 jsPropsToProps :: JSProps -> Props
 jsPropsToProps props = { label: fromMaybe "Count:" $ toMaybe props.label }

-jsCounter :: JSProps -> JSX
-jsCounter = counter <<< jsPropsToProps +jsCounter :: ReactComponent JSProps
+jsCounter = toReactComponent jsPropsToProps component { initialState, render } 
```

这个组件现在又可以从 JavaScript 中使用了。

### 引入更复杂的类型

当您需要用 JavaScript 构造一个更复杂的类型时，会发生什么呢？比方说，我们的计数器组件需要两条新信息:

1.  点击计数器后运行的有效回调函数
2.  一种类型，表示函数在单击时应该递增还是递减

我们可以应用相同的过程来适应新的特性。我们将在组件模块中编写惯用的 PureScript，然后在互操作模块中编写翻译。最终结果将是一个在 PureScript 代码或 JavaScript 代码中同样可用的组件，而不会影响您用这两种语言编写代码的方式。

```
--- a/src/purescript/Counter.purs +++ b/src/purescript/Counter.purs @@ -2,14 +2,35 @@ module Counter where

 import Prelude

-import React.Basic (Component, JSX, Self, createComponent, make)
+import Data.Maybe (Maybe(..))
+import Effect (Effect)
+import React.Basic (Component, JSX, Self, createComponent, make, readProps, readState)
 import React.Basic.DOM as R
 import React.Basic.DOM.Events (capture_)

-type Props = { label :: String }
+type Props =
+  { label :: String
+  , onClick :: Int -> Effect Unit
+  , counterType :: CounterType
+  } 
 type State = { count :: Int }

+data CounterType
+  = Increment
+  | Decrement
+
+counterTypeToString :: CounterType -> String
+counterTypeToString = case _ of
+  Increment -> "increment"
+  Decrement -> "decrement"
+
+counterTypeFromString :: String -> Maybe CounterType
+counterTypeFromString = case _ of
+  "increment" -> Just Increment
+  "decrement" -> Just Decrement
+  _ -> Nothing
+
 component :: Component Props
 component = createComponent "Counter"

@@ -23,7 +44,15 @@ render :: Self Props State -> JSX
 render self =
   R.button
     { onClick:
-        capture_ $ self.setState \s -> s { count = s.count + 1 } +        capture_ do
+          state <- readState self
+          props <- readProps self
+          let
+            newCount = case props.counterType of
+              Increment -> add state.count 1
+              Decrement -> sub state.count 1
+          self.setState _ { count = newCount }
+          props.onClick newCount
     , children:
         [ R.text $ self.props.label <> " " <> show self.state.count ]
     } 
```

有了这些变化，我们的计数器可以递减或递增，并且可以在 click 事件发生后运行任意有效的函数。但是我们不能从 JavaScript 运行它:在 JavaScript 中没有像`CounterType`这样的东西，一个普通的 JavaScript 函数像...

```
function onClick(ev) {
  console.log("clicked!");
} 
```

如果作为回调函数提供，将不起作用。由我们的互操作模块来解决这个问题。

我先做代码修改，之后再描述:

```
--- a/src/purescript/Counter/Interop.purs +++ b/src/purescript/Counter/Interop.purs @@ -2,16 +2,27 @@ module Counter.Interop where

 import Prelude

-import Counter (Props, counter)
+import Counter (CounterType(..), Props, component, initialState, render, counterTypeFromString)
 import Data.Maybe (fromMaybe)
 import Data.Nullable (Nullable, toMaybe)
+import Effect.Uncurried (EffectFn1, runEffectFn1)
 import React.Basic (JSX)

-type JSProps = { label :: Nullable String }
+type JSProps =
+  { label :: Nullable String
+  , onClick :: Nullable (EffectFn1 Int Unit)
+  , counterType :: Nullable String
+  } 
 jsPropsToProps :: JSProps -> Props
-jsPropsToProps props = { label: fromMaybe "Count:" $ toMaybe props.label } +jsPropsToProps props =
+  { label:
+      fromMaybe "Count:" $ toMaybe props.label
+  , onClick:
+      fromMaybe mempty $ map runEffectFn1 $ toMaybe props.onClick
+  , counterType:
+      fromMaybe Increment $ counterTypeFromString =<< toMaybe props.counterType
+  } 
```

首先，我更新了 JavaScript 接口，以包含组件接受的两个新字段。

我决定将`CounterType`表示为小写字符串`"increment"`或`"decrement"`，以防止没有提供值(`Nullable`)或提供的值没有意义(`counterTypeFromString`无法解析)。在这两种情况下，组件将默认为递增。

我还决定将`onClick`表示为一个可能丢失的值。但是我没有使用通常的函数，而是将值表示为一个`EffectFn1`:一个有效的、不受干扰的单参数函数。

那种类型值得额外解释一下。在 PureScript 中，默认情况下函数是 curried，有效的函数表示为 thunk。因此，这两个 PureScript 函数:

```
add :: Int -> Int -> Int
log :: String -> Effect Unit 
```

...不对应 JavaScript 中可以调用为`add(a, b)`或`log(str)`的函数。相反，他们更接近于翻译:

```
// each function has only one argument, and multiple arguments are represented
// by nested functions of one argument each.
const add = a => b => a + b;

// effectful functions are thunked so they can be passed around and manipulated
// without being evaluated.
const log = str => () => console.log(str); 
```

对于 JavaScript 来说，这是一种不寻常的编程风格。所以 PureScript 为导出感觉更自然的函数提供了帮助。

*   `Fn*`函数族处理有*个*参数的纯函数
*   `EffectFn*`函数族处理 *N* 参数的有效函数
*   还有一些其他的翻译功能；例如，您可以将`Aff`异步函数转换成 JavaScript 承诺，反之亦然。

如果我们重写我们的 PureScript 定义来使用这些助手:

```
add :: Fn2 Int Int Int
log :: EffectFn1 String Unit 
```

然后我们会得到一个更常用的 JavaScript 接口:

```
const add = (a, b) => a + b;
const log = str => console.log(str); 
```

如果不使用`EffectFn1`，使用计数器组件的 JavaScript 代码将不得不提供一个这样的 thunked 回调函数:

```
<Counter onClick={count => () => console.log("clicked: ", n)} /> 
```

然而，有了`EffectFn1`,我们可以提供通常的代码:

```
<Counter onClick={count => console.log("clicked: ", n)} /> 
```

让我们通过更新`App.js`来利用我们的新组件特性。我们的第一个组件将省略除了`onClick`回调之外的所有属性，该回调将把计数记录到控制台。下一个将指定递减计数器。最后一个组件将坚持原来的接口，只提供一个标签。

```
--- a/src/javascript/App.js +++ b/src/javascript/App.js @@ -5,8 +5,8 @@ function App() {
   return (
     <div>
       <h1>My App</h1>
-      <Counter />
-      <Counter label="Clicks:" /> +      <Counter onClick={n => console.log("clicked: ", n)} />
+      <Counter counterType="decrement" label="Clicks:" />
       <Counter label="Interactions:" />
     </div>
   ); 
```

[![running app with decrement and function](img/6392723290f95b14d94c4772e65068cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ahgbf1nV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thomashoneyman.cimg/2019/running-app-2.gif)

## 包装完毕

在本文中，我们替换了一个简单的计数器，但是同样的步骤也适用于更复杂的组件。

*   使用您喜欢的任何类型和库编写 PureScript 组件。
*   然后，为组件编写一个 interop 模块，在 JavaScript 和 PureScript 之间进行转换。
*   编译结果。
*   像其他 React 组件一样，将其导入 JavaScript 代码。

当您引入像 Redux store 这样的全局上下文时，React 和 PureScript 之间的互操作变得更加复杂，但是这是引导工作，在日常编码中很大程度上仍然看不到。

其他框架(如 Angular)或其他 PureScript UI 库(如 Halogen)之间的互操作不太透明。这不是因为这些库中的限制，而仅仅是因为您现在将框架混合在一起。在 CitizenNet，我们将卤素组件出口给公司的 Angular 和 React 团队使用。

下一次当你面对一个纠结的 JavaScript React 应用并且希望有更好的工具时，尝试引入 PureScript。