# 可选链接-活着是多么美好的时光！

> 原文：<https://dev.to/jimatjibba/optional-chaining-what-a-wonderful-time-to-be-alive-341d>

在处理数据时，我们经常需要遍历树状结构来查找和使用特定的数据。例如，从您系统中的用户处检索地址

```
{
  data: {
    user: {
      name: "John Doe",
      age: 30,
      address: {
        firstLine: "19 Pound Lane",
        secondLine: "Botley",
        city: "Oxford",
        postcode: "OX20 1RS"
      }
    }
  }
} 
```

要得到`city`，你需要做这样的事情:

```
const city = data.user.address.city; 
```

这很简单，但是我们做了很多假设，并且非常信任所提供的数据。如果`user`不存在呢？那么我们就有一个问题。我们会得到一个`Uncaught TypeError`。而且我们的 app 可能会崩溃！！

[![Tobias Sobbing](img/16c499d6bcd0a46ff8f8fef29ed0075a.png)](https://i.giphy.com/media/13699jZW4PZdx6/giphy.gif)

为了防止这种情况，我们有许多选择。我们可以嵌套逻辑或三元语句，使用带有`&&`的逻辑表达式，或者将我们的代码包装在 try/catch 块中，但这些都会导致难以阅读的冗长代码。也有像 [lodash](https://lodash.com/) 这样的库中的帮助函数。

```
// Examples
// nested logic
if(data) {
  if(user) {
    if(address) {
      [...]
    }
  }
}

// nested ternary
const city = !data
    ? undefined
    : !data.user
      ? undefined
      [...]

// lodash
const city = _.get(data, "user.address.city", undefined); 
```

## 可选链接符——我们穿着闪亮盔甲的骑士

[![Our Knight in shining armour](img/c100f73801b211a1eb0e170c59b0e1f0.png)](https://i.giphy.com/media/WTl5quTRtvj32/giphy.gif)

MDN 说得好:

> 可选的链接操作符？。允许读取位于连接对象链深处的属性值，而不必明确验证链中的每个引用是否有效。那个？。运算符的功能类似于。链接运算符，不同之处在于，如果引用为空或未定义，表达式不会导致错误，而是使用返回值 undefined 进行短路。当用于函数调用时，如果给定的函数不存在，则返回 undefined。

### Js

这意味着我们可以使用这个新的操作符`?`，而不是必须检查每个属性来确保我们没有破坏任何东西。整洁多了！

```
const city = data?.user?.address?.city; 
```

这是目前的第三阶段，但是在[巴别塔-插件-提议-可选-链接转换](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)的帮助下，今天就可以使用。

但是，对于 Typescript 开发人员来说，这给我们留下了什么呢？

这种转换目前不适用于 Typescript。Typescript 团队正在等待提案的最终确定，并计划在 3.7 版中发布。但目前 3.7 版还没有发布日期，所以我们需要一些临时的东西。

### Ts

这就是 ts-optchain 前来救援的地方。它不像`?`语法那样简洁，但是支持缺省值，保留所有类型，并且可以在今天使用！！

对我来说那听起来像是一个胜利！

一旦你安装了软件包:

```
yarn add -D ts-optchain 
```

您需要将插件添加到您的`tsconfig.json`中，然后就可以开始了。

```
//  tsconfig.json  {  "compilerOptions":  {  "plugins":  [{  "transform":  "ts-optchain/transform"  }]  }  } 
```

有了这个安装和配置，我们可以以更简洁的方式访问我们的`city`属性。

```
import { oc } from "ts-optchain";

interface User {
  data?: {
    user?: {
      name?: string;
      age?: number;
      address?: {
        firstLine?: string;
        secondLine?: string;
        city?: string;
        postcode?: string;
      };
    };
  };
}

const user: User = {
  data: {
    user: {
      name: "John Doe",
      age: 30,
      address: {
        firstLine: "19 Pound Lane",
        secondLine: "Botley",
        city: "Oxford",
        postcode: "OX20 1RS"
      }
    }
  }
};

console.log(oc(user).data.user.address.city("City not Found"));
console.log(
  (user &&
    user.data &&
    user.data.user &&
    user.data.user.address &&
    user.data.user.address.city) ||
    "City not Found"
); 
```

哇第二个选择:

[![Chris Morris](img/bf1dec75c7c2ff5ac9796aecdc497dba.png)](https://i.giphy.com/media/SyKalfHDAOXx6/giphy.gif)

您可以看到，这使我们能够编写更少的冗长代码，这意味着我们的代码将更容易阅读和重构。

## 结论

这是一篇分享 Javascript 在不久的将来(希望如此)将会出现的伟大事物的快速帖子，并且它们仍然可以在今天被使用，只需要一点额外的工作。可选的链接操作符是对语言的一个很好的补充！现在就开始用吧。