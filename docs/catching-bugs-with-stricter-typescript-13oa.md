# 用更严格的类型脚本捕捉 bug

> 原文：<https://dev.to/juliang/catching-bugs-with-stricter-typescript-13oa>

我们最近在一个相对旧的 TypeScript 项目中启用了`"noImplicitAny"`。它导致了 269 个新错误。大多数都缺少类型注释，但在少数情况下，我们发现代码有问题。这些问题已经存在几个月了，但我们的测试套件没有发现。

### TL；DR；

我们应该更喜欢严格的类型脚本配置来捕捉问题，不仅仅是在编译时，而是在我们键入时(用一个好的 IDE) **。**

我们应该努力保持最新的 TypeScript 版本，以便从不断改进的错误消息中受益；节省开发时间。

## 消息 id 的情况

我们有一个对象为每个键指定一条消息。这在“noImplicitAny”之前是有效的，但是现在我们得到了一个错误:

```
 const id = getMessageIdFromSomeObscureLogic();

  const messages = {
    success: "Everything is awesome!",
    warning: "Something is not entirely correct.",
    error: "An error was found. We can' go on.",
    bananas: "Do you like bananas?",
    example: "I'm running out of ideas for examples.",
    typescript: "Something to do with TypeScript",
    javascript: "Something to do with JavaScript"
  };
  const message = messages[id]; // ERROR! (see below)
} 
```

Enter fullscreen mode Exit fullscreen mode

TS 错误为:

```
Element implicitly has an 'any' type because type '{ success: string; warning: string; error: string; example: string; typescript: string; javascrip...' has no index signature.

```

这里的问题是，`messages`对象的关键字必须是“成功”或“警告”或“错误”或“示例”等。不能只是“任何字符串”。

我们可以用几种方法来解决这个问题:

### 指标签名“修正”

错误消息提到了“索引签名”。我们可以显式扩展类型声明以接受任何字符串作为键，就像这样:

```
const messages: {[index: string]: string} = {
  success: "Everything is awesome!",
  warning: "Something is not entirely correct.",
  error: "An error was found. We can' go on.",
  bananas: "Do you like bananas?",
  example: "I'm running out of ideas for examples.",
  typescript: "Something to do with TypeScript",
  javascript: "Something to do with JavaScript"
};

const message = messages[id]; // no error 
```

Enter fullscreen mode Exit fullscreen mode

### 铅字铸“修”

我们可以把“隐式任意”变成“显式任意”，就像这样:

```
const message = (messages as any)[id]; // no error 
```

Enter fullscreen mode Exit fullscreen mode

这消除了错误，但是推断出的类型是。

还有一种方法:

```
const message = (messages)[id as keyof typeof messages]; // no error 
```

Enter fullscreen mode Exit fullscreen mode

现在`message`的推断类型是`string`。好多了，但只是因为我们告诉 TypeScript“相信我，这是一个有效的键”。

**但是...我们确定它是有效的吗？**

### 适当的修正

我们看了看我们的`id`的类型，根本不是`string`。

请记住，它是从一些模糊的逻辑中获得的。

```
 const id = getMessageIdFromSomeObscureLogic(); 
```

Enter fullscreen mode Exit fullscreen mode

结果是，`id`的类型被推断为如下的联合类型:`'success' | 'warning' | 'error' | 'example' | 'banana' | 'typescript' | 'javascript'`

所以这已经是强类型了。为什么我们会得到一个错误？

原来`messages`对象丢失了`banana`键。

这是一个 bug！通过使 TypeScript 配置更严格一点来捕获。

### 结论

增加我们的 TypeScript 配置的“严格性”可以帮助我们在编译时发现问题，否则这些问题会在测试中发生。

另外，更新我们的 TypeScript 版本会有很大帮助。例如，我们在 TypeScript 3.3 中得到的最初错误是:

```
Element implicitly has an 'any' type because type '{ success: string; warning: string; error: string; example: string; typescript: string; javascrip...' has no index signature.

```

但是有了 TypeScript 3.5，它就有用多了，尤其是第二段，它提到了我们遗漏的`'banana'`属性:

```
Element implicitly has an 'any' type because expression of type '"success" | "warning" | "error" | "example" | "typescript" | "javascript" | "banana"' can't be used to index type '{ success: string; warning: string; error: string; example: string; typescript: string; javascript: string; }'.

Property 'banana' does not exist on type '{ success: string; warning: string; error: string; example: string; typescript: string; javascript: string; }'.

```

那会节省我们相当多的时间。

* * *

皮特·哈迪在 [Unsplash](https://unsplash.com/photos/Peyb6JQPGNU) 上的照片