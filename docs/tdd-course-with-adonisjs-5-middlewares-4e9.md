# 使用 AdonisJs - 5 的 TDD 课程。中间件

> 原文：<https://dev.to/michi/tdd-course-with-adonisjs-5-middlewares-4e9>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/tdd-with-adonisjs-5)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

在上一集中，我们向 cruddy ThreadController 添加了授权。然而，仅仅因为用户通过了身份验证，并不意味着他有权删除任何线程。这应该仅限于版主和创建线程的用户。

和往常一样，你可以在这次提交中找到所有的改动:[https://github . com/MZanggl/TDD-adonisjs/commit/d 845 ed 83700210 ac1b 520 a 25 c 702373 df 0782 b 69](https://github.com/MZanggl/tdd-adonisjs/commit/d845ed83700210ac1b520a25c702373df0782b69)

在我们开始测试中间件之前，让我们记住尽可能保持简单。让我们在控制器中添加授权逻辑。因此，让我们用下面的测试来扩展我们的功能性`thread.spec.js`文件:

```
test('thread can not be deleted by a user who did not create it', async ({ client }) => {
  const thread = await Factory.model('App/Models/Thread').create()
  const notOwner = await Factory.model('App/Models/User').create()
  const response = await client.delete(thread.url()).send().loginVia(notOwner).end()
  response.assertStatus(403)
}) 
```

Enter fullscreen mode Exit fullscreen mode

记住，线程工厂现在也在创建一个用户，因为它依赖于它。

测试失败，出现错误

```
expected 204 to equal 403
  204 => 403 
```

Enter fullscreen mode Exit fullscreen mode

让我们进入 ThreadController 并在那里添加授权逻辑:

```
async destroy({ params, auth, response }) {
    const thread = await Thread.findOrFail(params.id)

    if (thread.user_id !== auth.user.id) {
        return response.status(403).send()
    }

    await thread.delete()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在测试通过了。然而，我们打破了“授权用户可以删除线程”的测试，因为它现在返回 403，尽管我们期望它返回 204。

这很有道理。如果我们看一下测试，我们不是使用线程的所有者，而是使用一个新用户进行身份验证。让我们把它修好。

我们可以替换

```
const user = await Factory.model('App/Models/User').create()
const thread = await Factory.model('App/Models/Thread').create()
const response = await client.delete(thread.url()).send().loginVia(user).end() 
```

Enter fullscreen mode Exit fullscreen mode

用

```
const thread = await Factory.model('App/Models/Thread').create()
const response = await client.delete(thread.url()).send().loginVia(await thread.user().first()).end() 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们将从线程实例中获取用户。由于我们没有定义这种关系(只有反过来)，我们将收到错误`thread.user is not a function`。所以我们把关系加到“App/Models/Thread.js”里。

```
user() {
    return this.belongsTo('App/Models/User')
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，测试是绿色的。

* * *

让我们快速重构一下。在 ThreadController 中，我们添加了`return response.status(403).send()`。简单地用`return response.forbidden()`替换那个位，你仍然应该得到绿色！

在我们将授权逻辑抽象成策略之前，让我们首先使它值得这样做。我的意思是，在我们抽象事物之前，让我们首先创建一些复制，还有什么比更新线程更适合这个呢！

```
test('authorized user can update title and body of threads', async ({ assert, client }) => {
  const thread = await Factory.model('App/Models/Thread').create()
  const attributes = { title: 'new title', body: 'new body' }
  const updatedThreadAttributes = { ...thread.toJSON(), ...attributes }

  const response = await client.put(thread.url()).loginVia(await thread.user().first()).send(attributes).end()
  await thread.reload()

  response.assertStatus(200)
  response.assertJSON({ thread: thread.toJSON() })
  assert.deepEqual(thread.toJSON(), updatedThreadAttributes)
}) 
```

Enter fullscreen mode Exit fullscreen mode

因此，首先我们创建一个线程，定义我们想要更新的所有属性，然后将两者合并在一起，创建一个应该如何更新线程的图像。然后我们发送请求并刷新我们的线程模型。

最后，我们断言响应状态和文本，并检查属性是否相应地更新了。

运行测试套件的结果是 404，所以让我们将它添加到`start/routes.js`中。

```
Route.resource('threads', 'ThreadController').only(['store', 'destroy', 'update']).middleware('auth') 
```

Enter fullscreen mode Exit fullscreen mode

此时，您应该已经熟悉了这种模式。您得到一个 500，所以在我们发出请求后，立即在单元测试中添加`console.log(response.error)`。这应该记录`RuntimeException: E_UNDEFINED_METHOD: Method update missing on App/Controllers/Http/ThreadController`。

是时候将该方法添加到我们的 ThreadController 中了

```
async update({ response }) {
    return response.json({ })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在得到了错误`expected {} to deeply equal { Object (thread) }`。

所以是时候认真对待更新方法了，这里是完整的代码

```
async update({ request, params, response }) {
    const thread = await Thread.findOrFail(params.id)
    thread.merge(request.only(['title', 'body']))
    await thread.save()
    return response.json({ thread })
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得测试通过。

让我们添加一个测试来确认应用了授权中间件

```
test('unauthenticated user cannot update threads', async ({ assert, client }) => {
  const thread = await Factory.model('App/Models/Thread').create()
  const response = await client.put(thread.url()).send().end()
  response.assertStatus(401)
}) 
```

Enter fullscreen mode Exit fullscreen mode

传球！

以及检查只有线程的所有者才能真正更新它的测试。

```
test('thread can not be updated by a user who did not create it', async ({ client }) => {
  const thread = await Factory.model('App/Models/Thread').create()
  const notOwner = await Factory.model('App/Models/User').create()
  const response = await client.put(thread.url()).send().loginVia(notOwner).end()
  response.assertStatus(403)
}) 
```

Enter fullscreen mode Exit fullscreen mode

失败:/

很好，那么让我们从 destroy 方法中复制授权逻辑。

```
async update({ request, auth, params, response }) {
    const thread = await Thread.findOrFail(params.id)
    if (thread.user_id !== auth.user.id) {
        return response.forbidden()
    }

    thread.merge(request.only(['title', 'body']))
    await thread.save()
    return response.json({ thread })
} 
```

Enter fullscreen mode Exit fullscreen mode

测试通过了，但是现在我们已经创建了副本。是时候创建一个策略了！为此，我们将离开我们的特性测试，分解成一个单元测试。现在 Adonis 没有策略的概念，所以我们将为此使用一个中间件，因此标题为“测试中间件”。

首先，让我们为不存在的中间件创建一个新的单元测试。

```
adonis make:test ModifyThreadPolicy 
```

Enter fullscreen mode Exit fullscreen mode

并选择“单元测试”。

现在，用下面的测试用例
替换示例测试

```
test('non creator of a thread cannot modify it', async ({ assert, client }) => {

}) 
```

Enter fullscreen mode Exit fullscreen mode

太好了。那么测试中间件的最好方法是什么呢？嗯，我们可以简单地导入“Route”并动态地创建一个只在测试期间有效的路由。

让我们这样做，并在以后引入我们需要的所有特征和模块。

```
'use strict'

const { test, trait } = use('Test/Suite')('Modify Thread Policy')

const Route = use('Route')
const Factory = use('Factory')

trait('Test/ApiClient')
trait('Auth/Client')
trait('DatabaseTransactions')

test('non creator of a thread cannot modify it', async ({ assert, client }) => {
  const action = ({ response }) => response.json({ ok: true })
  Route.post('test/modify-thread-policy/:id', action).middleware(['auth', 'modifyThreadPolicy'])
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了路由，让我们发送一个请求并做一些断言！

```
 // ...
  const thread = await Factory.model('App/Models/Thread').create()
  const notOwner = await Factory.model('App/Models/User').create()
  let response = await client.post(`test/modify-thread-policy/${thread.id}`).loginVia(notOwner).send().end()
  console.log(response.error)
  response.assertStatus(403) 
```

Enter fullscreen mode Exit fullscreen mode

运行测试应该会抛出错误`RuntimeException: E_MISSING_NAMED_MIDDLEWARE: Cannot find any named middleware for {modifyThreadPolicy}. Make sure you have registered it inside start/kernel.js file.`。

所以我们按照它说的做，在“start/kernel.js”的`namedMiddleware`数组中添加下面一行。

```
 modifyThreadPolicy: 'App/Middleware/ModifyThreadPolicy' 
```

Enter fullscreen mode Exit fullscreen mode

现在运行测试会返回 Adonis 找不到模块的错误。

让我们通过运行
来创建策略

```
adonis make:middleware ModifyThreadPolicy 
```

Enter fullscreen mode Exit fullscreen mode

并选择“对于 HTTP 请求”。

让我们再做一次测试。因为我们没有向中间件添加任何逻辑，所以它不会做任何事情，也不会将请求转发给 action，后者返回状态代码 200。

```
expected 200 to equal 403
  200 => 403 
```

Enter fullscreen mode Exit fullscreen mode

既然控制器中已经有了我们需要的逻辑，那么让我们继续将它复制到中间件中。

总之，我们的中间件看起来像这样

```
'use strict'

const Thread = use('App/Models/Thread')

class ModifyThreadPolicy {
  async handle ({ params, auth, response }, next) {
    const thread = await Thread.findOrFail(params.id)
    if (thread.user_id !== auth.user.id) {
      return response.forbidden()
    }

    await next()
  }
}

module.exports = ModifyThreadPolicy 
```

Enter fullscreen mode Exit fullscreen mode

它过去了！

让我们在“modify-thread-policy.spec.js”中添加另一个单元测试来测试快乐路径。

```
test('creator of a thread can modify it', async ({ assert, client }) => {
  const action = ({ response }) => response.json({ ok: true })
  Route.post('test/modify-thread-policy/:id', action).middleware(['auth', 'modifyThreadPolicy'])

  const thread = await Factory.model('App/Models/Thread').create()
  let response = await client.post(`test/modify-thread-policy/${thread.id}`).loginVia(await thread.user().first()).send().end()
  response.assertStatus(200)
}) 
```

Enter fullscreen mode Exit fullscreen mode

为了避免创建路线两次，让我们向测试文件添加一个`before`部分。

将其导入到文件的顶部，如下所示: `const { test, trait, before } = use('Test/Suite')('Modify Thread Policy')`，从每个测试中删除路由创建逻辑，并将以下代码放在测试之前:

```
before(() => {
  const action = ({ response }) => response.json({ ok: true })
  Route.post('test/modify-thread-policy/:id', action).middleware(['auth', 'modifyThreadPolicy'])
}) 
```

Enter fullscreen mode Exit fullscreen mode

好了，有了我们的单元测试，让我们回到我们的功能测试。从 ThreadController 的销毁和更新方法中删除授权检查。

```
// delete this

if (thread.user_id !== auth.user.id) {
    return response.forbidden()
} 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，这两项测试现在都失败了

```
1\. thread can not be deleted by a user who did not create it
  expected 204 to equal 403
  204 => 403

  2\. thread can not be updated by a user who did not create it
  expected 200 to equal 403
  200 => 403 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们前往`start/routes.js`，将我们创建的中间件添加到更新和销毁路径中。

```
Route.resource('threads', 'ThreadController').only(['store', 'destroy', 'update']).middleware(new Map([
    [['store', 'destroy', 'update'], ['auth']],
    [['destroy', 'update'], ['modifyThreadPolicy']]
])) 
```

Enter fullscreen mode Exit fullscreen mode

测试再次通过！

因为我们已经检查了中间件中线程的存在，所以我们可以重构我们的 ThreadController 的 destroy 方法来简单地执行

```
async destroy({ params }) {
    await Thread.query().where('id', params.id).delete()
} 
```

Enter fullscreen mode Exit fullscreen mode

这一集就到这里了！下一次让我们来看看验证，因为我们目前可以插入一个空的标题和正文。