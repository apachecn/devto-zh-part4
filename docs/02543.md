# 如何用 ClojureScript 构建 Markdown 预览应用

> 原文：<https://dev.to/sophiabrandt/how-to-build-a-markdown-preview-app-with-reagent-5d63>

请注意:这是我不久前在博客上发表的旧帖子。如果你遇到任何问题，打电话给我，我会尽力解决。

***这个 app 是在我们有 React hooks 之前写的。**T3】*

## 为什么要 ClojureScript？

使用 React，您可以构建小组件并组合它们。你从数据设计*，然后让它流过函数(和 React 类)。*

你从编程逻辑开始。然后用类似 HTML 的语法(JSX)添加用户界面。

我喜欢以数据为中心的方法。

它也是 Clojure 和 ClojureScript 的核心。

我讨厌 Javascript 冗长的语法。不要让我从对象模型和`this`的陷阱开始。代码中断是因为您忘记了确保正确绑定您的函数。

用 ClojureScript 思考可以让你从一些可变性的灾难中解脱出来。你知道默认情况下所有的值都是不可变的。

那么程序中到底有什么需要改变的呢？那东西必须是一个`atom`。

对于 Javascript/React，我有时会搞不清到底能改变什么。

哪些函数应该是纯函数？使用无状态功能组件。

哪些组件改变状态？使用 React 类。

ClojureScript 和[试剂](https://reagent-project.github.io/)(ClojureScript 的 React 包装器)区分可变状态和不可变数据。

它们简洁的语言和标记语法更容易阅读。更少的噪音降低了理解代码的障碍。

# 建造某物

[Markdown 预览应用](https://markdown-preview-reagent.firebaseapp.com/)有一个文本区域，你可以在那里输入文本，还有一个实时预览，显示这些文本如何转换成 HTML。

我们来定义一下状态:

```
(ns  mdpreview.state  (:require  [reagent.core  :refer  [atom]]))  (def  inital-value  ; (B)  "## Welcome to Markdown Preview!
Type in some [Markdown text](https://daringfireball.net/projects/markdown/), e.g. in *italic*.
#### About this site
> Markdown Preview was built with Clojurescript and Reagent.
Documentation and more info for this site is available on **[Github](https://github.com/sophiabrandt/markdown-preview)**.
")  (defonce  app-state  (atom  {:value  inital-value}))  ; (A) 
```

Enter fullscreen mode Exit fullscreen mode

`app-state`，一个[试剂原子](https://github.com/reagent-project/reagent/blob/master/doc/ManagingState.md) *(A* )，是一个 hash-map，带有键`:value`和字符串 *(B)* 的值。

现在 UI:

```
(ns  mdpreview.views  (:require  [mdpreview.state  :refer  [app-state]]  ; (A)  [mdpreview.events  :refer  [update-preview,  clear-textarea]]  ["react-markdown"  :as  ReactMarkdown]))  (defn  header  []  [:div  [:h1  "Markdown Preview"]])  (defn  textarea  []  (let  [text  (:value  @app-state)]  ; (B)  [:div  [:textarea  {:placeholder  text  :value  text  :on-focus  #(clear-textarea  %)  ; (C)  :on-change  #(update-preview  %)}]]))  (defn  preview  []  [:div  [:>  ReactMarkdown  {:source  (:value  @app-state)}]])  ; (F)  (defn  app  []  [:div  [header]  [textarea]  [preview]]) 
```

Enter fullscreen mode Exit fullscreen mode

```
(ns  mdpreview.events  (:require  [mdpreview.state  :refer  [app-state]]))  (defn  clear-textarea  [event]  ; (D)  (.preventDefault  event)  (reset!  app-state  nil))  (defn  update-preview  [event]  ; (E)  (.preventDefault  event)  (swap!  app-state  assoc  :value  (..  event  -target  -value))) 
```

Enter fullscreen mode Exit fullscreen mode

该视图有四个区域:

*   一个简单的带有标题的`H1`标签
*   带有文本区域的组件，其中还包含事件处理程序(text area)
*   将文本区域中的所有内容转换为 HTML(预览)的组件
*   最终组件组合了子组件(app)

views.cljs 从 state.cljs 导入`app-state`，我们将事件处理函数存储在一个单独的文件中(参见 *(A)* )。

在文本区域，我们设置了一个绑定到`text`的`let`，在这里我们取消了对`app-state`的引用。取消引用(@-符号)意味着我们[得到了`app-state`原子的值](http://cljs.github.io/api/syntax/#deref)。当原子的任何部分被更新时，试剂将总是重新呈现一个组件(见 *(B)* )。

我们使用`text`作为这个输入字段的占位符和值。当用户触发[合成事件](https://reactjs.org/docs/events.html) `onFocus`或`onChange`时，events.cljs-file 中的函数改变内容(见 *(C)* )。

`on-focus`(在 Hiccup 中我们使用 kebap-case 而不是 camelCase)用`reset!`(参见 *(D)* )将文本区域(和状态)擦拭干净。

`on-Change`取事件目标值并更新状态。每当我们在文本区域中输入时，我们用`swap!`更新`app-state`原子的值(参见 *(E)* )。

预览组件然后获取`app-state`并利用(Javascript)“react-markdown”库。 [React 降价](https://github.com/rexxars/react-markdown)创建一个纯 React 组件。我们使用`reagent/adapt-react-class`(`[:>]`语法)来[使用带有试剂](https://github.com/reagent-project/reagent/blob/master/doc/InteropWithReact.md#creating-reagent-components-from-react-components)的反应组件(参见 *(F)* )。

```
ns  mdpreview.core  (:require  [reagent.core  :as  r]  [mdpreview.views  :as  views]))  (defn  ^:dev/after-load  start  []  (r/render  [views/app]  (.getElementById  js/document  "app")))  (defn  ^:export  main  []  (start)) 
```

Enter fullscreen mode Exit fullscreen mode

最后，core.cljs 渲染应用程序，并使用 [shadow-cljs](http://shadow-cljs.org/) 编译 ClojureScript 代码。

这就是整个应用程序。

该代码可在 [Github](https://github.com/sophiabrandt/markdown-preview) 上获得。我已经将[的现场演示部署到 firebase](https://markdown-preview-reagent.firebaseapp.com/) 上。