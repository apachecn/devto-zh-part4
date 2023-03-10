# 使用 GraphQL 访问者模式

> 原文：<https://dev.to/ankitjena/using-graphql-visitor-pattern-1fm7>

[![](img/31b95b7f7e5d58a85f0af0665d8e207e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yeCw5WV5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s3kikko8r5xkheauom4f.png) 
GraphQL 是一项神奇的技术，正被越来越多的人采用。作为我在 GSoC ' 19 JBoss 社区工作的一部分，我一直在做 [Graphback](https://aerogear.github.io/graphback) 。为了项目的需要，我们必须从一个模式(SDL 语法)中提取信息到一个我们可以使用的对象中。例如，转换这个

```
type User{
  username: String!
  email: String
} 
```

类似于

```
{
  name: "User",
  fields: [
    {
      name: "username",
      type: "String",
      isNull: false
    },
    {
      name: "email",
      type: "String",
      isNull: true
    }
  ]
} 
```

当 GraphQL 读取 schema 时，它并不将它存储为一个字符串，而是进行大量的解析，将其转换为一个 AST(将详细讨论)。

最好的方法是使用访问者模式。在这篇博文中，我将解释 GraphQL 如何使用访问者模式将 schema 转换成 ASTs。

## **抽象语法树【AST】**

> 当 GraphQL 服务器接收到一个要处理的查询时，它通常以字符串的形式出现。这个字符串必须被标记化并解析成机器能够理解的表示形式。这种表示被称为抽象语法树。

检查抽象语法树的一个很好的工具是 [AST Explorer](https://astexplorer.net) 。该站点允许您快速地将代码从 JavaScript 粘贴到 PHP，再粘贴到 TypeScript，甚至 GraphQL 类型到 UI 中，然后提供最终的抽象语法树。对于上面的例子，AST 是

```
{
  "kind": "Document",
  "definitions": [
    {
      "kind": "ObjectTypeDefinition",
      "name": {
        "kind": "Name",
        "value": "User",
        "loc": {
          "start": 133,
          "end": 137
        }
      },
      "interfaces": [],
      "directives": [],
      "fields": [
        {
          "kind": "FieldDefinition",
          "name": {
            "kind": "Name",
            "value": "username",
            "loc": {
              "start": 141,
              "end": 149
            }
          },
          "arguments": [],
          "type": {
            "kind": "NonNullType",
            "type": {
              "kind": "NamedType",
              "name": {
                "kind": "Name",
                "value": "String",
                "loc": {
                  "start": 151,
                  "end": 157
                }
              },
              "loc": {
                "start": 151,
                "end": 157
              }
            },
            "loc": {
              "start": 151,
              "end": 158
            }
          },
          "directives": [],
          "loc": {
            "start": 141,
            "end": 158
          }
        },
        {
          "kind": "FieldDefinition",
          "name": {
            "kind": "Name",
            "value": "email",
            "loc": {
              "start": 162,
              "end": 167
            }
          },
          "arguments": [],
          "type": {
            "kind": "NamedType",
            "name": {
              "kind": "Name",
              "value": "String",
              "loc": {
                "start": 169,
                "end": 175
              }
            },
            "loc": {
              "start": 169,
              "end": 175
            }
          },
          "directives": [],
          "loc": {
            "start": 162,
            "end": 175
          }
        }
      ],
      "loc": {
        "start": 128,
        "end": 177
      }
    }
  ],
  "loc": {
    "start": 0,
    "end": 178
  }
} 
```

AST 包括许多元数据，比如源中的位置，或者标识符，比如参数名；多亏了这个深度嵌套的 JSON 对象，我们现在拥有了使用 GraphQL 模式所需的所有能力。

## **访客模式**

> *visit()将使用深度优先遍历遍历 AST，在遍历中的每个节点调用访问者的 enter 函数，并在访问该节点及其所有子节点后调用 leave 函数。*
> 
> *通过从 enter 和 leave 函数返回不同的值，访问者的行为可以被改变，包括跳过 AST 的一个子树(通过返回 false)，通过返回值或 null 来编辑 AST 以删除该值，或者通过返回 BREAK 来停止整个遍历。*
> 
> *–graph QL 文档*

Visit 函数进行深度优先遍历，每次遇到 and 节点时调用`enter`函数，遍历完一个节点后调用`leave`函数。当我们从 leave 函数中的一个节点返回一个值时，这个节点就变成了一个返回值。

另外，我们可以使用指定的访问者来访问特定的节点。

```
visit(ast, {
  leave: {
    Kind(node) {
      // leave the "Kind" node
    }
  }
}) 
```

这就是我们解析 AST 所需的全部内容。让我们写代码。

在这个例子中，我们可以看到几种节点，`ObjectTypeDefinition`、`FieldDefinition`、`NotNullType`、`NamedType`和`Name`。让我们按照从小节点到大节点的顺序来编写我们的访问者。

```
const visitor = {
  Name: (node) => {
    return node.value
  },
  NamedType: (node) => {
    return {
      "type": node.name,  //returns the value
      "isNull": true
    }
  },
  NonNullType: (node) => {
    return {
      //spread returned object from NamedType
      ...node.type,     
      "isNull": false
    }
  },
  FieldDefinition: (node) => {
    return {
      //spread returned object from NamedType or NotNullType
      ...node.type,     
      "name": node.name
    }
  },
  ObjectTypeDefinition: (node) => {
    return {
      "name": node.name,
      "fields": node.fields
    }
  }
} 
```

当我们在访客的离开功能中使用这个。我们在结果的定义字段中得到我们需要的输出。

现在实际做访问并得到结果。

```
const ast = parse(schemaText)
const result = visit(ast, { leave: visitor }) 
```

访问者模式真的很有帮助，因为你可以把它运用到你的用例中。这是 GraphQL 中为数不多的优秀工具之一。希望这有所帮助。

### 参考文献

1-[https://graphql.org/graphql-js/language/#visitor](https://graphql.org/graphql-js/language/#visitor)
2-[https://graph QL-code-generator . com/docs/custom-codegen/using-visitor](https://graphql-code-generator.com/docs/custom-codegen/using-visitor)