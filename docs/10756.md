# TypeScript 中的严格函数类型:协方差、逆变和双变量

> 原文：<https://dev.to/miloszpp/strict-function-types-in-typescript-covariance-contravariance-and-bivariance-53kc>

让我们来谈谈一个不太为人所知的严格类型检查选项- `strictFunctionTypes`。它帮助你避免另一类错误，同时也是学习一些基本计算机科学概念的绝佳机会:**协方差**、**逆变**和**双变量**。

TypeScript 2.6 中引入了严格的函数类型检查。它在打字稿文档中的定义是指一个神秘的术语:**双变量**。

> 禁用函数类型的双变量参数检查。

## `strictFunctionTypes`能抓到什么虫子？

首先，让我们看一个通过启用这个标志可以捕获的 bug 的例子。

在下面的例子中，`fetchArticle`是一个函数，它接受一个在从某个后端服务获取一篇文章后执行的回调。

```
interface Article {
    title: string;
}

function fetchArticle(onSuccess: (article: Article) => void) {
    // ...
} 
```

有趣的是，使用默认设置的 TypeScript 编译下面的代码没有错误。

```
interface ArticleWithContent extends Article {
    content: string;
}

fetchArticle((r: ArticleWithContent) => {
    console.log(r.content.toLowerCase());
}); 
```

不幸的是，这段代码会导致运行时错误。作为回调传递给`fetchArticle`的函数只知道如何处理`Article`对象的特定子集——那些也具有`content`属性的对象。

然而，`fetchArticle`可以获取所有种类的文章——包括那些只定义了`title`的文章。在这种情况下，`r.content`是未定义的，并且抛出运行时异常。

```
TypeError: undefined is not an object (evaluating 'r.content.toLowerCase') 
```

幸运的是，启用`strictFunctionTypes`会导致**编译时错误**。编译时错误总是比运行时错误好，因为它在用户运行代码之前就出现了。

```
Argument of type '(r: ArticleWithContent) => void' is not assignable to parameter of type '(article: Article) => void'. 
```

## 协方差、逆变和双变量

如果你只是想知道`strictFunctionTypes`是做什么的，你现在可以停止阅读。但是，我鼓励您继续学习，了解一些支票背后的背景。

首先，让我们引入一个类型来表示一般的单参数回调。

```
type Callback<T> = (value: T) => void;

function fetchArticle(onSuccess: Callback<Article>) {
    // ...
}

declare const callback: Callback<ArticleWithContent>;
fetchArticle(callback); 
```

`fetchArticle`不应该接受`callback`的原因是回调太具体。它只对可以输入的一部分东西起作用。

`callback`的类型不应该分配给`onSuccess`参数的类型。

换句话说，`ArticleWithContent`可赋给`Article`的事实并不意味着*不可赋给*，而是意味着
`Callback<ArticleWithContent>`可赋给`Callback<Article>`。如果这样的暗示是真的，`Callback`类型将是**协变的**。

在我们的例子中，情况正好相反——`Callback<Article>`可赋给`Callback<ArticleWithContent>`。这是因为能够处理所有文章的回调也能够处理`ArticleWithContent`。因此，`Callback`就是**逆变**。

如果两个含义同时为真，那么`Callback`就是**双变量**。

现在让我们重温一下`strictFunctionTypes`的定义。

> 禁用函数类型的双变量参数检查。

现在有意义吗？启用检查后，函数类型参数位置会进行对比检查，而不是双变量检查。

另一方面，一些函数类型被排除在严格的函数类型检查之外——例如，方法和构造函数的函数参数仍然被双变量检查。

## 总结

总结一下，`strictFunctionTypes`是一个有用的编译器标志，可以帮助您捕获一类与传递函数参数相关的错误，比如回调。

这个标志背后的概念是 contravariance，它是一个类型(严格地说是类型构造函数)的属性，描述了它相对于它的类型参数的可赋值性。

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的课程！

[高级类型脚本主类](https://www.educative.io/courses/advanced-typescript-masterclass)