# 使用 fp-ts 的安全 Node.js 文件操作

> 原文：<https://dev.to/wojciechmatuszewski/safe-node-js-file-operations-with-fp-ts-48ki>

当我试图让自己熟悉函数式编程时，我想，我不妨测试一下我目前的技能，写一些代码。

经过反复思考，我决定为 Node.js 原生的`fs.readFile`和`fs.writeFile`方法编写功能性的、安全的、与文件相关的(读、写)包装器。

> 本文假设您了解 TypeScript 的基本知识和函数式编程的核心概念，如组合和部分应用程序，以及函子和单子的概念。

# 先做第一件事

> 在本文中，我将跳过函数式编程中的一些数学内容。虽然这里省略了，但它**不应该是** *，一般情况下，忽略不计。

开始之前，我们必须熟悉`IO`、`Task`和`Either`功能结构

## 要么

两者都是具有两个子类型的结构:

*   左边的
*   正确

这两种亚型带有失败(`left`)和成功(`right`)的概念。

它主要用于使计算和转换安全。
假设我想实现`safeParseInt`。`Either`是这样做的理想人选。

看看这个:

```
import { Either, left, map, right } from 'fp-ts/lib/Either';
import { increment } from 'fp-ts/lib/function';
import { compose } from 'ramda';

function safeParse(radix: number) {
  return function(strToParse: string): Either<string, number> {
    const n = parseInt(strToParse, radix);
    return isNaN(n) ? left('Parse failed') : right(n);
  };
}

const safeBaseTenParser = safeParse(10);

// You could also use fp-ts flow method
// flow works just like pipe, so you would have to switch the order of computations.
const res = compose(
  map(increment),
  safeBaseTenParser
)('123');

console.log(res);

// { _tag: 'Right', right: 124 }
// To get the actual value you probably should use the fold method. 
```

Enter fullscreen mode Exit fullscreen mode

由于 *`Either`是右偏的*，我们所有的变换(在这种情况下是`increment`)将只应用于实际的、正确的、*右*值。

一旦我们引入*左*值，所有继续该值的转换都将被忽略:

```
// ... previous code ... //

const res = compose(
  map(val => {
    console.log('Im here');
    return val;
  }),
  safeBaseTenParser
)('js-is-awesome');

console.log(res) // { _tag: 'Left', left: 'Parse failed' } 
```

Enter fullscreen mode Exit fullscreen mode

`map`中的`console.log`从不开火。该转换被忽略，因为我们从`safeBaseTenParser`接收到*左值*。多棒啊。

为了实现前面提到的文件操作，我们不打算直接使用`Either`，但是会出现*左*和*右*值的概念。

## IO_( *要么*)

IO 是一个用于同步计算的*计算生成器*。这就是计算，它会在我们的程序中产生副作用。

通过使用`IOEither`，我们正在传达这些计算可能会失败，因此我们必须处理*右*和*左*值。

我们将使用`IOEither`来解析/字符串化值。

```
import { toError } from 'fp-ts/lib/Either'
import { IOEither, tryCatch as IOTryCatch } from 'fp-ts/lib/IOEither';

const stringifyData = (data: Todo[]) =>
  IOTryCatch(() => JSON.stringify(data), toError);

const parseStringifiedData = (data: string): IOEither<Error, Todo[]> =>
  IOTryCatch(() => JSON.parse(data), toError); 
```

Enter fullscreen mode Exit fullscreen mode

`IOTryCatch`像`try{}catch{}`块一样工作，但是返回`IOEither`,所以我们可以组合这些操作。

我们还使用`toError`将`JSON.parse/stringify`误差转发到*左值*。

## 任务 _(*)*

 *任务是`IO`的异步版本。
因为我们想获得非阻塞异步操作的好处，所以我们需要这个结构来包装`fs.readFile`和`fs.writeFile`方法。

```
import { promisify } from 'util';
import fs from 'fs';
import { tryCatch as TaskEitherTryCatch } from 'fp-ts/lib/TaskEither';
import { toError } from 'fp-ts/lib/Either';

const readFromFile = promisify(fs.readFile);
const writeToFile = promisify(fs.writeFile);

export const getFileContents = (path: string) =>
  TaskEitherTryCatch(() => readFromFile(path, 'utf-8'), toError);

export const writeContentsToFile = (path: string) => (contents: string) =>
  TaskEitherTryCatch(() => writeToFile(path, contents), toError); 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们在这里使用了`tryCatch`变量，这使我们不必担心实现我们自己的`try{}catch{}`块。

我还将`writeContentsToFile`创建为*的高阶函数*，使其更加可重用，并能很好地配合合成。

## 实现

这些是主要的组成部分。现在让我们把所有的碎片拼在一起:

```
 import { flow } from 'fp-ts/lib/function';
import {
  chain as TaskEitherChain,
  fromIOEither,
  map as TaskEitherMap
} from 'fp-ts/lib/TaskEither';

const FILE_NAME = 'data.json';
const FILE_PATH = path.join(__dirname, `./${FILE_NAME}`);

export const getFileData = flow(
  getFileContents,
  TaskEitherChain((rawString: string) =>
    fromIOEither(parseStringifiedData(rawString))
  )
);

export const saveData = (path: string) => (data: Todo) =>
  flow(
    getFileData,
    TaskEitherMap(append(data)),
    TaskEitherChain(todos => fromIOEither(stringifyData(todos))),
    TaskEitherChain(writeContentsToFile(FILE_PATH))
  )(path); 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意一些事情:

*   有时候我们不得不用`fromIOEither`。这是因为`IOEither`是纯同步的，而`TaskEither`不是。`fromIOEither`允许我们将同步`IOEither`转换成匹配的`TaskEither`结构。

*   如果你不熟悉`chain`方法，它允许我们转义嵌套结构，并且仍然*映射*一个，在这个例子中，`TaskEither`到另一个。

*   `saveData`方法有这个*咖喱样的*签名，允许创建独立的*保存管理器*，它有`path`前缀。

*   我这里用的是`flow`的方法。它的工作原理就像`pipe`(从左到右)。

## 用法

保存数据非常简单。我们必须提供`path`和一个`Todo`。

```
saveData(FILE_PATH)({
  id: uuid(),
  isDone: false,
  content: 'content'
// getting the actual results using fold
})().then(either => fold(console.log, console.log)(either)); 
```

Enter fullscreen mode Exit fullscreen mode

获取数据与保存数据非常相似。

```
getFileData(FILE_PATH)().then(either => fold(console.log, console.log)(either)); 
```

Enter fullscreen mode Exit fullscreen mode

`saveData`和`getFileData`代表由于副作用而可能不安全的计算。通过援引它们，我们正在拔掉手榴弹的引线，希望出现最好的结果。

但是，如果发生了任何损坏，我们非常确定要去哪里寻找罪犯，因为我们在这些小的可组合函数中包含了杂质。

## 总结

所以你有它。

函数式编程的世界非常广阔，虽然我在这方面只是个初学者，但我已经能够在我的代码库中引入一点函数式魔法了。

我希望有些人会觉得这篇文章有用。

可以在 twitter 关注我: [@wm_matuszewski](https://twitter.com/wm_matuszewski)

谢谢👋

## 附加资源

*   有一个[很棒的系列](https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3)涵盖了 fp-ts，比我所能做到的要详细得多。读一下吧！

*   Kyle Simpson 在 FrontendMasters 上有一个[大系列](https://frontendmasters.com/courses/functional-javascript-v3/)

*   [这个 git 回购](https://github.com/MostlyAdequate/mostly-adequate-guide)

## 脚注

*有人可能会说，知道函数式编程与数学的关系是没有用的。我也有同样的观点，但是在学习了足够多的管理这些结构的定理和数学规则后，我发现学习新概念要容易得多，因为它们都通过数学联系在一起。*