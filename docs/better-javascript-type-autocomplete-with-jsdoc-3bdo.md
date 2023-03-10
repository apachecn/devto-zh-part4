# 用 JSdoc 实现更好的 Javascript 类型自动完成

> 原文：<https://dev.to/henryjw/better-javascript-type-autocomplete-with-jsdoc-3bdo>

如果你想跳过讲座，直接进入代码，这里有所有例子的[报告](https://github.com/henryjw/js-types-examples)。

## [T1】简介](#intro)

无论您是否喜欢类型，不可否认的是，在任何语言中拥有类型的自动完成功能都是很好的，因为这意味着我们不必运行代码就能知道对象应该是什么样子(我正看着您呢，JavaScript)。

例如，VS 代码通过在后台自动导入您导入的任何库的类型定义(如果有的话),部分解决了这个问题。

因此，每当 VSCode 自动完成您的`express`方法时，就没有魔法了！它在幕后拉出了[明确键入的](http://definitelytyped.org/)定义。

然而，VSCode(和其他编辑器)解决方案是有限的，因为很多时候编辑器没有办法知道，例如，函数的参数类型。参见示例

*键入自动完成的*
[![](img/3b2e2a6ec64c1cb904dc2a615ffc63e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NcFsUd-F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dekhzldnlyv0wl1snww6.png)

*键入未自动完成的*
[![](img/5620e166e291b2bb645c98f837bef751.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZKvr_VP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tjdgbvzxatknuzaaxd0q.png)

## 方案

有几种方法可以解决这个问题。您选择的方式将取决于您的项目需求，以及您的偏好和需求。

### 行内 JSdoc 类型定义

这种方法允许您内联定义自定义定义。缺点是这样定义的类型不能在定义它们的文件之外重用。

[![](img/09d398a64e94aebf7ac214ea224a4403.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gy2UOUQS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w6zg40emkso4ink0k8gx.PNG)

### 导入类型定义

这种方法允许您将现有 NPM 模块中的类型添加到代码中。要实现这一点，您首先必须将适当的类型定义`npm install`到您的项目中。例如，要添加`express`类型，您需要`npm install --save-dev @types/express`。在[你可以找到任何你需要的类型。](https://microsoft.github.io/TypeSearch/)

[![](img/19b40e33061ced7bfbab5217107cada8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RqDhv9rT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxazh98g43ct087z8wqi.PNG)

## 结论

正如我所展示的，有几种方法可以通过使用 JSDoc 注释来改进类型自动完成。当然，如果你需要实际的类型检查，你将需要使用静态类型语言，比如 Typescript，或者像 [flow](https://github.com/facebook/flow) 这样的工具。

类型注释还有一些其他用途，我在本文中没有详细介绍。请在[这个回购](https://github.com/henryjw/js-types-examples)中随意查看。

## 参考文献

[https://ricostacruz.com/til/typescript-jsdoc](https://ricostacruz.com/til/typescript-jsdoc)
T3】https://jsdoc.app/tags-typedef.html