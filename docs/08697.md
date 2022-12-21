# 预行动初学者教程-第 3 部分:JSX

> 原文：<https://dev.to/solarliner/a-beginners-tutorial-to-preact-part-3-jsx-1k68>

在前一部分中，我们看到了如何使从 JavaScript 创建内容变得更加容易，但是我们仍然有一种相当乏味的方式来表达我们的 DOM 节点。让我们看看如何能做得更好一点。

# JSX，或者应该称之为 XML-in-JS

当前的`h`函数有很多优点。它动态地创建了一个 DOM 元素树，这种方式很简洁，在视觉上与它生成的树没有太大的不同。

然而，所有的“功能调用”将很快成为一个眼中钉，一个键盘痛导航复杂的元素很快成为精神紧张。如果我告诉你我们可以把 HTML 重新放入 JavaScript 中，会怎么样？

这正是 JSX。它允许开发人员以类似 HTML(或者更确切地说，类似 XML)的方式编写`h`函数调用。不仅要写的东西更少，而且 JSX 代码比任何常规大小的代码更清楚地强调了数据和结构。

它看起来像什么？让我们回顾一下上一部分的项目:

```
const root = (
  <div id="app">
    <header>
      <h1>My custom-generated web page</h1>
      <h2>How neat is that?</h2>
    </header>
    <section>
      <div class="container">
        <p class="content">That's pretty neat.</p>
      </div>
    </section>
  </div>
);
// Prepending here because scripts should ideally be at the end of the body
document.body.prepend(root); 
```

我们可以清楚地看到这个“简单的语法糖”技巧是如何立即提高我们函数的可读性的。

但是，如果您尝试使用我们自定义的`h`函数(稍后会详细介绍)，您将会看到以下错误:

```
TypeError: Can't convert null to object 
```

这是因为当一个标签在 JSX 中没有任何属性时，转换后的 JS 使用`null`而不是传递一个空对象作为参数。我们需要考虑到这一点，并在应用属性之前检查 null:

```
/**
 * Creates an element and returns it
 * @param tag Tag name, like the HTML tag
 * @param attrs Object of attributes to add to the node
 * @param children Children nodes to append into the created element
 */
function h(
  tag: string,
  attrs: object | null,
  ...children: (HTMLElement | string)[]
) {
  const el = document.createElement(tag);

  // **ADDED**: Append given attributes (if defined)
  if (attrs !== null)
    for (const key of Object.keys(attrs)) {
      el.setAttribute(key, attrs[key]);
    }
  // Insert children
  for (const child of children) {
    // Text is special in the DOM, we must treat it accordingly
    if (typeof child === "string") {
      el.appendChild(document.createTextNode(child));
    } else {
      el.appendChild(child);
    }
  }
  return el;
} 
```

# 在你的项目中使用 JSX

JSX 的问题在于你不能直接使用它。如果这样做，您选择的 JavaScript 引擎将会抱怨——因为 JSX 不是 JavaScript 语言的一部分——它是放在 JavaScript 之上的一个技巧，用来帮助编写嵌套的`h`函数。这就是巴别尔的用武之地。

Babel 有插件可以将你的 JSX 转换成 h 函数调用，这反过来又使得 JavaScript 再次有效。对 Babel 的适当介绍不在本文的讨论范围之内，如果您不了解 Babel，您绝对应该去看看，因为它已经成为 JavaScript 开发人员日常工具的一部分。

启用 JSX 的另一个解决方案是使用 TypeScript。TS transpiler 支持 JSX，你可以用它来转换它。同样，配置 TypeScript 不在讨论范围之内，而且该项目的文档也很棒，所以如果需要的话，一定要去看看。

# 在下一部分

我们将跳出枯燥但必要的理论，使用我们在 Preact 上所学的知识创建我们的第一个项目！