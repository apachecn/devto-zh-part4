# 编写我的第一个巴别塔插件

> 原文：<https://dev.to/varunzxzx/writing-my-first-babel-plugin-1i7o>

在深入探讨 Babel 插件之前，我们先来了解一下 Babel 以及它的工作原理？Babel 是一个 transpiler，可以将您的 ES20XX、JSX 等代码转换到 ES2015，以获得更好的浏览器兼容性。随着新 API 的频繁引入和语言标准的不断更新，浏览器不会以这样的速度自我更新。巴贝尔来救援了。它允许开发人员使用现代语言特性，而不用担心浏览器的兼容性。

如果你曾经开发过一个 React 应用程序，那么你一直在使用 Babel。React 使用 JSX，这不是一个标准的 Javascript 语法。正是巴别塔将你所有美妙的 JSX 转换成浏览器能够理解的东西。巴别塔不仅仅限于 JSX 或 React。它支持所有现代 API，如 async/await。

## 它是如何工作的？

巴别塔经历了三个主要阶段来表演这个魔术:

1.  从语法上分析
2.  改变
3.  产生

#### 解析

解析阶段获取代码并输出抽象语法树或 AST。

> “一棵**抽象语法树(AST)** ，或者仅仅是**语法树**，是源代码的抽象语法结构的树表示”——维基百科

例如:

```
function square(n) {
  return n * n;
} 
```

上述程序的 AST 表示如下:

```
- FunctionDeclaration:
 - id:
 - Identifier:
 - name: square
 - params [1]
 - Identifier
 - name: n
 - body:
 - BlockStatement
 - body [1]
 - ReturnStatement
 - argument
 - BinaryExpression
 - operator: \*
 - left
 - Identifier
 - name: n
 - right
 - Identifier
 - name: n 
```

或者像这样作为一个 JavaScript 对象:

```
{
  type: "FunctionDeclaration",
  id: {
    type: "Identifier",
    name: "square"
  },
  params: [{
    type: "Identifier",
    name: "n"
  }],
  body: {
    type: "BlockStatement",
    body: [{
      type: "ReturnStatement",
      argument: {
        type: "BinaryExpression",
        operator: "*",
        left: {
          type: "Identifier",
          name: "n"
        },
        right: {
          type: "Identifier",
          name: "n"
        }
      }
    }]
  }
} 
```

> 注意:为了简单起见，删除了一些属性。

#### 变换

转换阶段获取一个 AST 并遍历它，在此过程中添加、更新和删除节点。这是巴别塔插件运作的地方。我们稍后将回到这一部分。

#### 生成

代码生成阶段获取最终的 AST，并将其转换回代码字符串。

## Babel Plugin

现在当我们知道了巴别塔是如何运作的，让我们深入挖掘巴别塔插件。Babel plugins 允许开发者以他们想要的任何方式转换他们的代码。Babel 抽象了解析和生成代码的复杂操作，并公开了一组神奇的 API 来根据我们的需要转换代码。我们将使用这些 API 来构建我们的插件。

正如你现在已经知道的，巴别塔插件运行在**转换**阶段。在这一阶段，我们得到一个类似于代码表示的对象。所以，我们只需要操纵那个对象来改变代码。

让我们开始编写我们的巴别塔插件。因此，有一个小小的 css-in-js 库，由 [siddharthkp](https://twitter.com/siddharthkp) 命名为 [use-css](https://github.com/siddharthkp/use-css#readme) 。这是它的一个小演示:

```
import useCSS from 'use-css';

function Oopsy(props) {
  const className = useCSS(`
    font-size: 21px;
    font-style: italic;
    color: ${props.color};
    &:hover {
      font-weight: bold;
    }
  `);

  return <div className={className}>because why not?</div>; }

render(<Oopsy color="green" />); 
```

我们将创建一个 babel 插件，它提取所有的 css 并将其放入静态文件中，因为为什么不呢:P
现在，问题来了，你将如何处理像上面例子中的动态值。我也有一个魔法咒语。

我们的插件从一个函数开始，该函数接收当前的巴别塔实例作为参数。

```
export default function(babel) {
  // plugin contents
} 
```

然后我们用属性访问者返回一个对象

```
export default function(babel) {
  return {
    visitor: {
      // visitor contents
    },
  };
} 
```

那么，什么是**访客**？在转换阶段(我们的插件运行的地方)，巴别塔遍历树意味着它将访问每个节点。 **Visitor** 是一个具有一些方法的对象，一旦找到特定类型的节点，这些方法就会被 babel 调用。你可以在上面 AST 的对象表示中看到，每个节点都有特定的类型。我们将使用这些信息来建立我们的访问者。
下面的例子会更清楚:

```
const MyVisitor = {
  Identifier() {
    console.log('Called!');
  },
}; 
```

现在，每次 babel 访问“Identifier”类型的节点时，都会调用 Identifier 方法。这些访问者方法接收节点的路径作为参数。要了解不同类型的节点，可以使用 [AST explorer](https://astexplorer.net/) 。这将是你构建巴别塔插件时最常用的资源。要操作任何对象，你应该知道对象的结构。您可以使用这个非常方便的工具查看任何代码的 AST 表示。

对于我们的插件，我们需要获得所有的“useCSS”调用，因为这样我们就能够获得所有的样式，并把它放在一个静态样式中。然后，我们将使用类名替换函数调用，并删除所有导入。所以下面的代码

```
import useCSS from 'use-css';

function Oopsy(props) {
  const className = useCSS(`
    font-size: 21px;
    font-style: italic;
    &:hover {
      font-weight: bold;
    }
  `);

  return <div className={className}>because why not?</div>; }

render(<Oopsy color="green" />); 
```

将被转化为

```
function Oopsy(props) {
  const className = “4vg68s”

  return <div className={className}>because why not?</div> }

render(<Oopsy color="green" />) 
```

所有的样式都将被转移到 bundle.css 中。对于动态值，我们将使用 css 变量(魔法)。例如:

```
import useCSS from 'use-css';

function Oopsy(props) {
  const className = useCSS(`
    font-size: 21px;
    font-style: italic;
    color: ${props.color};
    &:hover {
      font-weight: bold;
    }
  `);

  return <div className={className}>because why not?</div>; }

render(<Oopsy color="green" />); 
```

将被转换为:

```
//js
function Oopsy(props) {
  const className = “4vg68s”

  return (
  <div className={className} style={{“--sj55zd”: props.color}}>
    because why not?
  </div>
  )
}

render(<Oopsy color="green" />)

//bundle.css
.4vg68s{
font-size:21px;
font-style:italic;
color:var(--sj55zd);
}
.4vg68s:hover {
font-weight:bold;
} 
```

要获得所有的“useCSS”函数调用，我们只需这样做:

```
export default function(babel) {
  return {
    visitor: {
      CallExpression(path) {
        if (path.node.callee.name !== 'useCSS') return;
        // do the thing
      },
    },
  };
} 
```

**CallExpression** 是我们需要的节点类型，该节点的**被调用者**属性为我们提供了函数名。同样，我使用 AST Explorer 来查找节点的所有属性。
现在，我们需要将模板文字(样式)传递给函数。为此，我们将遍历它的子节点并获取样式。

```
export default function(babel) {
  return {
    visitor: {
      CallExpression(path) {
        if (path.node.callee.name !== 'useCSS') return;
        path.traverse({
          // nested visitor
        });
      },
    },
  };
} 
```

“遍历”方法用于遍历一个节点的子节点。它接受一个“访问者”对象。现在，在这个访问者中，我们只需要模板文字。所以，

```
export default function(babel) {
  return {
    visitor: {
      CallExpression(path) {
        if (path.node.callee.name !== 'useCSS') return;
        path.traverse({
          TemplateLiteral(path) {
            // do the thing
          },
        });
      },
    },
  };
} 
```

现在，模板文字有两个我们将要用到的主要属性，“准”和“表达式”。这两个属性都是一个数组。“准”包含字符串文字的字符串部分,“表达式”包含字符串文字中使用的变量。例如下面的代码:

```
`I love ${theThing} and ${anotherThing}`; 
```

将表示为:

```
{
  “type”: “TemplateLiteral”,
  “quasis”: [“I love”, “and”],
  “expressions: [“theThing”, “anotherThing”],
  ...
} 
```

> 注意:值不是以字符串数组的形式存储，而是以节点数组的形式存储。但是，我们可以从节点中得到这些值。

如果字符串中没有任何变量。那么“表达式”将是空的，“准”将只有一个节点(整个字符串)。让我们编写代码来合并这些值以获得样式

```
TemplateLiteral(path) {
  const templateLiteral = path.node; //get the node
  const quasis = [...templateLiteral.quasis]; //get the quasis
  let staticStyle = “”;

  // check whether it includes variables or not
  if (quasis.length !== 1) {
   quasis.map((el, i) => {
    // the last quasis is marked as tail in AST
    if (!el.tail) {
      const expr = templateLiteral.expressions[i];
      // check whether the value is an object’s property
      // or a normal variable(babel’s api, more on it later)
      if (t.isMemberExpression(expr)) {
        value = `${expr.object.name}.${expr.property.name}`;
      } else {
        value = expr.name;
      }
      // We are going to use this ‘value’ variable later on.
      // It stores the name of the variable.
    }
    staticStyle += el.value.cooked;
   });
 } else {
    staticStyle = quasis[0].value.cooked;
 }
} 
```

> 注意:我不打算描述节点的所有属性。AST Explorer 是最好的😄
> 上面的代码获取所有的字符串部分(quasis)并存储到变量 **staticStyle** 。我只展示了如何获得变量，但我们还没有使用它。现在，如果你注意到，当 quasis tail(该节点的属性)为 falsy 时，我正在从**表达式**中获取值。因为尾部的准星代表字符串的结尾(后面没有变量)。

现在，我们需要做两件事来处理动态值。

1.  生成一个随机的 css 变量(它必须是唯一的)
2.  将该变量添加到 JSX 的样式属性中

我们将使用 use-css 使用的同一个哈希库来创建唯一的 css 变量。第二步并不复杂。我们需要将 css 变量及其值添加到需要动态样式的 JSX 中。但是，怎样才能找到 JSX 呢？

找到所需 JSX 的唯一方法是找到用户代码中定义的变量。如果你看上面的 use-css 的演示。函数返回类名，用户将它存储在一个变量中，并在 JSX 中使用它。我们将追踪这个变量，得到 JSX。
现在，又出现了另一个问题。我们在插件中使用 CSS 函数调用 visitor。那么，如何到达居住在其他地方的 JSX，在同一个访问者里面。这是不可能的。因此，我们将在某个地方存储所需的信息，并为 JSX 创建一个新的访问者，并将使用存储的信息来操作 JSX。

我们要存储的信息是什么。为此我们需要三个值

1.  用户代码中的变量(用于查找 JSX)
2.  我们生成的 css 变量
3.  我们的 css 变量保存的值

让我们为它写代码

```
import hash from "@emotion/hash";
const styles = {};

TemplateLiteral(path) {
    ...
    const identifier = path.parentPath.parentPath.node.id.name;

    ...
    // generating unique css variable name
    cssVarName = hash(value);

    // adding it to the style
    el.value.cooked += `var(--${cssVarName})`;

    // add the css variabe name with its value to
    // the styles obj(dynamic styles)
    styles[identifier] = [cssVarName, value];

    ...
} 
```

Babel 提供了节点的父路径，所以我用它来访问用户代码变量。此外，我将 usercode 变量作为键存储在 **styles** 对象中，因为它将用于查找 JSX。现在，我们可以找到所需的 JSX 了。让我们直接进入代码:

```
export default function(babel) {
  const t = babel.types;
  return {
    visitor: {
      …,
      JSXAttribute(path) {
        if (path.node.value.type !== "JSXExpressionContainer") return;
        if (!styles[path.node.value.expression.name]) return;
        const identifier = path.node.value.expression.name;
        // add style attribute to JSX for dynamic styles
        path.parentPath.node.attributes.push(
          t.JSXAttribute(
            t.JSXIdentifier("style"),
            t.JSXExpressionContainer(
              t.ObjectExpression([
                t.ObjectProperty(
                  t.StringLiteral(`--${styles[identifier][0]}`),
                  t.Identifier(styles[identifier][1])
                )
              ])
            )
          )
        );
      }
    }
  };
} 
```

代码太多了。我们来分解一下。因此，我正在搜索 **JSXAttribute** ，如果属性值不是用户定义的变量，那么第一个“if”会提前退出函数。现在，第二个“if”检查我们在那个节点中是否有东西要修改。我们只需检查 styles 对象中是否有用户定义变量的条目。因为我们不需要改变没有使用任何动态样式的 JSX。

一旦我们通过了这些测试，我们就开始改变节点。现在，我们正在做一些不同的事情。前面，我们从节点中获得了一些信息，但是现在我们必须构建一个节点，即 JSX 风格属性。正如我所说，Babel 提供了一些神奇的 API，让我们的生活变得更加轻松。我们可以使用“babel.types”中的构建器方法来做到这一点。当我们从字符串文字中提取样式时，我们以前也使用过“babel.types”中的一个帮助函数。构建器的方法名就是您想要构建的节点类型的名称，只是第一个字母小写。例如，如果您想构建一个 MemberExpression(节点类型),您可以使用

```
const t = babel.types;
t.memberExpression(...) 
```

这些生成器的参数由节点定义决定，它们都可以在这里找到。
节点定义如下:

```
defineType("MemberExpression", {
  builder: ["object", "property", "computed"],
  visitor: ["object", "property"],
  aliases: ["Expression", "LVal"],
  ...
}); 
```

通过查看**构建器**属性，您可以看到调用构建器方法(t.memberExpression)所需的 3 个参数。
这对**成员表达式**来说看起来非常简单。但是，这里我们正在构建一个 JSX 属性。我想让你探索这个属性是如何构建的(参考使用[节点定义](https://github.com/babel/babel/tree/master/packages/babel-types/src/definitions)和 [AST 浏览器](http://astexplorer.net)

我们忘记了将样式保存到 css 文件中。让我们现在就做吧。

```
import { writeFile } from "fs";

export default function(babel) {
  return {
    visitor: {
        ...
        } else {
          staticStyle = quasis[0].value.cooked;
        }

        writeFile("bundle.css", staticStyle, function(err) {
          if (err) throw err;
        });
    }
  };
} 
```

这将生成把所有静态样式放到 **bundle.css** 中。但是，该库也接受 scss 之类的语法，样式还不包括任何类名。所以，我们也必须对它进行预处理。我们将使用 use-css 使用的相同的预处理器和相同的哈希库来生成类名。

```
function getClassName(styles) {
  return "c" + hash(styles);
}
export default function(babel) {
  return {
    visitor: {
        ...
        } else {
          staticStyle = quasis[0].value.cooked;
        }
        // convert string literal into string
        const finalStaticStyle = staticStyle.replace(/\r?\n|\r|\s/g, "");

        className = getClassName(finalStaticStyle);

        const rawCSS = stylis("." + className, finalStaticStyle);

        writeFile("bundle.css", rawCSS, function(err) {
          if (err) throw err;
        });
    }
  };
} 
```

现在我们的 babel 插件将所有 css 保存到一个静态文件中，同时也管理动态样式。因此，如果我们在构建期间完成了所有这些工作。为什么要重复预处理、散列等。在运行时。我们需要删除所有的 useCSS 调用，并用我们生成的类名替换它。为此，我将简单地使用 babel 提供的 helper 方法。你可以在这里找到所有的巴别塔助手功能。

```
path.replaceWith(t.StringLiteral(className)); 
```

我们完成了 99.9%的工作。我们还必须删除 use-css 导入。我厌倦了写这篇博客😅。所以，我把这个任务交给你✌️.
完整的插件可以在[这里](https://github.com/varunzxzx/babel-plugin-use-css)找到。

通过构建巴别塔插件，你会觉得你释放了一些新技能。社区已经建立了各种很酷的东西，比如 codemods 之类的。你唯一需要做的事情就是爱 ASTs😉。

在构建这个插件时，我使用了[巴别塔插件手册](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md)和 [AST 浏览器](https://astexplorer.net)作为参考。你不可能得到比这两个更好的资源。我强烈建议你阅读手册，它包含了你构建插件所需的所有 API。

如果你建造了一些令人惊叹的东西，请友好地 ping 我。如果我的帖子能激励别人，我会非常高兴。