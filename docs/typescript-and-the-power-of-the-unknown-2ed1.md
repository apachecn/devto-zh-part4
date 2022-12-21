# 打字稿和未知的力量

> 原文：<https://dev.to/thitemple/typescript-and-the-power-of-the-unknown-2ed1>

TypeScript 是一种发展非常迅速的语言，有时很难跟上它，有太多的功能不断发布，我们可能会错过一些真正重要的宝石。我认为`unknown`型就是其中之一。

## 什么事？

`unknown`类型是一个基本的类型脚本类型，是在语言的 3.0 版本中引入的。

所谓基本，我的意思是你可以用它来定义一个简单的变量或参数，正如你在下面的例子中看到的。

```
let movie: unknown = "";

function printMovie(movie: unknown) {
    console.log(movie);
} 
```

## 为什么会有？

简单的解释是:为了表示在声明时*的所有类型，开发人员不确定(或不知道)该值将如何使用，或者更具体地说，它的成员(如果有的话)将如何访问。*

它是对`any`的补充，但是它以一种更加类型安全的方式工作。这意味着在你真正使用类型`unknown`的值之前，它不会对它进行类型检查，但是一旦你的代码开始对它进行假设，编译器就会开始对它的使用进行类型检查。

为什么让我们看一个例子来更清楚地说明这一点。

让我们从上面取那个`printMovie`函数。这个函数工作得很好，因为，`console.log`期望一个类型为`any`的值作为它的第一个参数，而`unknown`可以被赋给另一个`unknown`值或`any`。

让我们更深入地研究一下这个例子:

```
function formatMovie(movie: string) {
    return `The movie title is ${movie}`;
}

function printMovie(movie: unknown) {
    console.log(formatMovie(movie));
} 
```

上面的代码片段将*而不是*编译💣因为我们现在对`movie`参数做了一个假设，所以我们试图将它传递给另一个函数`formatMovie`，该函数需要一个类型为`string`的参数。当我们试图调用该函数并在需要一个`string`的地方传递一个`unknown`值时，编译器将产生一个错误，准确地指出这一点。

现在您有一个选择，要么将第一个`movie`参数作为一个`string`输入，要么将其他参数转换成一个`string`。

这里还有一个例子:

```
function printMovie(movie: unknown) {
    console.log(`The movie title is ${movie.title} and its rating is ${movie.rating}`);
} 
```

这是另一个会失败的例子💣因为我们假设`movie`是一个具有属性`title`和`rating`的对象。同样，在这一点上，我们能够相应地键入它，至少使用内联类型。

```
function printMovie(movie: { title: string; rating: number; }) 
```

## 又有什么呢？

嗯，我强烈反对在 TS 中滥用`any`类型🙅。`any`的主要思想是表示实际上可以是任何东西的类型。我们*无法*知道那些类型在编译时会是什么，我的经验是人们太早接触`any`而不是花时间去写合适的类型。

如果可以编写类型，即使这些类型有不同的结构或值，我们也应该编写它们。有几种方法可以做到这一点，例如使用[泛型](https://www.typescriptlang.org/docs/handbook/generics.html)或[联合类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)，但关键是它们可以被类型化。我不想在这篇文章中过多地解释如何输入它们。

重点是，`any`是当你*知道*所讨论的类型无法被指定，而*未知*是当你还不知道它是什么的时候。

## 应该什么时候用？

我认为有一些`unknown`类型的用例。我当然会在这些场景中伸手去拿:

1)从 JavaScript 文件迁移时

当从 JavaScript 代码库迁移时，人们可能会逐个文件地进行迁移。假设您打开一个给定的文件，将其重命名为`.ts`,并开始看到一些由于缺少类型而导致的错误。你可以这样做:

```
type Movie = unknown; 
```

您可以为该文件中的类型创建类型别名，然后在需要时应用给定的类型，比如在函数`function printMovie(movie: Movie)`中。

完成后，你可以返回到`Movie`类型，用需要的属性完成它。

这里应用的策略对于其他场景也是一样的。🔁

2)当使用没有类型定义的第三方库时。

您可以开始创建一个定义文件(. d.ts ),并开始用`unknown`类型填充您目前不确定和不使用的东西，键入您正在使用的东西，并可能在以后对其进行拉取请求。😀

3)当使用未实现的 API 时。

如果你正在使用一个 API 来获取一些数据并使用这些数据，那么你很可能已经知道这些数据的类型，所以要确保它的安全🔒已经打好了。如果你不确定它的用途或者你不知道数据应该如何发送，那么这是一个很好的使用`unknown`的方法。

#### 就这样，让我们把现在不知道的东西用上