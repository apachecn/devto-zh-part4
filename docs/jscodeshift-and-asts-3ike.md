# JSCodeshift 和 ASTs

> 原文：<https://dev.to/mattchewone/jscodeshift-and-asts-3ike>

[![Tree](img/13f2c9b16e8fe32cbb8e8589b6774970.png)](///static/0115ca5ba07a8ac63c39f121c3314d36/e3340/tree.jpg)

我一直在为 [CanJS](https://canjs.com) 做一些开源工具。我正在开发迁移工具，它用来帮助将项目升级到最新版本。我们使用 [jscodeshift](https://github.com/facebook/jscodeshift) 来处理这些迁移，这是我第一次使用和编写它们。

`jscodeshift`允许你创建复杂的转换，可以读取源文件并解析成[抽象语法树(AST)](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 。然后我们可以操纵它并重新生成源代码。

## AST 浏览器

AST Explorer 是一个**非常**有用的工具，帮助我理解 AST，我一直用它在浏览器中实时创建我的转换。在我写转换的时候看到转换的发生对我很有帮助。

要启用`AST Explorer`中的`transforms`，切换顶部导航栏中的`transform`选项。通过打开它，你可以选择不同类型的转换，因为我正在使用`jscodeshift`选项。

## 例子

让我们完成一个简单的转换，将 [CanJS](https://canjs.com) `Component`转换成一个`StacheElement`类。

```
Component.extend({
 tag: 'my-app',
 view: `<h1>Hello World!</h1>`
}) 
```

会变成

```
class MyApp extends StacheElement {
 static get view () {
   return `<h1>Hello World!</h1>`
 }
}
customElements.define('my-app', MyApp) 
```

下面是用于进行最基本形式转换的代码要点: