# 通过编写更小的 JavaScript 函数来简化单元测试，减轻嘲笑/存根的痛苦

> 原文：<https://dev.to/ccleary00/lessen-the-pain-of-mocking-stubbing-by-writing-smaller-javascript-functions-for-easier-unit-testing-3lpb>

***原载于 [coreycleary.me](https://www.coreycleary.me/lessen-the-pain-of-mocking-stubbing-by-writing-smaller-javascript-functions-for-easier-unit-testing/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

我们来谈谈为 JavaScript /节点应用编写**单元**测试。

是的，你知道你“应该写测试”。

你真的去过！

为返回某个值的函数编写测试:你知道怎么做。对于预期的*输出*值，比如`true`或`some string`或`toBeDefined`，你可以放心地编写测试。

但是，正是这些*其他*函数——比如调用 API、访问数据库，甚至只是做几件不同的事情(比如复杂的 if/else 分支)——才是你在编写单元测试时遇到困难的。

因为在那些场景中，你通常不得不写一些 stub/mock 或者做一些依赖注入(DI ),以便真正地对它们进行测试——只是测试函数中的逻辑，而不是其他。

但是如果你*能够*轻松地为那些函数编写测试(即使这意味着你可能不得不做一些嘲讽)，我打赌你会**编写更多的**测试，并且会更**舒服地**对你的代码库进行修改，知道你不会意外地破坏某些东西。

### 分解你的函数，使它们更容易编写单元测试

即使你不能总是避免测试中的存根/模仿，你也可以经常 T2 分解你的函数，让它们更容易为没有 T5 存根/模仿的 T4 编写真正的单元测试。

很多很多次，我看到一些函数发出 HTTP 请求或者从数据库中获取数据，但是它们并不只是这样做...

他们也在做一些其他的事情...

比如从 HTTP 请求中过滤结果数据，或者根据作为参数传递给函数的某个标志格式化来自数据库调用的数据，等等。

我经常会看到在此基础上做更多事情的函数！

因此，当要为这些函数编写测试时，除了可能必须将其他代码段*内部*存根化到函数之外，您还必须存根化/模拟数据库/API 调用。*T3】*

谈论巨大的痛苦。

但是有一条出路。

那就是编写更多的“单元”**函数**，这样你就可以更容易地编写单元**测试**。

这看起来很简单，但是如果你想更容易地编写单元测试，你必须编写更多的单元函数。被分解成尽可能小的部分(或者足够小)的函数。

然后你有一个**集成函数**，它接受那些小单元函数，然后*集成*它们。以应用程序需要的方式组合它们。

就像测试一样。你有测试最小级别事物的单元测试，然后你有测试更大事物的集成测试，这些事物被集成在一起并做几件不同的事情。

不过这次的不同之处在于，这两种测试都将更加容易编写。而且你可能根本不需要存根/嘲笑任何东西！

### 示例罪犯

让我们来看看一个函数，按照它目前的情况，为它编写测试是很痛苦的。这个函数做了几件小事，但是这些小事中的每一件目前都不作为它自己的函数存在。

```
async function getLoanInfo() {
    const people = await callDb()

    const financialAttributes = await callHttp()

    return people.map(person => {
        return {
            person,
            ageGroup: (person.age && person.age >= 50) ? '50 and up' : '49 and below',
            meta: financialAttributes.find(attribute => person.zipCode === attribute.zipCode)
        }
    })
} 
```

除了从数据库获取人员记录和从第三方 API 获取财务属性之外，该函数还基于一些业务逻辑连接/格式化数据。

这里的*业务逻辑*——连接/格式化记录的逻辑——有些做作，但在现实世界中很常见。

如果我们想要测试这一点，我们必须为我们想要测试的每个逻辑路径存根数据库调用和 API 调用。这里我们最想测试的逻辑是什么？连接/格式化正确发生。

我们可以将外部依赖关系(数据库和 API)提取到它自己的函数中，而不是仅仅测试连接逻辑，就像这样:

```
function joinAndFormat(people, financialAttributes) {
    if (!people || !financialAttributes) return

    return people.map(person => {
        return {
            person,
            ageGroup: (person.age && person.age >= 50) ? '50 and up' : '49 and below',
            meta: financialAttributes.find(attribute => person.zipCode === attribute.zipCode)
        }
    })
} 
```

更小更容易测试！

编写更小的单位函数的好处之一是，当它是一个更大的函数的一部分时，你可以看到你可能错过的东西。例如，在这个新函数中，我意识到如果没有传入`people`或`financialAttributes`，我们可能应该早点退出！

现在，在我们原来的`getLoanInfo()`函数中，我们只是用新的单元函数
替换了连接/格式代码

```
async function getLoanInfo() {
    const people = await callDb()

    const financialAttributes = await callHttp()

    return joinAndFormat(people, financialAttributes)
} 
```

更小更易读！

### 现在，为了测试

在**单元级别**测试东西，这些单元测试看起来是这样的:

```
const deepEqualInAnyOrder = require('deep-equal-in-any-order')
const chai = require('chai')

const { joinAndFormat } = require('./index')

const { expect } = chai
chai.use(deepEqualInAnyOrder)

describe('joinAndFormat()', () => {
    it('should return null if missing args', () => {
        const people = [{person: 'tom'}]
        const formatted1 = joinAndFormat(people)

        expect(formatted1).to.be.null

        const formatted2 = joinAndFormat()

        expect(formatted2).to.be.null
    })

    it('should format correctly', () => {
        const people = [
            {person: 'Tom', age: 50, zipCode: 21345},
            {person: 'Jack', age: 40, zipCode: 31680}
        ]

        const financialAttributes = [
            {zipCode: 21345, attributes: {spending: 'high', creditScoreAvg: 750}},
            {zipCode: 31680, attributes: {spending: 'low', creditScoreAvg: 730}},
            {zipCode: 45560, attributes: {spending: 'high', creditScoreAvg: 600}}
        ]

        const formatted = joinAndFormat(people, financialAttributes)

        expect(formatted).to.deep.equal([{
            person: {person: 'Tom', age: 50, zipCode: 21345},
            ageGroup: '50 and above',
            financialInfo: {zipCode: 21345, attributes: {spending: 'high', creditScoreAvg: 750}}
        },
        {
            person: {person: 'Jack', age: 40, zipCode: 31680},
            ageGroup: '49 and below',
            financialInfo: {zipCode: 31680, attributes: {spending: 'low', creditScoreAvg: 730}}
        }])
    })

    it('should designate people as 50 and above', () => {
        const people = [
            {person: 'Tom', age: 50, zipCode: 21345}
        ]

        const financialAttributes = [
            {zipCode: 21345, attributes: {spending: 'high', creditScoreAvg: 750}}
        ]

        const formatted = joinAndFormat(people, financialAttributes)

        expect(formatted.pop().ageGroup).to.equal('50 and above')
    })

    it('should designate people as 49 and below', () => {
        const people = [
            {person: 'Tom', age: 49, zipCode: 21345}
        ]

        const financialAttributes = [
            {zipCode: 21345, attributes: {spending: 'high', creditScoreAvg: 750}}
        ]

        const formatted = joinAndFormat(people, financialAttributes)

        expect(formatted.pop().ageGroup).to.equal('49 and below')
    })
}) 
```

我们不必存根/模仿数据库和 API 对`people`和`financialAttributes`的调用，我们只需在返回它们的结构中添加一些假数据。我们可以避免复杂的测试设置！

### 使用这种方法

每当你很难弄清楚如何为一个函数编写一个*单元*测试时，在你开始 stub/mock 之前，看看你是否可以将任何代码提取到一个单独的函数中。寻找可以从外部调用中分离出来的业务逻辑代码。在 if/else 或 switch 主体中寻找逻辑。并把它们放到自己的函数中。

有时，stub/mock 函数似乎是测试函数逻辑的唯一方法，但是使用这种方法，您通常可以避免在单元测试中这样做！

这将使编写测试变得更加容易。我发现测试只有在容易编写的时候才会被编写...

如果你觉得这篇文章有帮助，[这里是链接](https://www.coreycleary.me/about/)订阅我的时事通讯！