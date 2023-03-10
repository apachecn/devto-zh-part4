# 模板中调试测试的指南

> 原文：<https://dev.to/pr3tori4n/a-guide-on-debugging-tests-in-stencil-age>

> **先决条件:**本文假设您对在 Javascript、Stencil.js 和 VSCode 的调试器中编写和运行测试有一些基本的了解。如果你需要复习以上任何一项，底部有链接。
> 
> 我在这里将重点放在端到端测试上，因为这些是你将会写更多的测试类型，以给你你的 UI 正在工作的洞察力。当处理前端组件时，单元(规格)测试达不到这一点。

# 我的测试不工作，我不知道为什么

故事是这样开始的。你开始使用 Stencil.js，无论是出于爱好还是盈利，并且正在创建 web 组件。然后你开始为你的一些组件编写一些端到端的测试。测试没有通过，但应该会通过。或者在运行测试时出现错误。如果你像我一样，你可能已经注意到调试测试并不仅仅是添加一个断点那么简单——这里有一些陷阱。我将分享对我有效的方法。

## 有点背景有帮助

Stencil 在引擎盖下使用 Jest 和木偶师。你最好花点时间去尝试和理解你的测试代码的哪一部分使用了哪一个 API。一般来说，Jest 处理定义测试的样板文件(例如`describe()`和`it()`)，也处理断言(例如`expect()`和它所有的链式函数)。木偶师在一个叫做 Chromium(如`const page = await newE2EPage()`)的类似浏览器的环境(又名无头浏览器)中设置`page`，并将你的组件添加到页面中(如`page.setContent()`)。您还可以使用 Puppeteer 在 DOM 中查找元素，并检查属性、特性或其他状态，如`isVisible`。

同样值得注意的是，Stencil 用一些自定义函数和助手扩展了这些 API。`newE2EPage`上面显示的其实是由 Stencil 提供的，不是 puppet er，但是它返回的页面元素是一个 puppet er 页面类。这是福也是祸。Stencil 为您处理了大部分的 Puppeteer 样板文件，并提供了一些用于验证组件定制事件的实用工具。但是 Stencil 也用不同于各自库文档中的函数名重命名/包装了这些 API。在撰写本文时，Stencil 团队还没有明确记录别名或显著差异的列表。我从他们的测试页面中选取了一个示例片段，并添加了一些注释，以便让您有所了解。例如，Puppeteer 的原始 API 使用`page.$()`和`page.$$()`来查找一个元素或查找多个元素，但是 Stencil 扩展了这些，并将其称为`page.find()`和`page.findAll()`。穿孔选择器`>>>`非常有用，仅在这些扩展方法中可用。

```
// Stencil
import { newE2EPage } from '@stencil/core/testing';

// Jest
describe('example', () => {
  // Jest
  it('should render a foo-component', async () => {
    // Stencil wrapping Puppeteer. 
    const page = await newE2EPage();
    // Puppeteer
    await page.setContent(`<foo-component></foo-component>`);
    // Stencil wrapping Puppeteer 
    const el = await page.find('foo-component');
    // Jest
    expect(el).not.toBeNull();
  });
}); 
```

查看这些的最全面的方法是查看[模板类型定义文件](https://github.com/ionic-team/stencil/blob/c6bd82d21de3abc5bd555567f01c41ed44cc9651/src/testing/puppeteer/puppeteer-declarations.ts#L21)，它有可靠的代码注释。

## 节点还是铬？我的代码运行在哪个环境中？

上述相关的原因有两个。首先，它有助于了解你应该阅读谁的文件。第二，有两种不同的环境，每一种都有自己的范围，你的测试代码在其中运行——并且知道哪里是你正确设置断点和记录语句所必需的。**节点**是 Jest 代码和木偶师运行的地方。木偶师 start 是 **Chromium** 的一个实例，那是实际组件代码运行的地方。也可以使用 Puppeteer 的`page.evaluate()`在 Chromium 中运行代码，我们稍后会谈到。

## 已经给我看代码了

### 在节点中调试

**1。**将这些配置添加到您的 VSCode 调试配置中。并运行`Debug Stencil Tests`。

```
{  "version":  "0.2.0",  "configurations":  [  {  "type":  "node",  "request":  "launch",  "name":  "Debug Stencil tests",  "cwd":  "${workspaceFolder}",  "program":  "${workspaceFolder}/node_modules/.bin/stencil",  "args":  ["test",  "--spec",  "--e2e",  "--devtools"],  "console":  "integratedTerminal",  "internalConsoleOptions":  "neverOpen",  "disableOptimisticBPs":  true,  "windows":  {  "program":  "${workspaceFolder}/node_modules/@stencil/core/bin/stencil"  }  },  {  "type":  "node",  "request":  "launch",  "name":  "Spec Test Current File",  "cwd":  "${workspaceFolder}",  "program":  "${workspaceFolder}/node_modules/.bin/stencil",  "args":  ["test",  "--spec",  "${fileBasename}"],  "console":  "integratedTerminal",  "internalConsoleOptions":  "neverOpen",  "disableOptimisticBPs":  true,  "windows":  {  "program":  "${workspaceFolder}/node_modules/@stencil/core/bin/stencil"  }  },  {  "type":  "node",  "request":  "launch",  "name":  "E2E Test Current File",  "cwd":  "${workspaceFolder}",  "program":  "${workspaceFolder}/node_modules/.bin/stencil",  "args":  ["test",  "--e2e",  "${fileBasename}",  "--devtools"],  "console":  "integratedTerminal",  "internalConsoleOptions":  "neverOpen",  "disableOptimisticBPs":  true,  "skipFiles":  [  "${workspaceFolder}/node_modules/**/*.js",  "<node_internals>/**/*.js"  ],  "windows":  {  "program":  "${workspaceFolder}/node_modules/@stencil/core/bin/stencil"  }  }  ]  } 
```

> 其他的是有用的方便公式，允许您一次只运行一个文件。您必须打开该文件，并且该选项卡必须在焦点上，以便它能够工作。如果有帮助的话，你也可以在这里把`"--devtools"`加到`args`上。

**2。**在你的测试文件中添加一个`debugger;`语句。

> **更新:**从 1.3.0 开始，增加了对测试文件的 Sourcemap 支持。
> 
> 为什么不通过标记行号来设置断点呢？从`@stencil/core`的`v1.2.5`开始，typescript 编译器没有使用 sourcemaps。所以代码实际中断的那一行不同于你在文件中标记的那一行。有时，你可以通过将它向上移动，直到你看到你的手表值填充，来玩这个游戏，但这很烦人。

```
describe('example', () => {
  it('should render a foo-component', async () => {
    const page = await newE2EPage();
    await page.setContent(`<foo-component></foo-component>`);  
    const el = await page.find('foo-component');
    const isVisible = await el.isVisible();
    // Test execution will stop at this line.
    debugger;
    expect(isVisible).toBe(true);
  });
}); 
```

例如，这将允许您查看`el`的值。也许`isVisible`是未定义的，您想看看对 foo-component 的引用是否被正确检索。这将帮助您找出哪里不正确地使用了测试库 API，或者您使用的方法是否没有按照您期望的方式工作。

### 调试中铬

如果因为我们的测试代码看起来很好，但是它没有通过，所以我们需要从我们的组件代码中单步调试或者看到一个`console.log()`怎么办？木偶师的 [page.evaluate()](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pageevaluatepagefunction-args) 命令允许我们在组件运行的浏览器上下文中执行**的代码。**

> 可以添加 console.log()和调试器；语句，但是如果测试在 headless 模式下运行，这些语句不会有任何影响。更多信息请见下文。

让我们用一个更复杂的例子。假设您的组件有一个按钮，当单击该按钮时，应该隐藏另一个元素。您的测试可能看起来像这样。

```
it("should hide the content when the close button is clicked", async () => {
    const page = await newE2EPage();

    await page.setContent(`<foo-component></foo-component>`);
    const foo = await page.find("foo-component");

    const closeButton = await page.find(`foo-component >>> .close`);
    const content = await page.find(`foo-component >>> .content`);
    await closeButton.click();

    // Debugging Start
    // page.evaluate( () => { debugger; } );
    // Debugging End

    const isVisible = await content.isVisible();
    expect(isVisible).toBe(false);
  }); 
```

但出于某种原因，这不起作用。我们来调试一下。
**1。**首先让我们添加下面一行(上面注释掉了)。你也可以做`page.debugger()`——这是一个模板方便的方法，做同样的事情。

> 这将添加一个调试器；将在浏览器上下文(而不是节点上下文)中执行的命令。

**2。**其次，我们需要修改我们的测试配置。通常木偶戏 run 的 Chromium 是无头的(在没有图形的背景中)，但是我们需要改变它，这样我们就可以看到浏览器并使用 devtools。

#### 简单的方式

在 Stencil v1.0.7 中，他们引入了一个标志，你可以在 cli 中传递给 Stencil test 命令`devtools`，它告诉 Chromium 在 headed 模式下运行，打开和减慢开发工具，这样人们就可以看到发生了什么。它还调整了 Jest 的超时时间，这样在测试清理和关闭 Chromium 之前，你有超过 30 秒的时间进行调试。

`$ node_modules/.bin/stencil test --spec --e2e --devtools`

#### 手动

**注意:只有当模板的默认设置对你不起作用时才这么做。你必须告诉木偶师禁用无头模式并启用 devtools。这允许您像在 Chrome 中一样查看浏览器实例的运行情况，并检查元素和源代码选项卡。您还需要放慢测试速度，这样您就可以看到它们的运行。最后，你需要在 Jest 中设置一个超时，你需要在 Jest 中调整一些超时来防止浏览器在你调试过程中关闭。模板在其测试配置`browserHeadless`、`browserDevtools`和`browserSlowMo`中暴露。在您的测试文件中，您可以通过执行`jest.setTimeout(n);`
来覆盖 Jest 的默认超时**

```
// stencil.config.ts
import { Config } from "@stencil/core";

export const config: Config = {
   // other stuff
   testing: {
     browserHeadless: false
     browserDevtools: true
     browserSlowMo: 1000 //milliseconds
   }
} 
```

```
// test file
jest.setTimeout(100000); //milliseconds
/* Put the number at something absurd to give you plenty of time to work. 
 * Just don't leave this in the test once you get it working.
 */ 
```

现在你应该看到 Chromium 浏览器打开了(应该很像 Chrome ),里面有 Chrome 的开发工具。您在`page.evaluate()`中添加的调试器应该会暂停测试，然后您可以跳过它，看看您的组件中的业务逻辑是如何工作的(或者不工作；d)。

## 结论

1.  使用 VSCode 的调试器，我们可以逐步调试我们的测试代码，并找到测试代码本身的错误。
2.  运行模板测试时，使用`--devtools`标志禁用无头模式。这让我们设置调试器；组件代码中的语句告诉木偶师暂停。我发现如果你想看到你的组件被渲染，后者是很有用的，它有时可以帮助你直观地发现什么没有被正确渲染。如果您需要检查组件代码本身，比如验证某个状态是否有正确的值，前者会更好。

从那里开始，就是学习木偶剧和 Jest APIs(以及 Stencil 的附加组件)并找到你需要的那个。我花了一些时间才弄明白的一件事是，如何在页面上运行一些定制代码来进行 DOM 操作。对于这一点来说也很好——在 page 类中还有一些其他的方法可以帮助你。

不要忘记，几乎所有的 Puppeteer 函数都返回一个承诺，所以如果你需要事情同步运行，几乎每个方法都需要在它前面有`await`。

模板团队更新的很频繁。所以请留意[他们的 Changelog](https://github.com/ionic-team/stencil/blob/master/CHANGELOG.md) ，看看他们的测试设置是否有任何变化(这就是我如何找到- devtools 标志的原因:)。

> “但测试一切。保留好的。”1 Thes。5:21

## 参考文献

### 先决条件

[Stencil.js](https://stenciljs.com/docs/introduction)

[Javascript 测试基础知识](https://kentcdodds.com/blog/but-really-what-is-a-javascript-test)

[VSCode 调试器](https://code.visualstudio.com/docs/editor/debugging)

### 测试 API

#### 木偶师 API

[页面类](https://pptr.dev/#?product=Puppeteer&version=v1.17.0&show=api-class-page)
[元素句柄类](https://pptr.dev/#?product=Puppeteer&version=v1.17.0&show=api-class-elementhandle)
[调试木偶师](https://developers.google.com/web/tools/puppeteer/debugging)

#### [t1 是一个 API](#jest-api)

[核心](https://jestjs.io/docs/en/api)
T3】期待 T5[调试提示](https://jestjs.io/docs/en/troubleshooting)

### 模版测试

[概述](https://stenciljs.com/docs/testing-overview)

打字稿定义文件
这些文件中的注释作为替代任何正式文件的文档。

[模版测试配置](https://github.com/ionic-team/stencil/blob/0a2fcf37c27e2adc6028a7e2b5f2803064b39bf2/src/declarations/testing.ts#L404)

[模版木偶师声明](https://github.com/ionic-team/stencil/blob/c6bd82d21de3abc5bd555567f01c41ed44cc9651/src/testing/puppeteer/puppeteer-declarations.ts#L21)