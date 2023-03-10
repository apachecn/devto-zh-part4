# AdonisJs 简化了测试

> 原文：<https://dev.to/michi/testing-with-the-adonis-framework-525n>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/testing-made-easy-with-adonisjs)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

Adonis 允许您编写真正干净的测试，是测试驱动开发的一个很好的候选。我将展示如何开始测试，以及一些关于数据库设置的常用技术。你可以在 https://adonisjs.com/docs/4.1/的官方文件中读到更多的东西。

## 设置

```
adonis install @adonisjs/vow 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在`aceProviders`数组中的`start/app.js`下添加 vowProvider。

```
const aceProviders = [
    '@adonisjs/vow/providers/VowProvider',
] 
```

Enter fullscreen mode Exit fullscreen mode

最后，运行`adonis test`来运行所有测试。

## 处理数据库

安装 vow 会创建两个文件`vowfile.js`和`.env.testing`，以及一个示例测试。

### 测试数据库

将数据库设置从您的`.env`复制到`.env.testing`。确保将`DB_DATABASE`换到不同的数据库进行测试。为了让测试运行得更快，选择 sqlite 作为`DB_CONNECTION`(您需要为它安装 npm 依赖 sqlite3)。

### 迁徙

如果您使用迁移，您可以很容易地在运行测试之前填充新的测试数据库，并在运行测试之后重新设置它。

只需进入文件`vowfile.js`并取消迁移所需的所有行的注释。本质上，这就是文件的样子

```
'use strict'

const ace = require('@adonisjs/ace')

module.exports = (cli, runner) => {
  runner.before(async () => {
    use('Adonis/Src/Server').listen(process.env.HOST, process.env.PORT)

    await ace.call('migration:run', {}, { silent: true })
  })

  runner.after(async () => {
    use('Adonis/Src/Server').getInstance().close()

    await ace.call('migration:reset', {}, { silent: true })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

### 每次测试后重置交易

您不希望一个测试意外地依赖于由不同测试插入的数据。为了保持测试简单，最好在每次测试后回滚所有插入的数据。我们可以通过使用 DatabaseTransactions 特征来做到这一点。所有查询都包装在一个自动回滚的事务中。

这里有一个例子:

```
'use strict'

const { test, trait } = use('Test/Suite')('suite name')

trait('DatabaseTransactions')

test('name of test', async ({ assert }) => { }) 
```

Enter fullscreen mode Exit fullscreen mode

### 模型工厂

我们经常依赖数据库中的数据进行测试。尽管总是手动插入数据是非常痛苦的。为了使生活简单，我们可以在`database/factory.js`内部创建[模型工厂](https://adonisjs.com/docs/4.1/seeds-and-factories#_model_factory_api)。下面是一个用户工厂的例子:

```
const Factory = use('Factory')

Factory.blueprint('App/Models/User', (faker, i, data) => {
  return {
    username: faker.username(),
    email: faker.email(),
    password: 'test-password',
    ...data,
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

在测试中，您现在可以轻松地创建用户

```
const Factory = use('Factory')

Factory.model('App/Models/User').create() 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以覆盖工厂数据。

```
const Factory = use('Factory')

Factory.model('App/Models/User').create({ email: 'email@test.com' }) 
```

Enter fullscreen mode Exit fullscreen mode

## 例子

### 浏览器测试&伪造邮件

```
'use strict'

const { test, trait } = use('Test/Suite')('ForgotPasswordController')
const Factory = use('Factory')
const Mail = use('Mail')

trait('Test/ApiClient')
trait('DatabaseTransactions')

test('sends forgot password email to user', async ({ assert, client }) => {
  Mail.fake()
  const user = await Factory.model('App/Models/User').create()

  await client.post('/password/forgot').send({ email: user.email }).end()
  const mail = Mail.pullRecent()
  assert.equal(mail.message.to[0].address, user.email)
  assert.equal(mail.message.subject, 'Password Reset')

  Mail.restore()
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 检查控制器响应

```
test('resets password with correct token', async ({ assert, client }) => {
  const user = await Factory.model('App/Models/User').create()
  const token = await (new TokenService).generateToken(user.email)

  const response = await client.post('/password/reset').send({ email: user.email, token, password: 'new password' }).end()
  await user.reload()

  response.assertStatus(200)
  response.assertJSON({ message: 'Password reset successful.' })
  assert.isTrue(await user.verify('new password'))
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这应该给你一个关于如何开始使用 adonis 框架进行测试的好主意。请务必阅读官方文件以获取更多信息。

> 点击这里阅读更多关于在 ioc 容器中伪造邮件、事件和依赖关系的信息:[https://adonisjs.com/docs/4.1/testing-fakes](https://adonisjs.com/docs/4.1/testing-fakes)
> 
> 在这里阅读更多关于 http 测试、不同的请求类型、认证、会话和断言类型:[https://adonisjs.com/docs/4.1/api-tests](https://adonisjs.com/docs/4.1/api-tests)

祝测试和测试愉快！

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。