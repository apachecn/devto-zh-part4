# 简单的 JavaScript:对模仿、存根和接口的简单介绍

> 原文：<https://dev.to/justindfuller/simply-javascript-a-straightforward-intro-to-mocking-stubbing-and-interfaces-12bg>

*这个帖子最初出现在[JustinDFuller.com](https://justindfuller.com/posts/2018-10-17_simply-javascript-a-straightforward-intro-to-mocking-stubbing-and-interfaces?utm_source=Dev.To&utm_medium=referral&utm_campaign=learning_javascript)。*

我喜欢认为我是一个简单的人，我喜欢简单的事情。所以每当我感觉到复杂，我的第一反应就是想知道我是否能让事情变得简单。

在我过渡到软件开发之前，我花了一段时间做音响工程师。我在录制乐队和混合现场表演。我甚至录制和混合直播节目进行广播。在那段时间里，我与太多的人交谈过，他们总是试图通过购买一些昂贵、更复杂的设备来解决问题。可悲的是，投资回报似乎从未达到它所承诺的水平。

我没有接受“越贵、越复杂越好”的哲学，而是每天晚上学习基础知识。我专注于基本技能。我学会了如何使用均衡器让声音听起来自然。我学会了如何使用压缩器来减弱快速而响亮的声音或增强微弱的声音。事实证明，这几个小时的投资回报超过了我的预期！

我最终选择了最简单的工具，并且我对自己的工作非常满意。

我相信同样的原则可以适用于生活的几乎每一个方面——财务、为人父母，甚至软件工程。

作为人，我们自然倾向于寻找华而不实、受欢迎的解决方案，这些方案承诺解决我们所有的问题(或者至少完美地解决一个问题)。我们被这些复杂的解决方案误导了。我们没有正确理解我们正在努力解决的任何问题的基本原理，从而制造了复杂的问题。

## JavaScript 基础知识

我们将学习基本的编程概念，以及如何将它们应用到 JavaScript 中。这里的目标是让代码更简单、更灵活、更容易理解和测试。

首先，在介绍接口之前，我想谈一个问题。

### 嘲讽、存根、突变

最近我在写从文件系统中读取文件的代码。代码运行得很好。为了测试它，我不得不使用一个库来阻止我的代码从文件系统中读取。如果我让它那样做，我的测试就会太慢了。此外，我还需要模拟在实际文件系统中实现起来很复杂的场景。

从历史上看，我会使用像 [Proxyquire](https://www.npmjs.com/package/proxyquire) 或 [Sinon](https://www.npmjs.com/package/sinon) 这样的库。Proxyquire 允许您覆盖文件的导入。Sinon 允许你改变一个对象的方法。您可以使用其中之一或两者来使您的代码更容易测试。虽然最好只用一个。

举个例子，假设你有一个模块叫做“a”。我们也假设模块“a”导入了模块“b”。Proxyquire 通过导入模块“a”并覆盖模块“b”的导出来工作。不会影响其他地方模块“b”的其他导入。Sinon 通过改变模块“b”的输出来工作。它会影响到每一个导入模块“b”的地方，所以你一定要记得在完成后恢复它。

```
/* This is my file I'll be testing foo.js */

import fs from 'fs'
import { promisify } from 'util'

const readFileAsync = promisify(fs.readFile)

export function readJsonFile (filePath) {
 return readFileAsync(filePath).then(JSON.parse)
}

/* This is my test file foo.test.js */

import fs from 'fs'
import test from 'ava';
import { stub } from 'sinon'
import proxyquire from 'proxyquire'

test('readJsonFile with proxyquire', async function (t) {
  t.plan(2)

  /* fs.readFile is overwritten for this import of foo.js */
  const { readJsonFile } = proxyquire('./foo.js', {
    fs: {
      readFile(filePath, callback) {
        t.is(filePath, 'myTestFile')

        return callback(null, '{ success: true }')
      }
    }
  })

  const results = await readJsonFile('myTestFile')
  t.deepEqual(results, { success: true })
})

test('readJsonFile with sinon', async function (t) {
  t.plan(1)

  /* fs.readFile is overwritten everywhere */
  const fsStub = stub(fs, 'readFile')
    .withArgs('myTestFile')
    .callsArg(2, null, '{ success: true }')

  const results = await readJsonFile('myTestFile')
  t.deepEqual(results, { success: true })

  // Won't happen if test fails :(
  fsStub.restore()
}) 
```

### 存根为什么不好？

这两种选择都不太好，因为它们涉及到变异。在软件开发中，我们希望尽可能避免突变。因为突变会降低应用程序的可预测性。

一个小小的突变似乎从来都不是什么大事。但是当有许多小的突变时，就很难跟踪哪个函数改变了什么值，以及每个突变是何时发生的。

还有锁定的麻烦。如果您将文件系统库从 fs 更改为 fs-extra-promise，sinon 和 proxyquire 都会要求您更新测试。在这两种情况下，您仍将使用 readFileAsync 函数。然而，sinon 和 proxyquire 将继续尝试覆盖 fs.readFile。

## 有哪些替代品？

为了解决这个问题，我遵循了一个叫做[依赖倒置](https://en.wikipedia.org/wiki/Dependency_inversion_principle)的原则。我的模块不是创建它的依赖项，而是期望获得它的依赖项。这产生了更容易测试和更灵活的模块。也可以让它们与相同依赖项的许多实现一起工作。

```
/* This is my file I'll be testing foo.js */

export default function ({ readFileAsync }) {
  return {
    readJsonFile (filePath) {
     return readFileAsync(filePath).then(JSON.parse)
    }
  }
}

/* This is my test file foo.test.js */

import test from 'ava'

import foo from './foo'

test('foo with dependency inversion', function (t) {
  t.plan(2)

  const dependencies = {
    readFileAsync(filePath) {
      t.is(filePath, 'bar')

      return Promise.resolve('{ success: true '})
    }
  }

  const result = await foo(dependencies).readJsonFile('bar')
  t.deepEqual(result, { success: true })
}) 
```

不仅在我们的代码中节省了宝贵的代码行，而且也没有更令人担忧的突变发生了！该模块现在将接受 readFileAsync，而不是自己创建该函数。该模块更好，因为它更集中，责任更少。

### 依赖去哪了？

依赖项必须被导入到某个地方。在遵循依赖倒置的应用程序中，您应该尽可能地将依赖关系“移出”。最好在应用程序的入口点一次性导入它们。

```
/* json.js */

export default function ({ readFileAsync, writeFileAsync }) {
  return {
    readJsonFile(fileName) {
      return readFileAsync(`${fileName}.json`).then(JSON.parse) 
    },
    writeJsonFile(filePath, fileContent) {
      return writeFileAsync(filePath, JSON.stringify(fileContent)) 
    }
  }
}

/* content.js */

export default function ({ readJsonFile, writeJsonFile }) {
  return {
     getContent(contentName) {
      // business logic goes here.
      return readJsonFile(contentName)
     },
     writeContent(contentName, contentText) {
      // business logic goes here
      return writeJsonFile(contentName, contentText) 
     }
  }
}

/* index.js where the app starts */

import fs from 'fs-extra-promise'
import jsonInterface from './json'
import contentInterface from './content'

const json = jsonInterface(fs)
const content = contentInterface(json)

// content can be used by an http server
// or just exported if this is a library
export default content 
```

在这个例子中，您看到依赖项被移动到了应用程序的入口点。除了 index.js，其他都接受接口。这使得应用程序变得灵活、易于更改和易于测试。

## 依存倒置还能做什么？

既然您已经爱上了依赖倒置，我想向您介绍它的更多功能。

当您的模块接受一个接口时，您可以将该模块用于该接口的多个实现。在这个场景中，库[类型脚本](https://www.typescriptlang.org/)和[流](https://flow.org/)会很有用。他们会检查你是否提供了正确的接口。

一个接口仅仅是方法和属性的集合。所以说模块接受一个接口，我是说模块接受一个实现一组方法和属性的对象。期望接口类似地实现不同的功能。

您可能知道的一个常见接口是 React 组件接口。在 TypeScript 中可能是这样的:

```
interface ComponentLifecycle {
      constructor(props: Object);
      componentDidMount?(): void;
      shouldComponentUpdate?(nextProps: Object, nextState: Object, nextContext: any): boolean;
      componentWillUnmount?(): void;
      componentDidCatch?(error: Error, errorInfo: ErrorInfo): void;
      setState(
          state: ((prevState: Object, props: Object) => Object,
          callback?: () => void
      ): void;
      render(): Object | null;
      state: Object;
  } 
```

如果您没有理解界面中的所有内容，请不要绝望。关键是 React 组件有一组可预测的方法和属性，可用于制作许多不同的组件。

我们现在开始进入[开闭原则](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)的领域。它声明我们的软件应该对扩展开放，但对修改关闭。如果你一直在用像 [Angular](https://angularjs.org/) 或 [React](https://reactjs.org/) 这样的框架构建软件，这听起来可能很熟悉。它们提供了一个公共接口，您可以扩展该接口来构建您的软件。

现在，你可以开始依靠你的内部接口来创建你的软件，而不是事事依赖第三方接口。

如果您正在编写一个 CRUD(创建、读取、更新、删除)应用程序，您可以创建一个为您的操作提供构建块的接口。您的模块可以扩展该接口来实现业务逻辑和用例。

如果您正在编写执行任务的应用程序，您可以构建一个任务接口，为不同的任务提供构建块。每个任务都可以接受任务接口并扩展它。

依赖倒置和开闭原则允许您编写更多可重用、可测试和可预测的软件。你将不再有杂乱无章的代码。相反，您将拥有一组遵循相同模式的统一模块。

### 许多实现

接受接口还有一个好处。您可以用许多不同的方式实现该接口。

这是我最喜欢的例子。假设您有一个 CRUD 应用程序的接口。您可以有一个实现数据库存储的接口。这很好，但是如果数据库读或写变得很慢怎么办？您还可以编写一个更快的实现，使用 [Redis](https://redis.io/) 或 [Memcached](https://www.memcached.org/) 来加快响应时间。你需要做的唯一改变就是编写一个新的接口。不需要更新业务逻辑或其他任何东西。

您可以将 React 和 [React-Native](https://facebook.github.io/react-native/) 视为这方面的流行示例。它们都使用相同的 React 组件和 React DOM 接口，但实现方式不同。即使在 React Native 内部，也有针对 IOS 和 Android 的实现。多重实现允许您一次编写逻辑，并以多种方式执行它。

## 现在怎么样了？

既然您已经了解了依赖倒置和开闭原则，那么是时候将它应用到您的代码中了。不要在你写的下一个模块中写任何导入。相反，允许它接受一个接口。在您的测试中，您将能够避免改变您的依赖性的第三方库！然后试着开始识别哪些地方可以使用公共接口。你会慢慢地但肯定地创建一个更好的应用程序！

* * *

这是一个转贴，帖子最初出现在[www.justindfuller.com](https://justindfuller.com/posts/2018-10-17_simply-javascript-a-straightforward-intro-to-mocking-stubbing-and-interfaces)上。

* * *

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不代表我的雇主。所有代码样本都是我自己的，与我雇主的代码完全无关。

我也很想收到你的来信，请随时通过 [Github](https://github.com/justindfuller) 或 [Twitter](https://twitter.com/justin_d_fuller) 与我联系。再次感谢阅读！