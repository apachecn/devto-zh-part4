# 解剖反应第 1 部分:JSX

> 原文：<https://dev.to/viniciusersouza/dissecando-react-parte-1-jsx-3g6j>

# 给读者简短的描绘

此文本可能包含一些用于教学目的的轻微不准确之处。某些概念可能无法涵盖整个概念，因此无法简化概念以使其更贴近读者的实际情况，因为本文的目标受众是那些与开发没有接触(或接触很少)的人，特别是在 React。

话虽如此，我希望阅读能与每天激励我的伟大的名字相称，也希望这套导言能帮助那些对从广阔的发展领域开始感兴趣的人踏上征途。

# 编制我们的摘要

这是一系列文本的第一个介绍部分，我打算编写这些文本，以便通过简单但详细的方式解释 React 应用程序的特性，从组件、状态、props、挂接到它们的一般特性。

有鉴于此，本摘要将随着新文章的发布而更新。我们将把我们的*【hello world】*与介绍性最强的部分结合起来，即了解一个组件及其主要特点。

**1 .JSX**
[2 .彩现与不变性〔T4〕](https://dev.to/viniciusersouza/dissecando-react-parte-2-renderizacao-e-imutabilidade-3hh0)

# 我是德乌斯，我是 JSX？

在开始之前，我们需要了解一种工具，这种工具将使我们在 react 上创建应用程序时的生活变得更加轻松。

在开发领域，有一个非常常见的术语叫做*【语法糖】*，它基本上是一种语法促进器，使我们能够加快生产速度，要么使代码更容易阅读，要么更简单，要么更容易写。

**JSX** 是 JavaScript 的延伸(JSX 代表 XML JavaScript，对于好奇的人来说)，允许我们调用反应元素。让我们看看:

```
 const Felino = <img src="./foto-do-meu-pet.png" /> 
```

我们上面写的甚至不是一个*模板字符串*或 *HTML* 。都是 **JavaScript** 。JSX 可以将我们编写的内容解释为 HTML 标记，并“翻译”这些内容，以便在 React 中创建一个元素。

与其它将函数和 html 分离为不同文件的开发模型不同，React 的建议是统一一般逻辑:事件处理、组件呈现、状态更改-因为这些步骤与组件的行为非常相关。

### Uau，mas e agora？

请看以下代码片段:

```
 const Felino = {
        name: "Julieta",
        photo: "./foto-do-meu-pet.png",
        subtitle: "Miau"
    };

    const CartaoDoPet = (
        <h1>{Felino.name}</h1>
        <img src={Felino.photo} />
        <p>{Felino.subtitle}</p>
    ); 
```

很多信息？不要绝望。让我们一步一步来。

我们先用 pet 的信息声明一个物体。

```
 const Felino = {
        name: "Julieta",
        photo: "./foto-do-meu-pet.png",
        subtitle: "Miau"
    }; 
```

我们想把这种信息嵌入——或者用行话来说，*嵌入*——一个组件内。

```
 const CartaoDoPet = (
        <h1>{Felino.name}</h1>
        <img src={Felino.photo} />
        <p>{Felino.subtitle}</p>
    ); 
```

这是我们在 JSX 中输入对象信息的位置。关键字为在其中插入任何有效的 JavaScript 表达式打开了空间。可以是数学运算(`1 + 1`)，对象数据，甚至函数。

在上面的示例中，我们访问了包含 pet 信息的对象的数据，pet 信息将填充我们的组件`CartaoDoPet`。

还有更多。想象一下，如果我们没有朱丽叶小猫的数据来填写我们的卡，在这些情况下，我们希望向用户显示一条消息。

```
 const CartaoDoPet = {
        if(Felino) {
            return (
                <h1>{Felino.name}</h1>
                <img src={Felino.photo} />
                <p>{Felino.subtitle}</p>
            )
        }
        return <h1>Nenhum felino encontrado.</h1>
    } 
```

如果`Felino`是`verdadeiro`，则返回时会填入我们的组件及其数据。对于其他情况，将显示消息！

这是因为 JSX 最终是 JavaScript -并且在我们运行项目时会被翻译为 JavaScript。这就引出 JSX 的第二个问题:

```
 const NomeDoPet = <h1 className="fonteAzul">{Felino.name}</h1> 
```

看起来像 HTML，但我们写的是`className`而不是`class`，而且它在 camelCase。怎么了？

这是因为人们相信使用 camelCase 是因为它是 JavaScript 而不是 HTML。反正`class`是保留字，所以要用`className`来指 html`class`。

### 呼吸困难

我们已经知道如何将值嵌入元素中，而且我们知道，归根结底，一切都会变成 JavaScript—而且我们用 JSX 编写的 HTML 实际上是伪装的 JavaScript，以便于创建 React 元素。

也就是说，参见下面的代码比较:

```
 const item = <h1 className="titulo">Meu titulo</h1>; 
    const item = React.createElement(
        'h1',
        { className: 'titulo' },
        'Meu titulo'
    ); 
```

两者代表着**相同的东西。**jsx 由 Babel 解释，这是一种帮助我们编写现代 javascript 的工具，它将我们的“html”元素转换为`React.createElement`，这是属于 React 的元素创建函数。

此功能对于以下角色非常重要:谁切换到反应将在文档对象模型(DOM)中呈现的内容(换句话说，浏览器将理解并在屏幕上显示的内容)、更新内容、卸载内容等等！

`'Meu Titulo'`代表我们元素的`Children`元素。所以当我们在标签内插入内容时，它会像这个元素一样传递`Children`。请记住这一点，因为稍后我们将看到一些实用程序，用于处理位于元素“`Children`”内的内容。

# 结论

为了不留下非常广泛的阅读，给内容的消化留出空间，我在这里结束这部分。还有更多关于 JSX 的行为在反应器自身的文档中，这是非常详细的。

在下一个文本中，我将简要介绍 React 组件的外观和惯例，以便我们能够初步了解组件的属性和生命周期。

感谢大家的阅读，您的反馈非常重要，因此我可以改进这里的内容。

下次见！