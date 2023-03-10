# 像数学家一样编写测试:第 1 部分

> 原文：<https://dev.to/ijlee2/write-tests-like-a-mathematician-part-1-29e9>

*原载于[crunching numbers . live](https://crunchingnumbers.live/2019/08/04/write-tests-like-a-mathematician-part-1/)T3】*

Ember 给了你**写测试的能力，并且从第一天起就高效**。你可以相信你的应用在今天和几年后都是正确的。还有一个问题:*你应该如何编写测试？*

由于测试是 Ember 框架和您的开发周期的核心部分，我将根据我的工作经验和以前作为数学家的生活，写几篇关于编写测试的最佳实践的博文。

今天，我们将讨论为什么测试是重要的，什么工具可以帮助你进行测试，以及如何运行和调试你的测试。

请注意，一些工具可能只适用于 Ember。然而，我将提到的最佳实践应该独立于您的框架。毕竟，考试是一种通用语言，就像数学一样。

* * *

# 1。为什么我需要测试？

如果你想向用户和利益相关者保证你的应用程序，无论大小，在任何给定的时间都能正常运行，那么编写测试是一个必要的组成部分。应用程序越大，手动测试的成本就越高，也越容易出错。

<figure>

[![xkcd comic takes a light jab at an untested method.](img/a13f54a1f2f2f09e43bb698666c94aab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WwiHfVwg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/08/random_number.png)

<figcaption>xkcd takes a light jab at an untested method. ([source](https://xkcd.com/221/))</figcaption>

</figure>

编写测试也是一项有趣的活动，是对每天交付特性的一个很好的改变，也是帮助你作为开发人员重构代码和提高的一种方式。测试也可以作为一个活的文档——新开发人员入职的一个关键因素。

# 2。有哪些工具可以帮助我？

## a. QUnit, QUnit DOM

每个 Ember 应用都附带了 [QUnit](http://qunitjs.com/) 和 [QUnit DOM](https://github.com/simplabs/qunit-dom) 。QUnit 是一个测试框架，QUnit DOM 是一个帮助你**编写简洁易读的测试的库。您可以将 QUnit 和 QUnit DOM 用于任何 JavaScript 框架，而不仅仅是 Ember。**

要了解 QUnit DOM 的强大之处，请考虑下面的代码片段。它检查我们的按钮组件是否显示了正确的标签和正确的属性。

```
File: /tests/integration/components/simple-button-test.js

/*
    For simplicity, the import, module, and setup statements
    are omitted here. Our component accepts two arguments,
    label (string) and isDisabled (boolean).
*/
test('should show label', async function(assert) {
    await render(hbs`
        <SimpleButton
            @text="Hello world!"
        />
    `);
    let button = this.element.querySelector('button');

    // QUnit
    assert.strictEqual(button.textContent.trim(), 'Hello world!');

    // QUnit DOM
    assert.dom(button).hasText('Hello world!');
});

test('should allow disabling the button', async function(assert) {
    await render(hbs`
        <SimpleButton
            @text="Hello world!"
            @isDisabled={{true}}
        />
    `);
    let button = this.element.querySelector('button');

    // QUnit
    assert.strictEqual(button.disabled, true);
    assert.ok(button.classList.contains('is-disabled'));

    // QUnit DOM
    assert.dom(button).hasAttribute('disabled');
    assert.dom(button).hasClass('is-disabled');
}); 
```

## b .摩卡，柴 DOM

Mocha 是另一个测试框架。如果你对摩卡比较熟悉，可以安装 [ember-mocha](https://github.com/emberjs/ember-mocha) 和 [Chai DOM](https://www.chaijs.com/plugins/chai-dom/) 来代替。

同样，您可以将 Mocha 和 Chai DOM 用于任何 JavaScript 框架。如果你正在使用 Ember，记住 [ember-qunit](https://github.com/emberjs/ember-qunit) — `setupTest`、`setupRenderingTest`和`setupApplicationTest`的设置功能需要替换为 [ember-mocha](https://github.com/emberjs/ember-mocha) 的功能。

## c. Ember CLI

当你使用 [Ember CLI](https://ember-cli.com/generators-and-blueprints) 生成一个 Ember“对象”(如组件、模型、服务)时，它会创建一个测试文件，该文件的设置会正确处理你的测试框架和你应该编写的测试的[类型。](https://crunchingnumbers.live/2019/08/06/write-tests-like-a-mathematician-part-2/)

您还可以使用 Ember CLI 创建独立于对象的测试文件。例如，如果在终端中输入以下几行，

```
Terminal: /

ember g model-test student
ember g component-test student
ember g acceptance-test students 
```

您得到了一个针对`student`模型的**单元测试**，一个针对`student`组件的**渲染测试**(集成测试)，以及一个可以用来检查`students`路径及其子路径的**应用测试**(验收测试)。

## d .余烬测试选择器

您希望能够在测试中获取 DOM 元素。由于 Ember 只是 JavaScript，所以可以使用 [`querySelector`](https://developer.mozilla.org/docs/Web/API/Element/querySelector) 和 [`querySelectorAll`](https://developer.mozilla.org/docs/Web/API/Element/querySelectorAll) 来实现。这些方法要求您传递一个**选择器**，一个标识您想要的元素的字符串。

虽然您可以使用 CSS 类作为选择器，但是测试的最佳实践是**将样式和测试的关注点**分开。类名和 DOM 结构会随着您、您的团队和插件开发人员的改变而变得更好。如果你依赖 CSS 类，你的测试将会中断，需要大量的重写。

[Ember 测试选择器](https://github.com/simplabs/ember-test-selectors)是一个插件，帮助你**编写对 DOM 变化更有弹性的测试**。您使用`data-test-*`属性来标记将在您的测试中使用的元素。该插件与 QUnit DOM 和来自 [@ember/test-helpers](https://github.com/emberjs/ember-test-helpers/) 的助手一起工作。它还删除了生产版本中的`data-test-*`属性。

再次考虑按钮组件的例子。这次，除了标签之外，我们的组件还可以显示一个材质图标。

```
File: /app/components/simple-button.hbs

<button
    data-test-button={{@label}}
    type="button"
>
    {{#if @icon}}
        <i
            data-test-icon
            aria-hidden="true"
            class="material-icons"
        >
            {{@icon}}
        </i>
    {{/if}}

    <span data-test-label>{{@label}}</span>
</button> 
```

```
File: /tests/integration/components/simple-button-test.js

test('should show icon and label', async function(assert) {
    await render(hbs`
        <SimpleButton
            @icon="face"
            @label="Hello world!"
        />
    `);

    // Bad
    assert.strictEqual(
        this.element.querySelector('.material-icons').textContent.trim(),
        'face',
        'The user sees the correct icon.'
    );

    assert.strictEqual(
        this.element.querySelector('span').textContent.trim(),
        'Hello world!',
        'The user sees the correct label.'
    );

    // Good
    assert.strictEqual(
        this.element.querySelector('[data-test-icon]').textContent.trim(),
        'face',
        'The user sees the correct icon.'
    );

    assert.strictEqual(
        this.element.querySelector('[data-test-label]').textContent.trim(),
        'Hello world!',
        'The user sees the correct label.'
    );

    // Great!
    assert.dom('[data-test-icon]')
        .hasText('face', 'The user sees the correct icon.');

    assert.dom('[data-test-label]')
        .hasText('Hello world!', 'The user sees the correct label.');
}); 
```

## e. Ember CLI 幻影

如果您的应用程序接收和发送数据，您希望表明您可以成功地执行这些操作。您还想证明您可以正确处理错误状态。

Ember CLI Mirage 是一个插件，允许你创建一个模拟服务器。您还可以使用它来测试您的应用程序在各种服务器状态下的性能。要了解更多关于在测试中使用 Mirage 的信息，我们鼓励你[访问官方网站](https://www.ember-cli-mirage.com/docs/testing/acceptance-tests)。

## 余烬考试

您希望您的测试快速完成。快速运行意味着您可以尝试不同的解决方案，并进行多次迭代。

Ember Exam 是一个插件，允许你并行运行。如果您有许多渲染和应用程序测试，这可以大大加快您的测试。

<figure>

[![Ember Exam allows you to parallelize and randomize tests.](img/bb914d033b1b1cf1a66f610404e5c890.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bwWDSv3q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/08/ember_exam.png%3Fw%3D600%26h%3D400)

<figcaption>Ember Exam allows you to parallelize and randomize tests.</figcaption>

</figure>

Ember Exam 还可以让你随机选择测试的运行方式。你为什么要这么做？当您没有正确地设置和拆除测试时，您可能会在测试之间创建依赖关系。随机排序有助于您捕捉这些无意中的错误。

## g .珀西

最后但同样重要的是， [Percy](https://percy.io/) 是一个**视觉回归测试**工具，帮助你捕捉偶然的风格变化。你可以免费试用，并支付额外的服务。

<figure>

[![Percy's visual diff allows you to catch accidental style changes.](img/185386553614462d90d8be4d85a63a47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IL5OoJUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/08/ember_percy.png%3Fw%3D600%26h%3D600)

<figcaption>Percy's visual diff allows you to catch accidental style changes.</figcaption>

</figure>

虽然我们一般不推荐这种做法，但是您也可以使用 Percy 代替应用程序测试来捕获复杂的工作流。

# 3。如何运行测试

您有几个运行测试的选项。

首先，您可以通过在终端中输入命令`ember test`或`ember t`来运行测试套件。这将只运行该套件一次。

相反，假设您希望套件在每次文件更改后运行。可以输入`ember test --server`，或者`ember t -s`。

最后，如果你已经在运行一个本地开发服务器(通过`ember server`，你可以访问`/tests` URI。这将呈现出`tests/index.html`模板。

```
Terminal: /

# Run all tests once
ember test ember t

# Run all tests after every file change
ember test --server
ember t -s 
```

## a .如何过滤测试

当您处理单个组件或页面时，您会希望在每次文件更改后只运行一小部分测试。要指定运行哪些测试，您可以在您的命令中添加`--module`或`--filter`选项。

`--module`选项允许您选择一个**模块**——您在 QUnit 的`module()`或 Mocha 的`describe()`中指定的一组测试。

```
Terminal: /

# Button component example
ember test --server --module="Integration | Component | simple-button"

# Run tests for a location service
ember t -s -m="Unit | Service | location" 
```

`--filter`选项更加通用。您可以提供一个短语来匹配模块和测试描述。测试描述出现在 QUnit 的`test()`中，或者出现在 Mocha 的`it()`中。

```
Terminal: /

# Button component example
ember test --server --filter="should show icon and label"

# Test everything related to your dashboard
ember t -s -f="Dashboard"

# Run integration tests
ember t -s -f="Integration" 
```

在 QUnit 中，您可以通过在过滤器的开头添加一个感叹号来排除测试，例如`ember test --filter="!Acceptance"`。在摩卡，`ember test --filter="Acceptance" --invert`。

要了解更多关于测试选项的信息，你可以访问 [Ember CLI 文档](https://ember-cli.com/testing)或者在命令行中键入`ember help test`。

# 4。如何调试测试

当您编写测试或应用程序代码时，测试的执行可能会失败。

要找出问题，可以在代码中添加 [`debugger`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/debugger) 来检查中间状态。您可以将这一行添加到测试和应用程序代码中。

由于 Ember 的设置，您还可以使用 [`pauseTest()`](https://github.com/emberjs/ember-test-helpers/blob/master/API.md#pausetest) 和 [`resumeTest()`](https://github.com/emberjs/ember-test-helpers/blob/master/API.md#resumetest) 来调试您的测试。`pauseTest`允许你方便地检查 DOM，但是只能在测试代码中使用。

只需将`await pauseTest();`添加到您的测试代码中，然后保存。当测试到达这一行时，它将暂停，允许您检查应用程序的状态。完成后，在浏览器控制台中键入`resumeTest()`继续测试。

<figure>

[![You can use pauseTest to inspect the DOM.](img/7408edba678904a3137eeb9502537d13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ukRaYMVU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/08/ember_pausetest.png%3Fw%3D600%26h%3D450)

<figcaption>You can use pauseTest to inspect the DOM.</figcaption>

</figure>

<figure>

[![Type resumeTest in the console when you are finished with debugging your test.](img/289a623b366f682dbca4b27967a52497.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NPZEjRPW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/08/ember_resumetest.png%3Fw%3D600%26h%3D450)

<figcaption>Type resumeTest when you are finished with debugging your test.</figcaption>

</figure>

# 5。摘要

Ember 考虑测试一个一等公民。(据我所知，只有*的* JavaScript 框架在其[官方教程](https://guides.emberjs.com/release/tutorial/)中涵盖了测试。)除了提供集成 QUnit 和 Mocha 的简单途径，Ember 还支持各种插件和调试工具，以改善开发人员的测试体验。

下一次，我们将研究 Ember 支持的 3 种测试类型——单元测试、渲染测试和应用程序测试。我们将了解每种类型，以及何时可以使用其中一种。