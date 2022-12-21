# 像数学家一样编写测试:第 2 部分

> 原文：<https://dev.to/ijlee2/write-tests-like-a-mathematician-part-2-9gf>

*原载于[crunching numbers . live](https://crunchingnumbers.live/2019/08/06/write-tests-like-a-mathematician-part-2/)T3】*

Ember 提供了 3 种开箱即用的测试:

*   单元测试
*   渲染测试(以前称为集成测试)
*   应用测试(以前称为验收测试)

概括地说，这些测试在两个方面有所不同:

*   他们检查你的应用程序的哪些部分是正确的。拥有不同类型的测试有助于分离测试关注点。
*   他们的执行速度有多快。

让我们来看看每一种类型，以及什么时候可以使用其中的一种。

# 1。单元测试

## a .定义

单元测试检查各个方法和函数的**正确性。给定一个输入，方法是否返回正确的输出？因为单元测试可以在方法级别检查代码，所以它们可以构成测试套件**的**基础。单元测试本质上也是非常快的**。****

 **当您使用 Ember CLI 创建[适配器](https://guides.emberjs.com/release/models/customizing-adapters/)、[控制器](https://guides.emberjs.com/release/controllers/)、[初始化器](https://guides.emberjs.com/release/applications/initializers/)、[模型](https://guides.emberjs.com/release/models/defining-models/)、[序列化器](https://guides.emberjs.com/release/models/customizing-serializers/)、[服务](https://guides.emberjs.com/release/applications/services/)和实用程序时，单元测试会自动创建。我希望在以后的博客文章中涵盖每个方面的编写测试。

## b .为什么要用它们？

单元测试有三个好处。

第一，单元测试通常是隔离的，并且集中在单独的方法和函数上，所以当测试失败时很容易调试。

第二，单元测试可以让你专注于在更高层次的测试中可能难以运用的小逻辑。

最后，单元测试运行速度极快，因此您可以检查参数的许多排列，而对测试套件性能的影响极小。

## c .例子

下面的代码显示了单元测试如何检查单个方法。想象一下，我们的应用程序有一个帮助我们处理数字的工具。

```
File: /tests/unit/math-library-test.js

import { module, test } from 'qunit';
import { getDivisors, isPrime } from 'our-app-name/utils/math-library';

module('Unit | Utility | math-library', function() {
    test('should check if a number is prime', function(assert) {
        assert.strictEqual(isPrime(1), false);
        assert.strictEqual(isPrime(2), true);
        assert.strictEqual(isPrime(3), true);
        assert.strictEqual(isPrime(4), false);
        assert.strictEqual(isPrime(5), true);
        assert.strictEqual(isPrime(6), false);
    });

    test('should get all divisors of a number', function(assert) {
        assert.deepEqual(getDivisors(1), [1]);
        assert.deepEqual(getDivisors(2), [1, 2]);
        assert.deepEqual(getDivisors(3), [1, 3]);
        assert.deepEqual(getDivisors(4), [1, 2, 4]);
        assert.deepEqual(getDivisors(5), [1, 5]);
        assert.deepEqual(getDivisors(6), [1, 2, 3, 6]);
    });
}); 
```

这里有更多单元测试比较理想的例子:

*   在控制器内部，一个计算属性在一个动作被执行后继续正确地过滤`this.model`
*   检查串行器中的 [`normalize()`](https://api.emberjs.com/ember-data/release/classes/JSONAPISerializer/methods/normalize?anchor=normalize) 如何接收数据
*   检查串行器中的 [`serialize()`](https://api.emberjs.com/ember-data/release/classes/JSONAPISerializer/methods/serialize?anchor=serialize) 如何发送数据
*   一个 [cron](https://en.wikipedia.org/wiki/Cron) 实用程序将输入字符串解析成一个可用于 UI 的对象

## d .需要注意什么

当单元测试涉及 Ember 框架时，必须导入并调用 [`setupTest()`](https://github.com/emberjs/ember-qunit#setup-tests) ，然后传递`hooks`对象。(不用担心。 [Ember CLI](https://crunchingnumbers.live/2019/08/04/write-tests-like-a-mathematician-part-1/) 将为您做到这一点！)

例如，考虑一个保存了一组消息的服务，这些消息将在以后显示给用户:

```
File: /tests/unit/services/flash-messages-test.js

import { setupTest } from 'ember-qunit';
import { module, test } from 'qunit';

module('Unit | Service | flash-messages', function(hooks) {
    setupTest(hooks);

    test('should be able to buffer messages', function(assert) {
        let service = this.owner.lookup('service:flash-messages');

        service.add('Hello');
        service.add('World!');

        assert.deepEqual(service.get('messages'), ['Hello', 'World!']);
    });
}); 
```

通过调用`setupTest()`，你可以访问一些东西。首先是 Ember 的[依赖注入](https://guides.emberjs.com/release/applications/dependency-injection/)系统。简而言之，在`this.owner`的一点帮助下，你可以[在你的应用程序中查找](https://emberjs.com/api/ember/release/classes/ApplicationInstance/methods/lookup?anchor=lookup)任何东西。其次，您可以在测试中访问一些常见的实用函数，`this.get()`和`this.set()`。最后，您可以使用`pauseTest()`来[调试您的测试](https://crunchingnumbers.live/2019/08/04/write-tests-like-a-mathematician-part-1/)。

# 2。渲染测试

## a .定义

渲染测试(集成测试)检查**组件的外观和行为**。Ember CLI 为[组件](https://guides.emberjs.com/release/components/)和[助手](https://guides.emberjs.com/release/templates/writing-helpers/)创建渲染测试。

就性能而言，渲染测试介于单元测试和应用测试之间。

## b .为什么要用它们？

因为你的应用程序是由多个组件组成的，所以你需要在测试之前确保每个组件都是正确的。如果一个组件是可重用的，你要保证它对所有(如果不是，许多)的[参数](https://guides.emberjs.com/release/components/passing-properties-to-a-component/)和[动作](https://guides.emberjs.com/release/components/triggering-changes-with-actions/)的排列都有效。

渲染测试允许您使用 Ember 的渲染引擎测试组件。这意味着，在渲染测试中创建的组件将像在真实应用程序中一样工作。你可以保证组件将遵循它的生命周期钩子。您还可以像最终用户一样与组件进行交互。

## c .例子

考虑一个按钮组件。为简单起见，假设组件跟踪点击次数，并将其显示为标签。(换句话说，这个组件不允许传递参数或动作。)

```
File: /tests/integration/components/simple-button-test.js

import { click, render } from '@ember/test-helpers';
import { hbs } from 'ember-cli-htmlbars';
import { setupRenderingTest } from 'ember-qunit';
import { module, test } from 'qunit';

module('Integration | Component | simple-button', function(hooks) {
    setupRenderingTest(hooks);

    test('should keep track of clicks', async function(assert) {
        await render(hbs`<SimpleButton />`);
        assert.dom('[data-test-label]').hasText('0 clicks');

        await click('[data-test-button]');
        assert.dom('[data-test-label]').hasText('1 click');

        await click('[data-test-button]');
        assert.dom('[data-test-label]').hasText('2 clicks');
    });
}); 
```

注意，我们从 [@ember/test-helpers](https://github.com/emberjs/ember-test-helpers/blob/master/API.md) 导入了`render`和`click`来显示组件并与之交互。我们还从 [ember-cli-htmlbars](https://github.com/ember-cli/ember-cli-htmlbars) 导入了`hbs`来帮助内联模板定义。使用这些方法，我们可以检查单击组件是否正确地向用户更新了它的输出。

下面是更多渲染测试的理想示例:

*   博客文章组件允许两种模式——查看和编辑
*   按钮组件满足各种参数和动作的可访问性
*   导航组件递归地呈现子导航项目
*   使用 [`Intl.NumberFormat`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat) 的助手根据要显示的货币和位数格式化价格

## d .需要注意什么

为了让渲染测试工作，你必须调用 [`setupRenderingTest()`](https://github.com/emberjs/ember-qunit#setup-rendering-tests) 并传递`hooks`对象。

`setupRenderingTest()`是做什么的？第一，它在幕后使用了`setupTest()`。就像在单元测试中一样，您可以访问`this.owner`、`this.get()`、`this.set()`和`pauseTest()`。

另外，`setupRenderingTest()`允许 Ember 的渲染器使用助手进行渲染和 DOM 交互，比如`render`、`click`和`fillIn`。您还可以使用`this.element`来访问从`render`得到的 DOM 元素。

# 3。应用测试

## a .定义

您可以使用应用程序测试(验收测试)来从最终用户的角度验证用户故事和特性。您可以像用户一样与应用程序进行交互——从访问主页，到验证自己的身份，到导航到不同的页面，到填写表单等。

应用程序测试比单元和渲染测试要慢，因为它们创建了 Ember 应用程序的一个实例。

## b .为什么要用它们？

应用程序测试有助于您了解不同组件之间的交互效果。对于嵌套的或者上下文相关的组件，你可以通过渲染测试。然而，如果组件是不相关的，应用程序测试可能是唯一的方法。

您还可以使用应用程序测试来检查路由。用户可以从一个页面导航到另一个页面吗？当页面加载时，他们会看到正确的组件吗？在应用程序测试中很容易检查这些。

最后，如果您的应用程序接收和发送数据，您希望保证您可以成功地采取这些行动。您还想证明您可以正确处理错误状态。应用程序测试是检查这些的好地方，因为你必须像用户一样与应用程序交互。

## c .例子

让我们继续看渲染测试中的博文示例。回想一下，我们的博客文章组件允许两种模式——查看和编辑。下面的测试检查了一种创建博客文章的方法:

```
File: /tests/acceptance/posts-test.js

import { click, currentURL, fillIn, visit } from '@ember/test-helpers';
import { setupApplicationTest } from 'ember-qunit';
import { module, test } from 'qunit';

module('Acceptance | posts', function(hooks) {
    setupApplicationTest(hooks);

    test('The user can create a blog post', async function(assert) {
        await visit('/posts/new');
        await fillIn('[data-test-field="Title"]', 'My New Post');
        await fillIn('[data-test-field="Content"]', 'Lorem ipsum dolor sit amet');
        await click('[data-test-button="Save"]');

        // The user is redirected to their new post
        assert.strictEqual(currentURL(), '/posts/1');
        assert.dom('[data-test-field="Title"]').hasText('My New Post');
        assert.dom('[data-test-field="Content"]').hasText('Lorem ipsum dolor sit amet');
    });
}); 
```

你还能测试什么？

*   用户可以阅读、更新和删除博客文章(可能以批处理方式)
*   用户可以在博客帖子上发表评论
*   用户可以分享博客文章
*   用户应该被授权在博客上采取行动
*   如果有错误，用户会收到反馈

## d .需要注意什么

有几件事需要注意。

首先是应用程序测试运行的时间。对于小应用来说，其影响微乎其微。然而，对于大型应用程序，保持一个短的反馈循环变得至关重要。在这些情况下，如果您可以使用单元测试或渲染测试来验证应用程序中的场景，您可能会考虑使用它们。

其次，您可以使用 Ember CLI 创建一个应用程序测试。因为应用程序测试可以涵盖应用程序中的任何内容，所以您会希望以某种自然的方式组织文件。这将帮助您快速找到测试并防止重复编写。

一种组织方式是模仿`app/routes`的文件夹结构。换句话说，对于每一条路线，您都要创建一个应用程序测试文件。如果这会导致文件过多，您可以为每个父路由创建一个文件。

最后，为了让应用程序测试工作，您必须调用 [`setupApplicationTest()`](https://github.com/emberjs/ember-qunit#setup-application-tests) 并传递`hooks`对象。除了`setupTest()`通常的优点之外，这种方法还创建了一个应用程序实例，这样您就可以从最终用户的角度测试应用程序。它还允许您使用测试助手进行路由和 DOM 交互，比如`currentURL`、`visit`、`click`和`fillIn`。

# 4。摘要

我们了解到，默认情况下，Ember 提供了 3 种类型的测试:单元测试、渲染测试和应用测试。

这些测试的不同之处在于它们集成了应用程序的多少部分来帮助你得出一个合乎逻辑的结论。在一端，单元测试让你独立地检查你的一部分代码。另一方面，应用程序测试让您作为最终用户体验整个应用程序。

一个推论是，这些测试在性能上有所不同。使用的部件越多(越接近真实应用)，测试就越慢。随着你的应用程序变得越来越大，你会想要保持一个健康的单元、渲染和应用程序测试的组合，这样你就可以享受广泛的测试覆盖和短的反馈循环。

下一次，我们将看看编写测试的最佳实践。

# 备注

非常感谢托德·乔丹。他花时间校对了这篇文章，并提供了许多很好的建议。**