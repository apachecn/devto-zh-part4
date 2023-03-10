# 通过功能化提升你的 Lodash 游戏:从链接到管道

> 原文：<https://dev.to/aurelio/up-your-lodash-game-by-going-functional-from-chaining-to-piping-3li8>

当我们在 JavaScript 中寻找用于数据操作的实用程序库时，Lodash 几乎是事实上的标准。以至于它是 npm 上[最依赖的包](https://gist.github.com/anvaka/8e8fa57c7ee1350e3491)。

Lodash 允许开发人员编写表达性代码，涵盖处理数据时最常见的需求。例如，它使得读写操作看起来非常自然和简单:

```
_.intersection(['a', 'b', 'c'], ['a', 'c', 'e']); // ['a', 'c'] 
```

很容易爱上这样一个工具的表现力和便捷性。今天，我们将看到如何最大限度地利用这个强大的库，最大限度地利用 Lodash 方法，同时最大限度地减少我们发送给用户的代码量，这要归功于我们的 bundler(这里是 Webpack)的功能。

我们一会儿就知道怎么做了。在继续之前，先简单说明一下。

## 免责声明:哪个 Lodash？

正如我们将看到的，Lodash 有不同的分布。在本帖中，我们将从使用作为 es 模块导出的 [Lodash-es](https://github.com/lodash/lodash/tree/es) 开始。这允许我们编写类似于
的东西

```
import { take, orderBy } from 'lodash-es'; 
```

而不是略显冗长的

```
import take from 'lodash/take';
import orderBy from 'lodash/orderby'; 
```

让我们的 bundler(这里是 Webpack)只包含`take`需要的东西，而不是盲目地捆绑整个 Lodash。

这两种情况下的目标都是避免导入整个库，这在编写类似于
的代码时会发生

```
// try to avoid doing this
import _ from 'lodash';

_.take([1,2,3], 2); // [1, 2] 
```

在本帖中，我们将看到如何使用不同口味的 Lodash 来实现我们想要的，以及这对我们的捆绑包意味着什么。

最后，[在这里](https://github.com/nobitagit/functional-lodash-example)如果你有兴趣自己尝试一下，你可以找到一个包含以下代码的 repo。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[nobitagit](https://github.com/nobitagit)/[functional-lodash-example](https://github.com/nobitagit/functional-lodash-example)

### “通过功能化提升您的 Lodash 游戏:从链接到管道”示例报告

<article class="markdown-body entry-content container-lg" itemprop="text">

# Lodash 的实用化:从链接到管道

</article>

[View on GitHub](https://github.com/nobitagit/functional-lodash-example)

我将在每个部分顶部的注释中引用每个阶段的提交。例如，这将检查第一次提交。

```
// -> git checkout 56e092ab1bd01b2492286d5e0f6922b0405b482b 
```

放弃免责声明后，是时候看看一些代码了！

## 简介:我们今天的挑战

为了展示 Lodash 的力量，我们将为自己设定一个现实的目标。给定一份球员名单，我们希望找到进球数排名前三的球员的名字。如果两名球员的进球数相同，那么射门次数少的一方将获胜。

以下是英超前锋的名单，以及他们的数据。

```
const players = [
  { player: 'Aleksandar Mitrovic', goals: 10, shots: 118 },
  { player: 'Mohamed Salah', goals: 19, shots: 116 },
  { player: 'Harry Kane', goals: 17, shots: 110},
  { player: 'Sergio Agüero', goals: 19, shots: 99},
  { player: 'Raùl Jiménez', goals: 12, shots: 96},
  { player: 'Paul Pogba', goals: 13, shots: 93 },
  { player: 'Eden Hazard', goals: 16, shots: 86},
  { player: 'Pierre-Emerick Aubameyang', goals: 18, shots: 81 },
  { player: 'Gylfi Sigurdsson', goals: 12, shots: 78},
  { player: 'Sadio Manè', goals: 18, shots: 77},
]; 
```

我们将在整篇文章中使用这些数据，并从现在开始将其称为`players`。

## 第一步:让我们通过使用中间变量来保持简单

为了开始，我们将从最简单的方法开始。我们将使用一些 Lodash 方法来解决这个问题，并将使用变量来存储中间值。

```
// git checkout aa1935007c2fa7e3663df7401dfca9f62715f3e7
import { take, orderBy, map } from 'lodash-es';

// Sort players by goals scored and shots taken.
// If 2 players have the same number of goals, the one player
// with less shots on targets is ranked higher.
const sorted = orderBy(players, ['goals', 'shots'], ['desc', 'asc']);
const top3 = take(sorted, 3);
const result = map(top3, 'player');
console.log(result); // ["Sergio Agüero", "Mohamed Salah", "Sadio Manè"] 
```

很好，这就是我们想要的结果。问题解决了。

现在让我们快速分析一下这个包。

```
$ npm run build 
```

我们也可以从构建目录中运行产品代码:

```
$ python3 -m http.server 3333 
```

最后在`localhost:3333`打开我们的浏览器，打开控制台查看结果。
现在我们已经构建了我们的代码，我们可以检查*它是如何构建的以及它的大小。* 

```
$ du -h build/js/*.js
 20K    build/js/1.chunk.js
4.0K    build/js/app.js 
```

我们可以看到 Webpack 已经自动将我们的代码分成两个文件，一个包含我们的代码(`app.js`)，另一个包含 lib (1.chunk.js)。
作为旁注`app.js`实际上比`du` 报道的 4k [要小一点，但是我们可以保留那个 4k 作为参考值。](https://unix.stackexchange.com/questions/34331/why-does-the-du-command-count-in-4kb-steps/34338#34338)

## 第二步:从变量到链接，或者说如何用三个简单的步骤打破生产

好了，现在我们的首要任务是去掉那些多余的中间变量(上例中的`const sorted`和`const top3`)。
连锁可能是我们想到的第一个想法，也是一个好主意，所以我们可以做这样的事情。

```
const result = orderBy(players, ['goals', 'shots'], ['desc', 'asc'])
   .take(3)
   .map('player'); 
```

虽然链接实际上是由 Lodash 提供的，但如果你尝试这样做，你会发现它不起作用。
Lodash 有多种实现方式，其中一种是结合使用`chain`和“值”方法。这里有一个例子。

```
// git checkout e8637158f5ecd8475b438b375a027db9a006d59c
import { chain } from "lodash-es";

const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value(); 
```

让我们运行开发中的应用程序(`npm run start`)，我们将看到一切都如预期的那样工作。

<figure>

[![So far so good](img/19f744d8f67eebf9b1f35f9edb87921d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--20QXfGpc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nobitagit.github.io/blimg/2019-7-2-lodash-2.png)

<figcaption>So far so good, everything seems to work</figcaption>

</figure>

让我们看看我们的 prod 包在这种情况下会是什么样子。

```
npm run build 
```

和 cd 放入我们的发行目录。

```
$ cd build/js
ls -l
-rw-r--r--  1 user1  staff   2788 Apr 21 21:41 app.js
-rw-r--r--  1 user1  staff  22082 Apr 21 21:41 app.js.map 
```

奇怪的是，Webpack 只生产了一个包，并且没有像以前一样将应用程序和程序块分开。另外，这个包裹看起来小得可疑。

```
$ du -h app.js
4.0K    app.js 
```

我们可以尝试在 prod 中运行该应用程序，看看会发生什么。

```
cd ..
python3 -m http.server 3333 
```

我们打开 [http://0.0.0.0:3333/](http://0.0.0.0:3333/) ，我们可以看到我们设法破坏了我们的应用程序。

```
TypeError: (intermediate value)(...).orderBy is not a function 
```

<figure>

[![A broken app](img/2407decaec7e99151defe138a8396bbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Z0OcUgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nobitagit.github.io/blimg/2019-7-2-lodash-1.png)

<figcaption>A nice, broken app</figcaption>

</figure>

这里的原因是 Webpack 不知道 Lodash 在这种情况下需要其他方法，而不是我们显式导入的方法(即`chain`)。然后，bundler 高兴地对所有看起来没用的方法进行了彻底的修改，关键是没有给我们留下任何方法。地图，没有 _。orderBy，no _。接受产品包。这将导致生产中出现运行时错误。不太好。
要解决这个问题，我们可以导入整个 lodash，然后只析构我们以后需要的东西。
我们就这么做，看看结果。

## 第三步:导入所有东西，修复断链

如前所述，让我们通过导入默认导出并将其分配给`_` char 来导入整个 Lodash。然后，我们通过析构提取 chain，因此代码的其余部分保持不变。

```
// git checkout 2edb1b825f8f8c475755bca0852a48092c426997
import _ from "lodash-es";

const { chain } = _;

const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value(); 
```

快速检查一下我们的 dev env build 将会发现一切都还在工作，所以我们可以重新构建产品包并在浏览器中进行测试。一切正常。

<figure>

[![Up and running again](img/545077a20626e169384bf9cc9664a3a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aQnXjTA5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nobitagit.github.io/blimg/2019-7-2-lodash-3.png)

<figcaption>Up and running again</figcaption>

</figure>

让我们再检查一下包裹。

```
$ du -sh build/js/*.js
 88K    build/js/1.chunk.js
4.0K    build/js/app.js 
```

我们现在回到了两个文件，我们的“main”(app . js)和我们的 chunk。不幸的是，这最后一个文件比以前大得多。

这可以归结为一个事实，我们告诉 Webpack 包含完整的库，我们的 bundler 就是这么做的。事实证明，连锁和摇树在 Lodash-es [无法同时实现](https://github.com/lodash/lodash/issues/3298)，除非你愿意[做一些不那么漂亮的体操](https://github.com/lodash/lodash/issues/3298#issuecomment-341685354)。

现在的问题是，有没有一种方法可以让我们拥有链接(或者类似的模式)的表现力，而不会招致代价或者维护一个笨拙的定制版本？这正是我们下一步要努力实现的目标。

## 第四步:从链接到管道

我们要做的第一件事是从一种模式[链](https://medium.com/backticks-tildes/understanding-method-chaining-in-javascript-647a9004bd4f)转移到一种相似但根本不同的模式，即[管道](https://vanslaars.io/post/create-pipe-function/)。

你可能已经看过管道的使用。无论如何，`pipe`背后的想法很简单。
Pipe 将接受两个参数:一个函数序列和一个值作为开始输入。
然后`pipe`中的每个函数将接收前一个函数的输出作为输入。

这正是我们所需要的，本质上来说，不是链接时与我们所拥有的相差甚远的*。
事实证明，Lodash 提供了一个相当于管道的`flow`函数。让我们看看它在实践中的作用。* 

```
// git checkout 146c84a17f2c44c81317794740e8d8c46aae0938
import { flow, orderBy, take, map, partial } from "lodash-es";

const result = flow(
  _players => orderBy(_players, ["goals", "shots"], ["desc", "asc"]),
  _players => take(_players, 3),
  _players => map(_players, "player")
)(players); 
```

这太棒了。我们现在已经消除了对中间常数的需求，并将数据转换转化为管道。`flow`函数负责用`players`的值开始一切，然后将每一步(每一行)的结果传递给下一个转换。

快速检查一下我们的包，我们可以看到我们已经再次缩减了包的大小。

```
$ du -sh build/js/*.js
 32K    build/js/1.chunk.js
4.0K    build/js/app.js 
```

我发现代码本身是可读的，但是这里仍然有相当多的冗余。那些在每一行重复两次的参数会产生噪音，如果我们能消除它们就好了。

就目前情况来看，我们有两个解决方案。我们可以厚着脸皮把这个变量重新命名为一个非常短的名字，因为现在这个值代表什么已经很清楚了:

```
const result = flow(
  v => orderBy(v, ["goals", "shots"], ["desc", "asc"]),
  v => take(v, 3),
  v => map(v, "player")
)(players); 
```

但是，如果我们可以完全删除箭头功能，不是更好吗？本质上，我的目标是这个。

```
const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players); 
```

哪一个是我们能得到的最接近原始链接版本的版本:

```
const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value(); 
```

不幸的是，这不太管用。为了做到这一点，我们需要以某种方式将我们正在使用的 Lodash 方法转换成支持部分应用的函数。我们可以尝试这样做，实际上 Lodash 再次帮助了我们，它提供了一个方便的方法，将每个函数转换成一个只有在传递最后一个参数时才执行的函数。就是我们要找的东西。

## 第五步:部分存在

```
// git checkout 00fd8b573be5c075e3d3cd841bf5fed6d977c28f
import { flow, orderBy, take, map, partial } from "lodash-es";

const __ = partial.placeholder;

const result = flow(
  partial(orderBy, __, ["goals", "shots"], ["desc", "asc"]),
  partial(take, __, 3),
  partial(map, __, "player")
)(players); 
```

这里需要做相当多的解释。首先，我们将想要转换的函数传递给一个支持部分应用参数的函数。

```
partial(orderBy, ...), 
```

然后我们按顺序列出所有要传递给这个函数的参数。关键的是，我们需要传递给它的第一个参数是我们的参数。我们现在可以指示 Lodash，我们将在稍后阶段通过使用占位符来传递该值。Lodash 提供了这种功能，这样我们就可以标记出当参数可用时将被传递到的位置。

```
const __ = partial.placeholder;
// ...
partial(orderBy, __, ... ) 
```

然后我们可以列出所有剩下的参数，因为我们已经知道它们:

```
 partial(orderBy, __, ["goals", "shots"], ["desc", "asc"]) 
```

这里是它的完整版本:

```
const __ = partial.placeholder;

const result = flow(
  partial(orderBy, __, ["goals", "shots"], ["desc", "asc"]),
  partial(take, __, 3),
  partial(map, __, "player")
)(players); 
```

**注意**:这可能是对`_.partial`的过度简化，实际上是为了解释我们手头的特殊问题。值得注意的是，在这种情况下，我们可以通过使用`_.partialRight`来实现一个改进的版本，但我决定在这篇文章中跳过它。如果你感兴趣，我在 [StackOverflow](https://stackoverflow.com/a/42368281/1446845) 上贴出了这个答案中的一个例子。

我们的包裹看起来还不错

```
$ du -sh build/js/*.js
 32K    build/js/1.chunk.js
4.0K    build/js/app.js 
```

但是实现本身看起来并没有比我们使用箭头函数时有太多的改进。我们当然可以做得更好。理想情况下，我们希望 Lodash 负责部分应用，而不需要我们在**如何**中如此明确地做到这一点，也不需要为每个方法都这样做。
为此，我们需要一个不同版本的 Lodash，Lodash/fp。

## 第六步:满足 Lodash/fp

Lodash 提供了一个支持每种方法开箱即用的部分应用程序的版本。Lodash/fp 还有其他特性，比如重新排列参数，以便将数据作为每个方法的最后一个参数传递，而不是第一个参数，这将使我们能够到达我们想要的地方。

让我们首先安装作为 Node.js 模块导出的 Lodash 的“常规”版本。这实际上包含了函数库的功能版本，这是在 Lodash-es
中缺少的

```
npm i --save lodash 
```

然后我们修改导入来反映:

```
// import { flow, orderBy, take, map, partial } from "lodash-es";
import { flow, orderBy, take, map, partial } from "lodash/fp"; 
```

最后，我们可以更改我们的转换，以利用开箱即用的部分应用:

```
// git checkout 9ecd0acd4b40d20ce1de7bfea83b62a60b6868f6
import { flow, orderBy, take, map, partial } from "lodash/fp";

const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players);
// > > Array(3) [ "Sergio Agüero", "Mohamed Salah", "Sadio Manè" ] 
```

我们运行我们的代码，再一次，我们得到了我们想要的结果。然后，我们检查包裹的大小....

```
$ du -sh build/js/*.js
 84K    build/js/1.chunk.js
4.0K    build/js/app.js 
```

很明显，它已经包含了整个库代码！
原因是我们导入 Lodash 方法的方式。不幸的是，由于我们不再使用【Webpack 无法对命名导入进行树抖动。

## 第七步:切换导入

解决方案是将它们更改为默认导入。

```
// git checkout ac97938fd864f738481149459b39976ff22f17bf
import flow from "lodash/fp/flow";
import orderBy from "lodash/fp/orderBy";
import take from "lodash/fp/take";
import map from "lodash/fp/map";

const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players);
// > > Array(3) [ "Sergio Agüero", "Mohamed Salah", "Sadio Manè" ] 
```

```
$ du -sh build/js/*.js
 52K    build/js/1.chunk.js
4.0K    build/js/app.js 
```

如您所见，我们再次削减了我们的产品包。虽然在 32K 时它不像以前那么小，但我们实际上只导入我们需要的东西。

## 结论:移至 lodash/fp。值得吗？

那么，您是否应该使用管道而不是链接，并将您的导入转换为使用`lodash/fp`？如同编程中的一切(或者生活中的一切！)答案只有一个:看情况。
让我们来比较一下我们最初的连锁版本:

```
const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value(); 
```

到最后一个使用 Lodash/fp:

```
const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players); 
```

如您所见，语法上的差异很小。

正如我们已经看到的，我们可以通过不导入整个 Lodash 来缩减我们的包大小，但是如果我们使用 Lodash 的`fp`版本，我们可能会有一个稍微大一点的包大小(虽然比完整的包导入要小)，并且我们将会失去使用命名导入(import { flow，orderBy，take，map，partial } from "lodash-es ")的非常方便的特性，同时仍然支持树抖动。

不过，功能性版本有一个很大的优势。除了更主观的风格偏好之外，采用管道而不是链接将允许我们用自己的函数*散布 lodash 方法的用法*。
例如，我们可以提取管道的前两个步骤，并将它们分配给一个常量:

```
const top3 = p =>
  flow(
    orderBy(["goals", "shots"], ["desc", "asc"]),
    take(3)
  )(p);

const top3names = flow(
  top3,
  map("player")
)(players); // logs 3 players' names

const top3totalGoals = flow(
  top3,
  sumBy('goals)
)(players); // 56 
```

这样我们可以找到一个有意义的名字，并在其他地方重用`top3`函数。在这种情况下`top3`只包含 Lodash 方法，但是当然我们并不局限于它们。只要新函数接收数据并返回数据，我们就可以开始工作了。

我个人的观点是，转移到 pipes + lodash/fp 在大多数时候是值得的，如果你是一个 lodash 的重度用户，绝对值得一试。

## 有用的链接

*   lodash FP 指南
*   你应该在 JavaScript 中使用的 3 个 Lodash 函数
*   [为什么链接是一个错误](https://medium.com/making-internets/why-using-chain-is-a-mistake-9bc1f80d51ba#.dcv5qk552)
*   [Stack Overflow 的《如何使用 Lodash 链接函数》](https://stackoverflow.com/questions/35590543/how-do-you-chain-functions-using-lodash/42368281#42368281)
*   [尼克·库珀(封面图片作者)在 Unsplash 上的个人资料](https://unsplash.com/@cooper301)

*这篇文章最初发表在[我的博客](https://nobitagit.github.io/blog/Functional-Lodash-From-Chaining-to-Piping/)上。*