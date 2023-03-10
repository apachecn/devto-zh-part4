# 使用 AdonisJs - 3 的 TDD 课程。模型工厂和数据库事务

> 原文：<https://dev.to/michi/tdd-course-with-adonisjs-3-model-factories-db-transactions-9cg>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/tdd-with-adonisjs-3)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

欢迎回来！让我们进入第二个测试，删除线程！

你可以在这里找到我们在这篇文章中所做的所有修改:[https://github . com/MZanggl/TDD-adonisjs/commit/95a 52 a 79 de 271 c 126 a1e 0 A8 e 087 FB 87d 040555](https://github.com/MZanggl/tdd-adonisjs/commit/95a52a79de271c126a3a1e0a8e087fb87d040555)

现在为了删除一个线程，我们首先要创建一个线程。现在让我们在测试中手动完成，但是最后，我们将再次重构它！

在`thread.spec.js`内添加一个新的测试

```
test('can delete threads', async ({ assert, client }) => {
  const thread = await Thread.create({
    title: 'test title',
    body: 'test body',
  })

  const response = await client.delete(`threads/${thread.id}`).send().end()
  console.log(response.error)
  response.assertStatus(204)
}) 
```

Enter fullscreen mode Exit fullscreen mode

运行它！我们收到 404，因为我们还没有创建路由，所以让我们将它添加到`routes.js`中的资源路由中。按照惯例，删除实体的动作是`destroy`。

```
// start/routes.js

Route.resource('threads', 'ThreadController').only(['store', 'destroy']) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在得到了错误`RuntimeException: E_UNDEFINED_METHOD: Method destroy missing`，所以让我们在 ThreadController 中创建方法。

```
async destroy({ params }) {
    const thread = await Thread.findOrFail(params.id)
    await thread.delete()
} 
```

Enter fullscreen mode Exit fullscreen mode

测试通过！但是现在让我们确定它确实已经从数据库中删除了。前往测试，并在测试结束时添加以下检查。

```
assert.equal(await Thread.getCount(), 0) 
```

Enter fullscreen mode Exit fullscreen mode

哎呦！

```
1\. can delete threads
  expected 1 to equal 0
  1 => 0 
```

Enter fullscreen mode Exit fullscreen mode

怎么会这样，我们正在删除它对吗？

让我们试着只运行“可以删除线程”测试，看看会发生什么

```
npm t -- -g 'can delete threads' 
```

Enter fullscreen mode Exit fullscreen mode

或者可选地

```
adonis test -g 'can delete threads' 
```

Enter fullscreen mode Exit fullscreen mode

会过去的，对吧？

这是有意义的，因为我们从未从第一次测试中删除插入的线程。为了解决这个问题，我们只需在测试的顶部加载另一个特征。

```
trait('DatabaseTransactions') 
```

Enter fullscreen mode Exit fullscreen mode

这将把所有查询包装在一个事务中，该事务在每次测试后被回滚，因此当我们的第二次测试运行时，来自第一次测试的线程被长时间回滚。试一试测试套件！

# 重构

好吧，我们的测试中有很多需要重构的地方。

我们先来看这几行

```
const thread = await Thread.create({
    title: 'test title',
    body: 'test body',
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要的测试越多，这就变得越乏味。幸运的是，Adonis 允许创建模型工厂。为此，转到`database/factory.js`并添加以下代码。

```
Factory.blueprint('App/Models/Thread', (faker) => {
  return {
    title: faker.word(),
    body: faker.paragraph(),
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

另外，取消文件顶部的`const Factory = use('Factory')`注释。

`faker`是[https://chancejs.com](https://chancejs.com)的一个实例，看看他们的文档中所有你可以伪造的东西。

现在回到我们的测试中，我们可以用简单的
来代替手动创建线程

```
const thread = await Factory.model('App/Models/Thread').create() 
```

Enter fullscreen mode Exit fullscreen mode

另外，将`const Factory = use('Factory')`添加到测试的顶部。

运行测试，你仍然应该得到绿色！

* * *

还有一种更好的方式

```
const response = await client.delete(`threads/${thread.id}`).send().end() 
```

Enter fullscreen mode Exit fullscreen mode

特别是`threads/${thread.id}`。
如果我们能做`const response = await client.delete(thread.url()).send().end()`就更好了，事实上，我们就这么做并运行测试吧。它会抱怨`thread.url`不是一个函数。

为此，我们必须将方法`url`添加到我们的线程模型中。但是目前，我们正在进行整合测试。那么我们如何用 TDD 的方式来做这件事呢？

解决方案是将特性测试分解成线程模型的单元测试。

让我们使用`adonis make:test Thread`创建一个测试，这次选择 unit。

这就是单元测试的样子

```
'use strict'

const { test, trait } = use('Test/Suite')('Thread')
const Factory = use('Factory')

trait('DatabaseTransactions')

test('can access url', async ({ assert }) => {
  const thread = await Factory.model('App/Models/Thread').create()
  assert.equal(thread.url(), `threads/${thread.id}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

漂亮地抛出同样的错误`TypeError: thread.url is not a function`。
记得我说过 TDD 遵循红色- >绿色- >重构的概念。我之前没有提到，但我们刚刚了解到，这三个步骤是循环进行的！

转到`app/Models/Thread.js`并将下面的方法添加到线程类

```
url() {
    return `threads/${this.id}`
} 
```

Enter fullscreen mode Exit fullscreen mode

再次运行测试，这一次单元和功能测试都应该是绿色的！

现在我们已经可以创建和删除线程了，但是到目前为止，即使是来宾也可以执行这些操作。下一次，让我们看看如何将这些操作仅限于经过身份验证的用户，并将 user_id 字段添加到我们的 threads 表中。