# 在没有巴别塔的浏览器中运行 JSX

> 原文：<https://dev.to/devalnor/running-jsx-in-your-browser-without-babel-1agc>

只是为了好玩，我想知道我是否可以在一个现代的浏览器中直接运行 React + JSX 代码，而不需要编译和打包。

答案不是火箭科学，在[reactjs.org](https://en.reactjs.org/docs/add-react-to-a-website.html)T2【上解释

```
<script src="https://unpkg.com/react@16/umd/react.production.min.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js" crossorigin></script> 
```

对于 JSX，我们也必须加上巴贝尔

```
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script> 
```

给你！🤗

## 但是！

> 这种方法非常适合学习和创建简单的演示。然而，它会使你的网站运行缓慢，不适合生产。

没错，你的代码没有优化，加载 Babel 可能会太重。

记住，这是为了**只是为了好玩**。还有另一种(也可能是更好的)方式，使用 Preact 的创造者 [@_developit](https://dev.to/_developit) 制作的标准标记模板，使用 [HTM](https://github.com/developit/htm) (Hyperscript 标记标记)作为 JSX 的替代方案。

如果我们不是真的需要巴别塔，下载权重差异是巨大的。

| 解放运动 | 版本 | 装载尺寸 |
| --- | --- | --- |
| 巴比伦式的城市 | Six point two six | < 195KB |
| html 文件的后缀 | 2.2.1 | **> 1KB** |

```
<script src="https://unpkg.com/htm@2.2.1" crossorigin></script>
<script type="module">
// Bind htm with createElement
const html = htm.bind(createElement);
</script> 
```

### 这就好像 JSX 还亮着一样

使用 HTM 时，您编写的语法尽可能接近 JSX:

*   传播道具:`<div ...${props}>`
*   自结束标签:`<div />`
*   组件:`<${Foo}>` *(其中`Foo`为组件参考)*
*   布尔属性:`<div draggable />`

### JSX 上空的改进

实际上将 JSX 风格的语法向前推进了几步！

以下是一些你可以免费获得的人体工程学特性，这些特性在 JSX 是没有的:

*   **不需要运输工具**
*   HTML 的可选引号:`<div class=foo>`
*   组件结束标签:`<${Footer}>footer content<//>`
*   通过[lit-html VSCode extension]和[vim-jsx-pretty plugin]提供语法高亮和语言支持。
*   多个根元素(片段):`<div /><div />`
*   支持 HTML 风格的注释:`<div><!-- comment --></div>`

### 语法差异

使用标记函数`html`
几乎没有语法差异

```
// JSX syntax
function Header({title}) { return <h1>${title}</h1>} 

function App() {    
  const name="World"
  return (
    <div>
      <Header title="Hello, ${name}!"/>
    </div> 
  );
}

// HTM syntax
function Header({title}) { return html`<h1>${title}</h1>`} 

function App() { 
  const name="World"
  return html`
    <div>
      <${Header} title="Hello, ${name}!"/>
    </div>
  `;
} 
```

### 演示

了解了这些差异，您就可以在没有任何 transpiler 的情况下在浏览器中享受 React(或者 Preact)带来的乐趣了。

[https://jsfiddle.net/Devalnor/aeLupb4t/24/embedded/html,result//dark](https://jsfiddle.net/Devalnor/aeLupb4t/24/embedded/html,result//dark)

### 加成(带挂钩的预动作)

如果你想在 Preact 10 中使用钩子(目前在候选版本中)。

```
<script src="https://unpkg.com/preact@10.0.0-rc.1/dist/preact.umd.js" crossorigin></script>
<script src="https://unpkg.com/preact@10.0.0-rc.1/hooks/dist/hooks.umd.js" crossorigin></script>

<script type="module">
const { useState } = preactHooks;
// ...
</script> 
```

在 **dev.to** 上的第一个帖子，如果你喜欢它并且想要更多，不要忘记喜欢❤️