# 使用 AdonisJs - 2 的 TDD 课程。我们的第一个测试

> 原文：<https://dev.to/michi/tdd-course-with-adonisjs-2-our-first-test-34j7>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/tdd-with-adonisjs-2)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

你可以在这里找到这篇博文的所有改动:[https://github . com/MZanggl/TDD-adonisjs/commit/87 bcda 4823 c 556 c 7717 a 31 ad 977457050684 bbcf](https://github.com/MZanggl/tdd-adonisjs/commit/87bcda4823c556c7717a31ad977457050684bbcf)

让我们从创建第一个真正的测试开始。我们专注于应用程序提供的核心部分，线程。如果你考虑一下，为了创建线程，我们需要一个用户来创建线程，为此我们需要实现注册和认证。您可能认为按照这种逻辑，注册和认证应该是我们实现的第一件事。然而，用户注册和认证不是我们应用程序的核心部分，所以我们现在不必关心这些部分。相反，让我们从一个特性开始。(Protip:设计 UI 的时候也这样做，不需要先创建导航条和页脚)

第一个测试是最难的，因为它需要一些额外的设置，比如建立数据库连接。

让我们创建一个测试来创建线程，我们可以从命令行轻松完成:

```
adonis make:test Thread 
```

Enter fullscreen mode Exit fullscreen mode

并选择`functional`。

您可以用下面的
替换新创建文件的内容

```
'use strict'

const { test, trait } = use('Test/Suite')('Thread')

trait('Test/ApiClient')

test('can create threads', async ({ client }) => {

}) 
```

Enter fullscreen mode Exit fullscreen mode

我们正在加载“apiClient”特征，它将为我们提供用于 api 请求的`client`变量，以测试我们的端点。

好吧，让我们在测试中加入一些逻辑。我们现在保持简单，用标题和正文发送到线程端点应该返回响应代码 200。很公平。

```
test('can create threads', async ({ client }) => {
  const response = await client.post('/threads').send({
    title: 'test title',
    body: 'body',
  }).end()

  response.assertStatus(200)
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们运行测试套件，看看发生了什么。

我们得到的误差是

```
1\. can create threads
  expected 404 to equal 200
  404 => 200 
```

Enter fullscreen mode Exit fullscreen mode

当然啦！毕竟我们还没有创建任何路由或控制器。尽管如此，我们运行测试**，让它指导我们**下一步是什么。这种方法的伟大之处在于它阻止了我们对事物的过度设计。我们尽最大努力通过测试。一旦测试通过，我们就重构。

因此，让我们前往`start/routes.js`并添加以下路线

```
Route.post('threads', 'ThreadController.store') 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，您可能倾向于添加一个路由组或使用资源路由，但是同样，保持简单，尽可能简单。一旦测试通过，我们可以重构一些更好的东西。

再次运行测试将返回不同的错误！

```
 1. can create threads
  expected 500 to equal 200
  500 => 200 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在测试中记录响应错误，看看哪里出错了。为了更健壮，你可以[扩展异常处理器](https://adonisjs.com/docs/4.1/exceptions)。

```
// ...

console.log(response.error)
response.assertStatus(200) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们确定误差是

```
'Error: Cannot find module 'app/Controllers/Http/ThreadController' 
```

Enter fullscreen mode Exit fullscreen mode

所以这就是我们的下一步！

使用`adonis make:controller ThreadController`创建控制器，并选择`for HTTP requests`。

运行测试，错误更改为`RuntimeException: E_UNDEFINED_METHOD: Method store missing on ...`。

因此，让我们在控制器上创建“store”方法，现在只让它返回一个空对象。

```
'use strict'

class ThreadController {
    async store({ response }) {
        return response.json({ })
    }
}

module.exports = ThreadController 
```

Enter fullscreen mode Exit fullscreen mode

再次运行测试套件将使测试通过！

但是显然我们还没有完全完成。因此，让我们扩展我们的测试，以确认我们确实将线程保存到了数据库中。

首先，让我们在测试文件的顶部导入`Thread`模型。

```
const Thread = use('App/Models/Thread') 
```

Enter fullscreen mode Exit fullscreen mode

是的，是的，这个文件还不存在，但是我们将假设它存在，并让测试为下一步指明方向。

在测试中，我们将从数据库中获取第一个线程，并断言它匹配 JSON 响应。

```
test('can create threads', async ({ client }) => {
  const response = await client.post('/threads').send({
    title: 'test title',
    body: 'body',
  }).end()
  console.log(response.error)
  response.assertStatus(200)
  const thread = await Thread.firstOrFail()
  response.assertJSON({ thread: thread.toJSON() })
}) 
```

Enter fullscreen mode Exit fullscreen mode

运行测试会返回错误`Error: Cannot find module 'app/Models/Thread'`。所以让我们来创造它吧！

```
adonis make:model Thread -m 
```

Enter fullscreen mode Exit fullscreen mode

`-m`也将方便地创建一个迁移文件。Adonis 利用迁移来创建和修改数据库模式。不需要在数据库中手动创建表。这提供了几个好处，比如版本控制，或者在我们的测试中使用这些迁移文件！

再次运行测试揭示了与数据库相关的下一步。

```
Knex: run
$ npm install sqlite3 --save
Error: Cannot find module 'sqlite3' 
```

Enter fullscreen mode Exit fullscreen mode

如果你没看过`.env.testing`，这是用于测试的环境。默认情况下，它使用 sqlite。即使您计划在实际开发中使用不同的数据库(如 mysql)，使用 sqlite 进行测试也是一个不错的选择，因为它可以让您的测试速度更快。

这一步可能会让一些人感到惊讶。不，我们没有模仿数据库层，相反，我们有一个测试数据库，我们可以迁移和重置。使用 sqlite，它的重量非常轻。我们需要模仿的越少，我们的测试实际测试的就越多。阿多尼斯让它变得轻而易举。

因此，让我们像错误消息建议的那样安装 sqlite。

```
npm install sqlite3 --save 
```

Enter fullscreen mode Exit fullscreen mode

再次运行测试向我们展示了`Error: SQLITE_ERROR: no such table: threads`。是的，我们还没有创建表，但是我们有一个线程的迁移文件。我们要做的是告诉 vow 在测试开始时运行我们的所有迁移，并在测试结束时回滚所有内容。

我们在`vowfile.js`中这样做。一切都已经存在了，事实上，我们只需要取消一些行的注释。

```
14 -> const ace = require('@adonisjs/ace')
37 -> await ace.call('migration:run', {}, { silent: true })
60 -> await ace.call('migration:reset', {}, { silent: true }) 
```

Enter fullscreen mode Exit fullscreen mode

再次运行测试会显示下一个错误`ModelNotFoundException: E_MISSING_DATABASE_ROW: Cannot find database row for Thread model`。

有道理，因为现在，控制器没有将线程插入数据库。

所以让我们来看看控制器和那部分。

```
'use strict'

const Thread = use('App/Models/Thread')

class ThreadController {
    async store({ request, response }) {
        const thread = await Thread.create(request.only(['title', 'body']))
        return response.json({ thread })
    }
}

module.exports = ThreadController 
```

Enter fullscreen mode Exit fullscreen mode

运行测试现在将返回另一个与插入相关的错误。

```
'Error: insert into `threads` (`body`, `created_at`, `title`, `updated_at`) values (\'body\', \'2019-09-01 12:51:02\', \'test title\', \'2019-09-01 12:51:02\') - SQLITE_ERROR: table threads has no column named body', 
```

Enter fullscreen mode Exit fullscreen mode

该表当前不包含任何名为 body 的列。

解决方案是将新列添加到以`_thread_schema.js`结尾的迁移文件的`up`方法中。

```
 this.create('threads', (table) => {
      table.increments()
      table.text('body')
      table.timestamps()
    }) 
```

Enter fullscreen mode Exit fullscreen mode

运行测试将返回关于列`title`的非常相似的错误。因此，让我们也将它添加到迁移文件中。

```
 this.create('threads', (table) => {
      table.increments()
      table.string('title')
      table.text('body')
      table.timestamps()
    }) 
```

Enter fullscreen mode Exit fullscreen mode

在你知道它之前，测试是绿色的！

现在，如果您试图在开发期间到达实际的端点，它会抱怨“线程”表不存在，这是因为您必须使用`adonis migration:run`自己为您的开发/生产环境运行迁移。

# 重构

TDD 包括三个阶段，红绿重构。因此，让我们重构应用程序和测试，并确保一切仍然是绿色的。这就是 TDD 的美妙之处，它给你重构的信心，从而使它变得安全、简单和有趣。

让我们首先去掉测试中的 console.log。我们不再需要它了。
接下来，我很自信我想[保持控制器足智多谋](https://adonisjs.com/docs/4.1/routing#_route_resources)，这意味着它只有默认的 CRUD 动作。所以让我们去`routes.js`换下

```
Route.post('threads', 'ThreadController.store') 
```

Enter fullscreen mode Exit fullscreen mode

用

```
Route.resource('threads', 'ThreadController').only(['store']) 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上没有必要，但是我想展示的是，您现在可以再次运行测试，并确认您的重构没有导致任何副作用。那就是自信！

* * *

# 总结

我们进行了第一次测试！下一次，我们将看看如何解决一个问题，这个问题是由意外使用来自其他测试和模型工厂的插入数据的测试引起的！