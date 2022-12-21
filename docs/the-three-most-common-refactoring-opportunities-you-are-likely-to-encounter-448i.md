# 你可能会遇到的三个最常见的重构机会

> 原文：<https://dev.to/kylegalbraith/the-three-most-common-refactoring-opportunities-you-are-likely-to-encounter-448i>

重构是所有开发人员都会做的事情。当谈到何时重构代码时，我们往往有第六感。一些人在做重构决策时有坚实的原则可以遵循。其他人在工作的时候可以感觉到这是必要的。

无论你站在哪个阵营，重构都是所有代码库的必要过程。现实是需求在发展。决策来得很快。生产支持事件迫使我们做出超乎想象的快速反应。可能会发生各种各样的事情，使得重构代码成为必要。

但是我们怎么知道什么时候是必要的呢？它因代码库而异。但是，大多数开发人员至少认识到了三个常见的机会。

### 1。重复代码

作为开发人员，我们继承了天生的能力，能够察觉我们在重复自己。无论对错，我们已经认识到，重复自己是不好的，我们不应该这样做。

因此，自然地，我们倾向于发现需要重构的第一件事是我们重复自己的代码。我们甚至有一个以此命名的编码最佳实践。

> 不要重复你自己(DRY)是软件开发的一个原则，旨在减少软件模式的重复，[1]用抽象代替它或者使用数据规范化来避免冗余。(来源:[维基百科](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself))

重复的代码往往会意外出现，或者是我们稍后会修复的临时代码。它可能看起来像这样。

```
function doSomething(x, y, z) {
    let squared = Math.pow(x, 2)
    let summed = y + z
    let result = summed / squared

    if (result > 0) {
        return 'Yes'
    } else {
        return 'No'
    }
}

function doAnotherThing(x, y, z) {
    let squared = Math.pow(x, 2)
    let summed = y + z
    let result = summed / squared

    if (result <= 0) {
        return 'Yes'
    } else {
        return 'No'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一些琐碎的代码，但它很好地突出了我们作为开发人员的本能。当我们查看这段代码时，特别是如果这两个函数真的像我们这里这样相邻，我们会立即注意到重复的逻辑。我们看到一个结果是通过参数计算出来的，而`'Yes'`或`'No'`是基于这个结果返回的。返回值基于`result`对于`doAnotherThing`是小于 0 还是大于 0 对于`doSomething`。

我们在这里可以进行的重构非常简单。

```
function getResult(x, y, z) {
    let squared = Math.pow(x, 2)
    let summed = y + z
    return summed / squared
}

function doSomething(x, y, z) {
    let result = getResult(x, y, z)

    if (result > 0) {
        return 'Yes'
    } else {
        return 'No'
    }
}

function doAnotherThing(x, y, z) {
    let result = getResult(x, y, z)

    if (result <= 0) {
        return 'Yes'
    } else {
        return 'No'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的两个函数调用一个包含共享代码的公共函数`getResult`。

但是这对我们有什么好处呢？它允许公共逻辑集中在一个地方。

例如，我们的代码中有一个 bug，`summed / squared`容易出现被零除的错误，因为我们没有检查`x`是否不为 0。在重构之前，我们必须在代码重复的两个地方处理这个问题。相反，现在我们可以在新函数中处理这个问题，并且两个函数都得到修复。

```
function getResult(x, y, z) {
    let squared = Math.pow(x, 2)
    let summed = y + z
    if (squared > 0) { 
        return summed / squared
    } else {
        return 0
    }
}

function doSomething(x, y, z) {
    let result = getResult(x, y, z)

    if (result > 0) {
        return 'Yes'
    } else {
        return 'No'
    }
}

function doAnotherThing(x, y, z) {
    let result = getResult(x, y, z)

    if (result <= 0) {
        return 'Yes'
    } else {
        return 'No'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

重复的代码会导致许多 bug，因为 bug 可能会随之复制。开发人员不擅长更新出现重复的各个区域，因此一个区域可能得到了修复，而另一个区域则没有。如果不是我们上面的重构，我们可能很容易将对 0 的检查添加到一个函数中，而忽略了另一个函数。

但是，所有重复的代码都是坏的吗？

不。有时作为开发者，我们违反 DRY 原则是合理的。有些人甚至对 DRY 有不同的看法，他们不会创建一个公共函数，除非他们不得不重复代码超过两次。

其他场景，如基于微服务的架构，支持在必要时违反该原则。这是因为共享代码在跨越服务边界时会产生耦合。当您遇到重复代码重构的机会时，请记住这些事情。

### 2。长函数

继续，下一件我们倾向于认为需要重新粉刷的事情，长功能。与重复代码不同，我们重构长函数，以便更好地理解正在发生的事情。

这不同于从重构重复代码中获得的优势。在这种情况下，我们正在重构，以集中共享的逻辑，这样我们就不会重复自己。在这个新的机会中，我们将重构为更小的函数。不是因为它们的逻辑可以共享，而是为了让我们可以更好地理解每个部分处理什么。

让我们来看一个长函数的例子。

```
const run = async () => {
    const databaseOneClient = new Client({
        user: process.env.DB1_USER,
        host: process.env.DB1_HOST,
        database: process.env.DB1_DB,
        password: process.env.DB1_PASSWORD,
        port: 5439,
    })

    const databaseTwoClient = new Client({
        user: process.env.DB2_USER,
        host: process.env.DB2_HOST,
        database: process.env.DB2_DB,
        password: process.env.DB2_PASSWORD,
        port: 5432,
    })

    const databaseOneCountResult = await databaseOneClient.query(`select count(*) from table_one`)
    const databaseOneCount = databaseOneCountResult.rows[0].count
    const result = await databaseTwoClient.query(`select count(*) from copy_table`)
    const existingCount = result.rows.length != 1 ? 0 : result.rows[0].row_count

    for (var i = existingCount; i <= databaseOneCount; i++) {
        let newObj = {
            x: `lat_x_${i}`,
            y: `long_y_${i}`,
            z: `d_z_${i}`,
            url: ''
        }

        try {
            const response = await fetch(`https://somurl-to-call.com/api/do/${i}`)
            const jsonResponse = await response.json()
            if (jsonResponse.location >= 10) {
                newObj['url'] = jsonResponse.lookup
            }
            const insertQuery = `insert into copy_table(x, y, z, url) values ($1, $2, $3, $4) `
            await databaseTwoClient.query(insertQuery, [newObj.x, newObj.y, newObj.z, newObj.url])
        } catch (error) {
            console.error(error)
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，这并没有那么糟糕。我们正在连接到两个不同的数据库。一旦连接上，我们就可以从每个数据库的一个表中获得行数。如果这些行数不相等，在对外部服务进行 API 调用后，我们将一些生成的数据复制到第二个数据库中。

当我们用这样的语言表达时，事情就变得很清楚了。但是当我们通读代码时，有相当多的认知负荷来阅读正在发生的事情。幸运的是，这个功能并不太糟糕，因为它仍然适合在一个屏幕上显示。

如果它不适合一个屏幕，当我们向下滚动到底部时，我们可能会失去顶部发生的事情的上下文。

重构一个长函数的目的是保持代码的字面意思不变，但是减少理解它的认知负荷。为此，我们将函数的*块*移到它们自己独立的函数中。

这里有一个例子可以说明这一点。

```
const run = async () => {
    const databaseOneClient = initializeDatabaseOne()
    const databaseTwoClient = initializeDatabaseTwo()

    const databaseOneCount = await getDatabaseOneTotalCount(databaseOneClient)
    const existingCount = await getDatabaseTwoTotalCount(databaseTwoClient)

    for (var i = existingCount; i <= databaseOneCount; i++) {
        let newObj = {
            x: `lat_x_${i}`,
            y: `long_y_${i}`,
            z: `d_z_${i}`,
            url: ''
        }

        try {
            const response = await fetch(`https://somurl-to-call.com/api/do/${i}`)
            const jsonResponse = await response.json()
            if (jsonResponse.location >= 10) {
                newObj['url'] = jsonResponse.lookup
            }
            const insertQuery = `insert into copy_table(x, y, z, url) values ($1, $2, $3, $4) `
            await databaseTwoClient.query(insertQuery, [newObj.x, newObj.y, newObj.z, newObj.url])
        } catch (error) {
            console.error(error)
        }
    }

}

const getDatabaseOneTotalCount = async (databaseOneClient) => {
    const databaseOneCountResult = await databaseOneClient.query(`select count(*) from table_one`)
    return databaseOneCountResult.rows[0].count
}

const getDatabaseTwoTotalCount = async (databaseTwoClient) => {
    const result = await databaseTwoClient.query(`select count(*) from copy_table`)
    const existingCount = result.rows.length != 1 ? 0 : result.rows[0].row_count
    return existingCount
}

const initializeDatabaseOne = () => {
    return new Client({
        user: process.env.DB1_USER,
        host: process.env.DB1_HOST,
        database: process.env.DB1_DB,
        password: process.env.DB1_PASSWORD,
        port: 5439,
    })
}

const initializeDatabaseTwo = () => {
    return new Client({
        user: process.env.DB2_USER,
        host: process.env.DB2_HOST,
        database: process.env.DB2_DB,
        password: process.env.DB2_PASSWORD,
        port: 5432,
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

在一个屏幕上仍然有大量的文本，但是现在我们的一个大功能的逻辑被分成了更小的功能。

*   我们现在有两个初始化数据库客户机的函数(`initializeDatabaseOne`和`initializeDatabaseTwo`)。
*   我们还有两个从每个数据库获取表计数的函数(`getDatabaseOneTotalCount`和`getDatabaseTwoTotalCount`)。

这些不是共享函数，所以我们不会减少重复的代码，但是，我们会让主函数中的代码更容易理解。

这个小的重构使得我们第一次看到`run()`函数的开头时更容易理解。很明显，我们连接到两个数据库，并从每个数据库中获取计数。如果行数不相等，我们在进行 API 调用后将一些生成的数据复制到第二个数据库中。

请注意，我们没有重构循环或其内部。这是因为这是我们第三个最常见的重构机会，现在让我们来探讨一下。

### 3。复杂循环

重构的第三个也是最后一个机会是前一个机会的延伸。我们经常在代码中使用循环来迭代集合，并对它们执行操作和/或转换。

我们从上面的示例代码中可以看到这一点。

```
const run = async () => {
    const databaseOneClient = initializeDatabaseOne()
    const databaseTwoClient = initializeDatabaseTwo()

    const databaseOneCount = await getDatabaseOneTotalCount(databaseOneClient)
    const existingCount = await getDatabaseTwoTotalCount(databaseTwoClient)

    for (var i = existingCount; i <= databaseOneCount; i++) {
        let newObj = {
            x: `lat_x_${i}`,
            y: `long_y_${i}`,
            z: `d_z_${i}`,
            url: ''
        }

        try {
            const response = await fetch(`https://somurl-to-call.com/api/do/${i}`)
            const jsonResponse = await response.json()
            if (jsonResponse.location >= 10) {
                newObj['url'] = jsonResponse.lookup
            }
            const insertQuery = `insert into copy_table(x, y, z, url) values ($1, $2, $3, $4) `
            await databaseTwoClient.query(insertQuery, [newObj.x, newObj.y, newObj.z, newObj.url])
        } catch (error) {
            console.error(error)
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们从`existingCount`一直迭代到`databaseOneCount`。随着每一次迭代，我们都在构建一个新的对象，`newObj`，在外部 API 调用之后，我们将该对象插入到第二个数据库中。

这个内部循环可以被简化，稍微调整一下就更容易理解。

*注意:我已经省略了上面的一些代码，集中在这个复杂的循环部分*。

```
const run = async () => {
    const databaseOneClient = initializeDatabaseOne()
    const databaseTwoClient = initializeDatabaseTwo()

    const databaseOneCount = await getDatabaseOneTotalCount(databaseOneClient)
    const existingCount = await getDatabaseTwoTotalCount(databaseTwoClient)

    for (var i = existingCount; i <= databaseOneCount; i++) {
        await insertNewObject(i, databaseTwoClient)
    }

}

const insertNewObject = async (i, databaseTwoClient) => {
    let newObj = {
        x: `lat_x_${i}`,
        y: `long_y_${i}`,
        z: `d_z_${i}`,
        url: ''
    }

    try {
        const response = await fetch(`https://somurl-to-call.com/api/do/${i}`)
        const jsonResponse = await response.json()
        if (jsonResponse.location >= 10) {
            newObj['url'] = jsonResponse.lookup
        }
        const insertQuery = `insert into copy_table(x, y, z, url) values ($1, $2, $3, $4) `
        await databaseTwoClient.query(insertQuery, [newObj.x, newObj.y, newObj.z, newObj.url])
    } catch (error) {
        console.error(error)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

💥我们复杂的循环已经大大简化了。

等等，我们不是刚把循环内部移到一个单独的函数里了吗？是的，我们就是这么做的。这个小小的变化对理解`run`正在发生的事情产生了巨大的影响。通过一个描述性的函数名，我们看到我们从`existingCount`一直迭代到`databaseOneCount`和`insertNewObject`。

这一小小的改变在减少我们理解这段代码的认知负荷方面产生了巨大的差异。

当然，我们可以进一步重构`insertNewObject`函数。我们可以将 API 调用从数据库的实际插入中分离出来。但是，这是我留给读者的练习。

### 结论

重构是每个开发团队的必要过程。现实情况是，我们今天编写的代码是基于假设、时间限制和我们此时此刻可以利用的决策。所有这些在未来都将改变，因此我们的代码也需要改变。

重构的艺术是一个大话题，我们在这里仅仅触及了表面。还有许多我们在这里没有讨论过的其他模式和机会。但是，这里的三个应该在任何技术堆栈中都是通用的，并且应该让您对实践更加熟悉。

### 想看看我的其他项目？

我是 DEV 社区的超级粉丝。如果你有任何问题或者想谈谈关于重构的不同想法，请在下面留下评论或者在 Twitter 上联系。

在博客之外，我创建了一个[通过使用 It 课程](https://kylegalbraith.com/learn-aws/)学习 AWS。在本课程中，我们通过实际使用 Amazon Web Services 来托管、保护和交付静态网站，重点学习该服务。这是一个简单的问题，有许多解决方案，但它非常适合提升您对 AWS 的理解。我最近在课程中增加了两个新的额外章节，重点是代码基础设施和持续部署。

我也策划我自己的每周时事通讯。[边做边学简讯](https://kylegalbraith.com/learn-by-doing/)每周都充满了令人敬畏的云、编码和 DevOps 文章。注册即可在您的收件箱中获得它。