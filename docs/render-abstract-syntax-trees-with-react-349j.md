# 用 React 渲染抽象语法树

> 原文：<https://dev.to/clayrisser/render-abstract-syntax-trees-with-react-349j>

[github . com/codejamninja/react-ast](https://github.com/codejamninja/react-ast)

一个软件开发人员在给定的一天里只能编写这么多代码，但是通过元编程，一个人的生产率可以成倍地提高。根据互联网的说法，元编程是“一种编程技术，其中计算机程序将其他程序视为其数据”。元编程非常适合解决可重复的问题。

比如我发布了很多开源代码。这意味着我必须一遍又一遍地写自述文件、投稿指南、许可证和变更日志。使用元编程，我可以构建一个约曼生成器，它根据一些输入为我生成这些文件，比如我的名字、当前年份和项目名称。这正是我几年前用我的 [generator-github-project](https://github.com/codejamninja/generator-github-project) 所做的。

约曼生成器基本上是一个含糖的模板引擎。模板引擎是解决元编程的好方法，但是它们有一个严重的缺陷。他们非常固执己见，几乎不可能作曲。换句话说，它们解决的是一个非常具体的问题，而不是别的。如果两个生成器重叠，在它们之间重用代码几乎是不可能的。

另一种没有这种限制的方法是操作抽象语法树，通常简称为 AST。AST 基本上是源代码的树形表示。抽象语法树是一个强大的工具，但是它们天生就很难使用。

在元编程领域，ast 和模板引擎经常面临另一个问题。这些项目中使用的代码往往会很快变得杂乱无章。这个标记很难读懂，其他开发人员也几乎不可能读懂。

[![npm](img/3011c841ddb004948ad85811cbb4fa39.png)](https://www.npmjs.com/package/react-ast)
[![GitHub stars](img/8ea4d05a02d475ba4ff66e23060acf99.png)T6】](https://github.com/codejamninja/react-ast)

使用 React，我能够构建一个工具，使与 ASTs 的交互变得容易。React 本质上是可组合的。这意味着我可以将代码生成器分解成小的模块化组件，这些组件可以在许多项目中使用。与 ast 交互的传统技术通常涉及命令式编程，很难可视化和阅读。因为 React 本质上是声明性的，所以理解元编程背后的逻辑是非常自然的，并且可以很好地跨团队扩展。

在构建了一个与 Babel 的 AST 交互的大型代码生成器之后，我有了 React AST 的想法。我想为 [GJS](https://wiki.gnome.org/Attic/Gjs) 、 [Gnome 的](https://gnome.org) JavaScript 运行时引擎创建类型绑定。具体来说，我想为 GTK 与 GJS 的集成创建绑定。然而，这项任务令人生畏。这将需要大约 30，000 行打字稿绑定。显而易见的解决方案是元编程。我构建了一个名为 [ts-gir](https://github.com/codejamninja/ts-gir) 的工具，它从 [Gir](https://developer.gnome.org/programming-guidelines/stable/introspection.html.en) 文件生成 TypeScript 绑定。该工具是成功的。你可以在这里看到装订的。

然而，虽然我为自己节省了几个月的时间，但这个过程是痛苦的。尽管我尽了最大的努力，ts-gir 中的代码还是被删除了，对任何人来说为这个项目做贡献都不容易。具有讽刺意味的是，我最初构建 TypeScript 绑定的原因是为了构建到 GTK 的 React 绑定。我使用元编程为 [react-gtk](https://github.com/codejamninja/react-gtk) 构建了一个概念证明，但是代码生成器几乎不可维护。为了正确推进项目，我将不得不重写绑定。

想到 React、元编程、ASTs 和代码生成器，我不可避免地想到为 ASTs 构建一个 React 渲染器。React AST 无疑为 met 编程带来了一种新的思维方式。这将使我能够继续努力构建一个 React 渲染器来渲染 GTK。

好了，历史讲够了。这实际上是如何工作的？

我在下面提供了一个非常简单的教程，让你了解使用 React AST 进行元编程的感觉。

首先，需要安装 npm 模块。只需运行以下命令。

```
npm install --save react-ast 
```

Enter fullscreen mode Exit fullscreen mode

创建一个名为`renderCode.js`的文件，并将下面的内容放入文件中。

```
import React from 'react';
import {
  ClassDeclaration,
  Code,
  FunctionDeclaration,
  Param,
  ReturnStatement,
  render
} from 'react-ast';

const code = render(
  <>
    <ClassDeclaration name="Hello" superClassName="Array">
      <Code>hello = 'world'</Code>
    </ClassDeclaration>
    <FunctionDeclaration
      name="add"
      params={[<Param key="a">a</Param>, <Param key="b">b</Param>]}
      returnStatement={<ReturnStatement>result</ReturnStatement>}
    >
      <Code>const result=a+b</Code>
    </FunctionDeclaration>
  </> );

console.log(code); 
```

Enter fullscreen mode Exit fullscreen mode

确保您已经为项目设置了 babel。

*.babelrc*

```
{
  "presets": [
    [
      "@babel/env",
      {
        "targets": {
          "node": "6"
        }
      }
    ],
    "@babel/react"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

通过运行以下命令生成代码。

```
babel-node ./renderCode.js 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到呈现的代码打印到控制台。

```
class Hello extends Array {
  hello = 'world';
}

function add(a, b) {
  const result = a + b;
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您想呈现 AST 而不是代码，只需使用`renderAst`函数。

```
import React from 'react';
import {
  renderAst,
  ClassDeclaration
} from 'react-ast';

const ast = renderAst(
  <ClassDeclaration name="Hello" />
);

console.log(ast); 
```

Enter fullscreen mode Exit fullscreen mode

AST 将类似于下面这样。

```
{ type: 'File',
  program:
   { type: 'Program',
     body: [ [Object] ],
     directives: [],
     sourceType: 'script',
     interpreter: null },
  comments: [],
  tokens: [] } 
```

Enter fullscreen mode Exit fullscreen mode

以这种方式生成代码有几个非常强大的功能。阅读和理解正在发生的事情是非常容易的。这提高了生产率，减少了出错的机会。

通常，元编程项目的作者会选择生成的输出的编码标准。但是，由于 React AST 实际上会确认生成器用户的编码标准。

它也是非常可组合的。例如，可重用的代码片段可以很容易地抽象成一个 React AST 组件，然后可以与其他 React AST 组件组合在一起。

现在只有基本的 React AST 组件，如`ClassDeclaration`、`FunctionDeclaration`和`Code`。然而，这些基本组件可以被组合成更高级的组件，如`ReactComponent`或`ExpressRoute`。

我希望看到更多的社区为这个项目做出贡献。构建一个可重用和可组合的 React AST 组件库有很大的潜力。

查看[投稿指南](https://github.com/codejamninja/react-ast/blob/master/CONTRIBUTING.md)

如果您有任何问题或意见，您可以通过下面的链接提交反馈并提出问题。

提交一个[问题](https://github.com/codejamninja/react-ast/issues/new)

[github . com/codejamninja/react-ast](https://github.com/codejamninja/react-ast)