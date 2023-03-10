# TC39 管道方案比较- RxJS 案例研究

> 原文：<https://dev.to/zakhenry/tc39-pipeline-proposal-comparison-rxjs-case-study-1nk0>

你可能知道也可能不知道，javascript 可能会出现一个令人兴奋的新操作符——`|>`管道操作符。我不想重复它是什么以及它是如何有用的，本·莱什在这方面已经做得很好了，所以先看看他的文章。相反，我将使用 RxJS 作为案例研究来研究这两个竞争提案，因为这是一个我对新运营商特别感兴趣的用例。

*预先警告，本文观点不一！如果你有不同的意见，我很乐意改变我的想法，因为我已经在这个话题上摇摆了几次。*

在我们深入 RxJS 之前，让我们先回顾一下这两种类型及其特点/缺点。

## 极小/无点样式

<sup>[试试吧！【T2](https://babeljs.io/en/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=MYewdgzgLgBAtgQwA4wLwwGZjQPhggJwN3yIDpEkAKLASgG4AoUSWDASwBsoBTY9LCUL88wsh258aYBs3DQYBHgBMArsB5oY0gDQx2tIUSMEyStRt37ZLBezDAlcHmFjoAbiU8BqGAEYmW1gEZWUtBBIAIyEYX0imOVZFHghVbi0AbT8dACYdAGYAXUYYAB88SioI1FEYACoYHNoS8swuXgIqmLwAVmayvHN1HioqVLg9BEMa_G9xvQAGftb7Rx5nVxbRUKp85sSIEE4eMk4QAHMqJVTuBhgAenv_BaA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-1%2Cstage-2&prettier=false&targets=&version=7.5.0&externalPlugins=)</sup>

```
const map = fn => arr => arr.map(fn);
const filter = fn => arr => arr.filter(fn);
const reduce = (fn, i) => arr => arr.reduce(fn, i);
const increment = v => v + 1;
const add = a => b => a + b;

const result = [1,2,3]
 |> map(a => a * 2)
 |> filter(a => a > 5)
 |> reduce((sum, a) => a+sum, 0)
 |> increment
 |> add(3)

console.log(result); // 10 
```

在这个例子中，你可以看到我们必须对函数做一些准备工作，使它们成为[无点风格](https://en.wikipedia.org/wiki/Tacit_programming)，也就是说它们返回一个带一个参数的函数。一旦准备工作完成，管道就变成了一个简单的操作符组合。在这一点上值得注意的是，这并不是一个很好的例子，因为无论如何，你将来可能仍然会使用`Array.prototype.*`方法。我只是举了一个应该感觉很熟悉的例子。

## 话题风格(又名智能组合)

<sup>[试试吧！](https://babeljs.io/en/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=MYewdgzgLgBAlmYAnApgWxWWBeGBDGbAPnxgGoYBGAbgChRJY8ATZwmACjwBoYAjAJSESBCnzr1w0GKggBXADY4YAbUrcATNwDMAXVowAPiQDEAOjR4ADl2GkAVDA0wBB4zHMAzOEpRJbxKQkAKyuRqZmqMxywCgcHPJovHhCgXhkibwADGHuCMjomFBuIqwcJrzarpKQIAooZgogAOYcsopQAtQwAPQ9VFlAA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-1%2Cstage-2&prettier=false&targets=&version=7.5.0&externalPlugins=) -(在左边的管道建议中选择“智能”)</sup>

```
const increment = a => a + 1;
const add = (a, b) => a + b;

const result = [1,2,3]
 |> #.map(a => a * 2 )
 |> #.filter(a => a > 5)
 |> #.reduce((sum, a) => a+sum, 0)
 |> increment
 |> add(#, 3)

console.log(result); // 10 
```

主题风格采取了一种不同的方法，本质上是将函数用作实际调用的“模板”。因为参数事先是未知的，所以我们使用`#`作为占位符，一旦已知就使用该值。

这种策略的优点是管道中使用的函数声明简单，代价是管道本身稍微冗长一些。

此外，在我个人看来，这感觉像是额外的认知负荷，当我做类似`add(#, 3)`的事情时，我需要明白，当到达这一行代码时，我并没有真正调用 add 函数，而是为它的执行定义了一个模板。

在这个虚拟的例子中，这两个提议并没有太大的区别，但是我不认为这是非常现实的，所以让我们面对现实吧！

使用 RxJS，我们将定义一个管道，它从文本框中获取用户输入，当字符数少于两个时忽略它，去抖他们的击键，然后查询 api 以获得搜索结果。

## RxJS 预流水线

```
const searchResults$ = fromEvent(document.querySelector('input'), 'input').pipe(
  map(event => event.target.value),
  filter(searchText => searchText.length > 2),
  debounce(300),
  distinctUntilChanged(),
  switchMap(searchText => queryApi(searchText).pipe(retry(3))),
  share(),
) 
```

这只是实现了上面给出的描述。RxJS 使用`.pipe()`方法来代替更好的方法——Javascript 管道操作符:

## RxJS 最小/无点样式

```
 const searchResults$ = fromEvent(document.querySelector('input'), 'input')
  |> map(event => event.target.value)
  |> filter(searchText => searchText.length > 2)
  |> debounce(300)
  |> distinctUntilChanged()
  |> switchMap(searchText => queryApi(searchText) |> retry(3))
  |> share() 
```

这与之前的语法几乎完全相同，但是由于新的`|>`操作符，阅读起来更加整洁。`switchMap`中内嵌操作符有多好？

为了便于比较，让我们看看上面的主题风格的操作符实现是什么样子的:

## RxJS 话题风格

```
 const searchResults$ = fromEvent(document.querySelector('input'), 'input')
  |> map(#, event => event.target.value)
  |> filter(#, searchText => searchText.length > 2)
  |> debounce(#, 300)
  |> distinctUntilChanged
  |> switchMap(#, searchText => queryApi(searchText) |> retry(#, 3))
  |> share 
```

嗯。我不是粉丝。也许这只是一个不熟悉的话题，但是要求所有的操作者都必须把可观察对象作为一个话题来传递，这感觉真的很不干净。当然，这需要重写所有的 RxJS 操作符来使用这个新语法，并把一个可观察值作为第一个参数。

你怎么想呢?关于主题样式操作符，我是否遗漏了一些真正引人注目的东西？我知道很多讨论都是围绕如何处理`await`关键字，但老实说，我很少使用`async/await`，因为 RxJS 是我最常用的，但那是*，只是我个人的经验*。

另一方面，[TC39 提案](https://github.com/tc39/proposal-pipeline-operator/issues)感觉有点停滞不前，因为 Github 问题几乎都是去年的评论。让我们重新开始讨论，看看我们是否能让它更接近被使用！

<sup>照片由 [Rodion Kutsaev](https://unsplash.com/@frostroomhead?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge) 在 Unsplash</sup> 上拍摄