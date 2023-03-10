# 使用 AdonisJs - 4 的 TDD 课程。使用授权中间件

> 原文：<https://dev.to/michi/tdd-course-with-adonisjs-4-using-the-auth-middleware-2kdi>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/tdd-with-adonisjs-4)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

我们的路由目前可以被未经身份验证的用户访问，所以让我们编写一个新的测试来确认这一点！

像往常一样，你可以在 GitHub 上的以下 commit 中找到我们在这里所做的所有更改:[https://GitHub . com/MZanggl/TDD-adonisjs/commit/6f 50 e 5f 277674 dfe 460 b 692 ced c28 d5a 67 D1 cc 55](https://github.com/MZanggl/tdd-adonisjs/commit/6f50e5f277674dfe460b692cedc28d5a67d1cc55)

```
// test/functional/thread.spec.js

test('unauthenticated user cannot create threads', async ({ client }) => {
  const response = await client.post('/threads').send({
    title: 'test title',
    body: 'body',
  }).end()

  response.assertStatus(401)
}) 
```

Enter fullscreen mode Exit fullscreen mode

测试失败，因为响应代码仍然是 200。因此，让我们将集成的身份验证中间件添加到我们的路由中。

```
// start/routes.js

Route.resource('threads', 'ThreadController').only(['store', 'destroy']).middleware('auth') 
```

Enter fullscreen mode Exit fullscreen mode

这使得测试通过，但同时，我们中断了其他测试，因为它们现在也返回了状态代码 401(未经身份验证)。为了让它们再次通过，我们需要能够在测试中对用户进行认证。

首先，让我们为用户创建一个模型工厂，就像我们创建线程一样。

回到`database/factory.js`，为用户添加以下蓝图。

```
Factory.blueprint('App/Models/User', (faker) => {
  return {
    username: faker.username(),
    email: faker.email(),
    password: '123456',
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 functional thread.spec.js 测试中试试吧！我们可以使用`loginVia`方法“登录”。

```
test('can create threads', async ({ client }) => {
  const user = await Factory.model('App/Models/User').create()
  const response = await client.post('/threads').loginVia(user).send({
    title: 'test title',
    body: 'body',
  }).end()

  response.assertStatus(200)

  const thread = await Thread.firstOrFail()
  response.assertJSON({ thread: thread.toJSON() })
}) 
```

Enter fullscreen mode Exit fullscreen mode

但是，此操作失败，并出现错误`...loginVia is not a function`。像以前一样，特征可以帮助我们解决这个问题，所以让我们将`trait('Auth/Client')`添加到文件的顶部，并再次运行测试。

太棒了。让我们对现有的失败的删除测试应用相同的修复。

```
test('can delete threads', async ({ assert, client }) => {
  const user = await Factory.model('App/Models/User').create()
  const thread = await Factory.model('App/Models/Thread').create()
  const response = await client.delete(thread.url()).send().loginVia(user).end()
  response.assertStatus(204)

  assert.equal(await Thread.getCount(), 0)
}) 
```

Enter fullscreen mode Exit fullscreen mode

当然，任何用户都可以删除任何线程并不是最佳选择，但我们正在努力实现...

我认为是时候将测试用例重新命名为更有意义的东西了。

> test('可以创建线程')=> test('授权用户可以创建线程')
> 
> test('可以删除线程')=> test('授权用户可以删除线程')

* * *

完成后，将 user_id 列添加到 threads 表中是有意义的。

为此，我们首先要重构我们的测试用例“授权用户可以创建线程”。我们目前实际上没有测试标题和正文是否被正确插入，我们只是断言响应与数据库中找到的第一个线程匹配。所以让我们把那部分也加上

```
test('authorized user can create threads', async ({ client }) => {
  const user = await Factory.model('App/Models/User').create()
  const attributes = {
    title: 'test title',
    body: 'body',
  }

  const response = await client.post('/threads').loginVia(user).send(attributes).end()
  response.assertStatus(200)

  const thread = await Thread.firstOrFail()
  response.assertJSON({ thread: thread.toJSON() })
  response.assertJSONSubset({ thread: attributes })
}) 
```

Enter fullscreen mode Exit fullscreen mode

测试应该仍然通过，但是让我们继续将 user_id 添加到我们添加的断言

```
response.assertJSONSubset({ thread: {...attributes, user_id: user.id} }) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在收到错误

```
expected { Object (thread) } to contain subset { Object (thread) }
  {
    thread: {
    - created_at: "2019-09-08 08:57:59"
    - id: 1
    - updated_at: "2019-09-08 08:57:59"
    + user_id: 1
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们来看看 ThreadController，用这个
替换掉“store”方法

```
async store({ request, auth, response }) {
    const attributes = { ...request.only(['title', 'body']), user_id: auth.user.id }
    const thread = await Thread.create(attributes)
    return response.json({ thread })
    } 
```

Enter fullscreen mode Exit fullscreen mode

不要担心，我们会在测试通过后进行重构。

测试现在将在断言`response.assertStatus(200)`处失败，错误代码为 500，所以让我们在前面的行中添加`console.log(response.error)`。它将显示我们的表缺少列`user_id`。

转到 threads 迁移文件，在 body 之后，像这样添加 user_id 列

```
table.integer('user_id').unsigned().notNullable() 
```

Enter fullscreen mode Exit fullscreen mode

让我们也用一个外键注册这个新列。我喜欢在所有列声明之后保留外键。

```
// ... column declarations

table.foreign('user_id').references('id').inTable('users') 
```

Enter fullscreen mode Exit fullscreen mode

太好了，这个测试又通过了！

但结果是我们打破了另外两个测试！

由于`SQLITE_CONSTRAINT: NOT NULL constraint failed: threads.user_id`，我们的单元测试“可以访问 url”和功能测试“授权用户可以删除线程”现在都失败了。

两个测试都使用了我们的线程模型工厂，当然我们还没有用用户 id 更新它。因此，让我们转到`database/factory.js`并将 user_id 添加到线程工厂，如下所示:

```
return {
    title: faker.word(),
    body: faker.paragraph(),
    user_id: (await Factory.model('App/Models/User').create()).id
  } 
```

Enter fullscreen mode Exit fullscreen mode

请务必将该函数转换成一个`async`函数，因为我们在这里必须使用 await。

如果我们再次运行我们的测试套件，我们应该得到绿色！

# 重构

让我们转向 ThreadController，为这一部分想一个更面向对象的方法:

```
const attributes = { ...request.only(['title', 'body']), user_id: auth.user.id }
const thread = await Thread.create(attributes) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不用自己定义关系就好了。我们可以用这个
交换这两条线

```
const thread = await auth.user.threads().create(request.only(['title', 'body'])) 
```

Enter fullscreen mode Exit fullscreen mode

由于我们还没有定义关系，所以会得到错误`TypeError: auth.user.threads is not a function`。

所以我们要做的就是转到“App/Models/User.js”并添加关系

```
threads() {
    return this.hasMany('App/Models/Thread')
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，一个坚实的重构！

让我们快速添加另一个测试来确保未经认证的用户不能删除线程

```
test('unauthenticated user can not delete threads', async ({ assert, client }) => {
  const thread = await Factory.model('App/Models/Thread').create()
  const response = await client.delete(thread.url()).send().end()
  response.assertStatus(401)
}) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们必须在这里添加更多的测试，不是每个用户都可以简单地删除任何线程。下一次，让我们测试并创建一个策略来解决这个问题！