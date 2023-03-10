# 使用函数式编程避免中间变量和嵌套函数

> 原文：<https://dev.to/ccleary00/using-functional-programming-to-avoid-intermediate-variables-and-nested-functions-35jj>

***原载于 [coreycleary.me](https://www.coreycleary.me/using-functional-programming-to-avoid-intermediate-variables-and-nested-functions/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

通常，当我们开发一段代码时，我们需要获取一个初始值，并在返回该值之前对其应用几个函数。

大概是:

```
const incompleteTasks = getIncomplete(tasks)
const withoutBlockedTasks = getNonBlocked(incompleteTasks)
const sortedByDueDate = sortByDueDate(withoutBlockedTasks)
const groupedByAssignee = groupByAssignee(sortedByDueDate)
// etc... 
```

这个的问题是它很难阅读。每当你添加中间变量(`incompleteTasks`、`withoutBlockedTasks`等)。)，您必须跟踪哪些是作为参数传递给下一个函数的。所以当你读代码的时候，你会做很多变量跟踪。如果我们不能在其他地方使用它们，为什么还要创建一堆中间变量呢？感觉是一种浪费。

当然，如果只有几个变量不应该太影响可读性/理解代码，但是当你需要通过许多函数传递一个起始值时，它会很快变得混乱和痛苦。

使用中间变量的一种方法是做如下事情:

```
groupByAssignee(sortByDueDate(getNonBlocked(getIncomplete(tasks)))) 
```

...但是像这样使用嵌套函数会使**更加不可读。祝您好运添加调试断点！**

### 函数式编程的拯救

使用一种叫做*函数组合*的函数式编程模式，我们可以让某些东西**更具可读性，而不需要中间变量或嵌套函数。**

这将使那些阅读你的代码和审查你的拉请求变得更加容易。

现在每个人都想使用函数式编程——这是现在很酷的事情，而且有充分的理由。我发现，仅仅通过使用函数式组合，你就可以走得很远，并获得函数式编程的许多好处，而不必学习其他更复杂的东西，比如 monad 到底是什么。

所以就当这是一举两得吧！这将使代码更具可读性*并且*你将开始使用更多的函数式编程。

### 操作组合

与其先用定义来解释复合，不如让我们看看代码。我们的原始代码获得了迭代中每个用户剩余的未完成任务，看起来像这样:

```
const { pipe } = require('ramda')

// here are the individual functions, they haven't changed from the above,
// just including them so you can see their implementation
const getIncomplete = tasks => tasks.filter(({complete}) => !complete)

const getNonBlocked = tasks => tasks.filter(({blocked}) => !blocked)

const sortByDueDate = tasks => tasks.sort((a, b) => new Date(a.dueDate) - new Date(b.dueDate))

const groupBy = key => array => {
    return array.reduce((objectsByKeyValue, obj) => {
        const value = obj[key]
        objectsByKeyValue[value] = (objectsByKeyValue[value] || []).concat(obj)
        return objectsByKeyValue
    }, {})
}

const groupByAssignee = groupBy('assignedTo')

// this is the magic
const getIterationReport = pipe(
    getIncomplete,
    getNonBlocked,
    sortByDueDate,
    groupByAssignee
) 
```

很简单，对吧？我们只是把我们的函数放入一个`pipe`函数中...就是这样！而要调用函数，只需:

```
const report = getIterationReport(tasks) 
```

*等等，我以为`getIterationReport`是变量，不是函数？*

这里我们使用的是来自函数式编程库 [Ramda](https://ramdajs.com/) 的`pipe`函数。`pipe`返回一个函数，所以`getIterationReport`的值实际上是一个函数。这让我们可以用我们想要的任何数据调用它，在这个例子中是`tasks`。

因此，功能组合允许我们将功能“链接”在一起以创建另一个功能。就这么简单！我们不必像使用中间变量方法那样存储转换原始数据的每一步的结果，我们只需**定义这些步骤是什么**。

这:

```
const getIterationReport = pipe(
    getIncomplete,
    getNonBlocked,
    sortByDueDate,
    groupByAssignee
) 
```

比这个好多了:

```
const getIterationReport = tasks => {
    const incompleteTasks = getIncomplete(tasks)
    const withoutBlockedTasks = getNonBlocked(incompleteTasks)
    const sortedByDueDate = sortByDueDate(withoutBlockedTasks)
    return groupByAssignee(sortedByDueDate)
} 
```

### 作文种类

一般有两种构图- `compose`、`pipe` - [构图为从右向左](https://www.coreycleary.me/why-is-compose-right-to-left/)、`pipe`为从左向右。

我更喜欢使用`pipe`,因为它遵循西方从左到右(或者从上到下，就像我们在这里格式化的那样)阅读的标准，并且更容易理解你的数据将如何按顺序通过每个函数。

### 论争论

大多数`pipe`和`compose`实现将只操作一个参数，即 FP 术语中的“一元”。所以复合最适合采用一个值的函数(就像我们这里的`tasks`)并对该值进行操作。如果除了`tasks`之外，我们还必须传入其他参数，我们的`getIterationReport`函数就不会工作。

有很多方法可以改变你的函数来解决这个问题，但是这超出了本文的范围。

只要知道如果你使用 Ramda 的管道，[第一个函数可以有任意数量的参数](https://ramdajs.com/docs/#pipe)，但是其余的必须是一元的。所以如果你有一个需要多个参数的函数，把它放在第一个`pipe`中。

### 数据和结果

现在，为了完成图片的其余部分，让我们来看看我们将调用这个函数的数据:

```
const tasks = [
    {
        assignedTo: 'John Doe',
        dueDate: '2019-08-31',
        name: 'Add drag and drop component',
        blocked: false,
        complete: false
    },
    {
        assignedTo: 'Bob Smith',
        dueDate: '2019-08-29',
        name: 'Fix build issues',
        blocked: false,
        complete: false
    },
    {
        assignedTo: 'David Riley',
        dueDate: '2019-09-03',
        name: 'Upgrade webpack',
        blocked: true,
        complete: false
    },
    {
        assignedTo: 'John Doe',
        dueDate: '2019-08-31',
        name: 'Create new product endpoint',
        blocked: false,
        complete: false
    }
] 
```

当我们调用该函数时，结果将类似于:

```
{
    'Bob Smith': [{
        assignedTo: 'Bob Smith',
        dueDate: '2019-08-29',
        name: 'Fix build issues',
        blocked: false,
        complete: false
    }],
    'John Doe': [{
            assignedTo: 'John Doe',
            dueDate: '2019-08-31',
            name: 'Add drag and drop component',
            blocked: false,
            complete: false
        },
        {
            assignedTo: 'John Doe',
            dueDate: '2019-08-31',
            name: 'Create new product endpoint',
            blocked: false,
            complete: false
        }
    ]
} 
```

正如您所看到的，我们过滤掉了已完成和阻塞的任务，并由开发人员对它们进行分组。

虽然我们的任务数据结构不是非常复杂，但希望这有助于您了解我们可以多么容易和干净地使用组合来转换数据，而不必求助于使用中间变量来存储转换序列的每一步。

所以下一次你发现自己在编写类似于:
的代码时

```
const incompleteTasks = getIncomplete(tasks)
const withoutBlockedTasks = getNonBlocked(incompleteTasks)
const sortedByDueDate = sortByDueDate(withoutBlockedTasks)
const groupedByAssignee = groupByAssignee(sortedByDueDate)
// etc... 
```

当您将每个步骤的结果存储为一个变量并将结果传递给下一个函数时，可以使用 Ramda 的`compose`或`pipe`或者您选择的任何库来使其更易于阅读和推理！

如果你觉得这篇文章有帮助，[这里是链接](https://www.coreycleary.me/about/)订阅我的时事通讯！