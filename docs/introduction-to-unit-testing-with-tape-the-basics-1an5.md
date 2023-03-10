# 用磁带进行单元测试的介绍，基础知识

> 原文：<https://dev.to/avalander/introduction-to-unit-testing-with-tape-the-basics-1an5>

如果你从来没有听说过单元测试，或者只是听说过，不知道从哪里开始，这篇文章就是为你准备的。在这里，我将介绍单元测试的基本概念，并且我将用[磁带](https://github.com/substack/tape)展示一个实际的例子来让你开始。

# 什么是单元测试？

单元测试可以粗略地概括为编写测试代码单元的代码。一个*代码单元*基本上是一个独立的组件，通常是一个函数。单元测试的目的是通过使用精心制作的输入执行代码单元并验证输出来验证代码单元是否按预期执行。

出于多种原因，单元测试是一种可取的实践。首先，组件的行为可以被快速自动地验证，这在更改组件以适应新功能之后尤其有用。此外，组件的行为在测试中有文档记录，因此任何使用该组件的开发人员都可以将它们作为参考。

值得一提的是，有了[纯函数](https://en.wikipedia.org/wiki/Pure_function)，单元测试就容易多了。因此，尽可能保持代码库中的大部分组件纯净是一个好的实践。

# 我们开始吧

首先，您需要安装 node。你可以从[官方网站](https://nodejs.org/en/download/)下载或者使用 [nvm](https://github.com/nvm-sh/nvm/blob/master/README.md) 管理 node 的多个版本。

其次，我们要用我的一个玩具项目，[小步舞](https://github.com/Avalander/Minuette.git)。这是一个非常简单的 shell 待办应用程序。继续，克隆项目并尝试看看它是如何工作的。

```
git clone https://github.com/Avalander/Minuette.git
cd Minuette
npm install npm start 
```

您可以使用`node start add 'text'`、`node start complete 0`和`node start list`运行不同的命令。

最后，我们需要一个试跑者。我们将使用[磁带](https://github.com/substack/tape),因为它简单明了。我们还会用一个漂亮的记者。我喜欢轻点，但你可以查看[这个列表](https://github.com/substack/tape#things-that-go-well-with-tape)并尝试不同的一个。

```
# Run in the project's root folder
npm install -D tape tap-dot 
```

# 我们的第一个单元测试

现在我们准备好出发了。我们将测试的第一个函数是`sortItems`，它位于`src/sort.js`中。

让我们创建一个名为`test`的文件夹，并在其中放置一个名为`sort.test.js`的文件。然后，我们将在该文件中编写我们的测试。

我们将从导入`tape`模块开始。

```
const test = require('tape') 
```

`tape`模块返回一个接收两个参数的函数:一个描述测试用例的字符串，一个执行文本用例的函数。

```
test('This is my first test #yolo', t => {
  t.plan(3)
  t.equal(3, 3)
  t.deepEqual([ 1, 2, 3 ], [ 1, 2, 3 ])
  t.pass('We good')
  t.end()
}) 
```

传递给测试函数`t`的参数是一个带有几个断言的对象，我们可以用它来执行我们的测试。这些是我们可以使用的一些断言，查看[文档](https://github.com/substack/tape#methods)获得完整列表。

*   接收一个整数，如果执行的断言多于或少于设置的数目，测试将失败。
*   `equal`检查两个值是否相等。对于您需要的数组和对象，它不能很好地工作
*   `deepEqual`类似于`equal`,但是它作用于数组和对象。
*   `pass`总会过去。
*   `end`表示测试结束。

注意测试函数必须使用`plan`或`end`是很重要的。

# 我们写那个测试怎么样？

当然，我们来测试一下功能`sortItems`。`sortItems`接收结构为`{ status, text, timestamp }`的对象数组，并根据以下标准对其进行排序:

1.  带有`status` `'done'`的项目被发送到数组的末尾。
2.  具有相同`status`的项目按照`timestamp`升序排序。

因此，我们可以编写一个测试用例来检查第一个标准。

```
const test = require('tape')

const { sortItems } = require('../src/sort')

test('sortItems should place items with status done at the back', t => {
  const result = sortItems([
    { status: 'done' },
    { status: 'todo' },
  ])

  t.deepEqual(result, [
    { status: 'todo' },
    { status: 'done' },
  ])
  t.end()
}) 
```

我们走吧。这个测试将使用包含两个项目的数组调用`sortItems`，并使用`t.deepEqual`检查它们是否按照第一个标准排序。然后我们给`t.end`打电话，表示我们完成了。

要运行测试，只需在控制台中键入以下命令并检查输出。

```
npx tape test/**/*.test.js | npx tap-dot 
```

为了简化进一步的执行，您可以将文件`package.json`中的`"test"`脚本更新为`"tape test/**/*.test.js | tap-dot"`，然后只需输入`npm test`即可运行您的测试。

让我们写一个测试来检查第二个排序标准。给定两个状态相同的项目，它们应该按照时间戳升序排序。

```
test('sortItems should order items with the same status according to timestamp', t => {
  const result = sortItems([
    { status: 'todo', timestamp: 800 },
    { status: 'todo', timestamp: 500 },
  ])

  t.deepEqual(result, [
    { status: 'todo', timestamp: 500 },
    { status: 'todo', timestamp: 800 },
  ])
  t.end()
}) 
```

# 更多测试

我们可以对`sortItems`的测试感到满意，但是我们只对包含两个项目的两个数组进行了测试。这些很难涵盖该函数必须操作的所有可想到的输入。让我们试试别的东西。

首先，我们将创建一个包含几个条目的数组，比如十个。

```
const items = [
  { status: 'todo', text: 'Rainbow Dash thinks Fluttershy is a tree.', timestamp: 1000 },
  { status: 'todo', text: 'I simply cannot let such a crime against fabulousity go uncorrected.', timestamp: 1100 },
  { status: 'todo', text: `Huh? I'm pancake...I mean awake!`, timestamp: 1200 },
  { status: 'todo', text: `Don't you use your fancy mathematics to muddy the issue!`, timestamp: 1300 },
  { status: 'todo', text: `Reading's for eggheads like you, Twilight. Heh, no offense, but I am *not* reading. It's undeniably, unquestionably, uncool.`, timestamp: 1400 },
  { status: 'done', text: 'Too old for free candy? Never!', timestamp: 1000 },
  { status: 'done', text: 'Trixie is the highest level unicorn!', timestamp: 1100 },
  { status: 'done', text: `I'd like to be a tree.`, timestamp: 1200 },
  { status: 'done', text: 'Ha! Once again, the Great and Powerful Trixie has proven herself to be the most amazing unicorn in all of Equestria. Was there ever any doubt?', timestamp: 1300 },
  { status: 'done', text: 'What the hay is that supposed to mean?', timestamp: 1400 },
] 
```

请注意，数组是根据我们定义的标准排序的。接下来我们可以随机洗牌几次，并检查`sortItems`的输出是否总是等于排序后的数组。

遗憾的是，node 没有任何`shuffle`函数，所以我们必须实现自己的函数。

```
const shuffle = ([ ...items ]) => items.sort(() => Math.random() - 0.5) 
```

注意我们如何在`([ ...items ])`中使用析构和扩展操作符。这将生成数组的浅层副本。我们需要这样做，因为`Array.sort`在适当的位置对数组进行排序，所以如果我们不进行复制，它将打乱我们的引用数组，并且对照`sortItems`的输出进行测试是没有用的。

然后我们使用`items.sort`，它接收一个函数，该函数接收两个参数，让我们称它们为`a`和`b`，如果`a`在`b`之前，则应该返回一个大于`0`的数字，如果`b`在`a`之前，则返回一个小于`0`的数字，如果两者具有相同的优先级，则返回一个小于`0`的数字。

在我们的例子中，我们希望对数组进行洗牌，而不是排序，所以我们不关心输入参数，只返回`Math.random() - 0.5`，这将在`-0.5`和`0.5`之间生成一个随机数。这可能不是生成非常混乱的结果的最佳算法，但对于演示目的来说，这就足够了。

现在来看测试用例。

```
test('sortItems sorts a randomly shuffled array', t => {
  const input = shuffle(items) // Remember how we declared items a few lines above?
  const result = sortItems(input)

  t.deepEqual(result, items)
  t.end()
}) 
```

然后*瞧*，我们有一个测试来验证一个由十个条目组成的随机列表是否总是被正确排序。

我们甚至可以更进一步，测试`items`数组的几种排列。

```
for (let i = 0; i < 20; i++) {
  test('sortItems sorts a randomly shuffled array', t => {
    const input = shuffle(items)
    const result = sortItems(input)

    t.deepEqual(result, items)
    t.end()
  })
} 
```

# 总结

在本教程中，我们已经学习了测试运行程序 [tape](https://github.com/substack/tape) 编写和执行单元测试的最基本功能，并且我们已经为函数`sortItems`创建了单元测试，它恰好是一个纯函数。

在本系列的下一部分中，我们将看到如何测试产生副作用的函数，如打印到控制台或读取文件。

# 挑战

*   从[这个列表](https://github.com/substack/tape#things-that-go-well-with-tape)中尝试不同的测试记者，选出你最喜欢的一个。
*   不要打乱`items`数组，而是为该数组生成所有可能的排列，并为每个排列运行测试用例。