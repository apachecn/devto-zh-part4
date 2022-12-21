# 如何用单元测试覆盖 Django 应用程序

> 原文：<https://dev.to/mkdev/how-to-cover-django-application-with-unit-tests-4ei3>

> 注意:本教程基于在[如何用 Django](https://mkdev.me/en/posts/how-to-create-an-api-with-django) 创建 API 中开发的技巧和代码。我们将在 [GitHub](https://github.com/philipkiely/django_taskmaster_tested) 上使用这个版本，使用相同的待办事项 API。

去年夏天，我和几个朋友一起开发 Django web 应用程序。有一次，我完成了一个功能，并把它发给另一个人去测试。他回答说他不能测试新功能，因为我无意中破坏了登录功能。问题是，这种情况不只发生一次，每周都会发生。潜在的问题是我们的工作流程没有包括足够的自动化测试。

### 什么是自动化测试？

测试代码是编程的重要部分。根据您的项目，您可以通过查看网页、玩视频游戏或分析输出日志来测试您的代码。手工测试是时间密集型的，并且会留下出错的空间，所以大多数专业程序员都非常重视自动化测试。今天，我们将回顾一种常见的自动化测试，单元测试，来考虑自动化测试如何在开发过程中帮助我们。首先，我们将在现有 API 的模型和视图上编写测试，然后通过添加新特性来实践测试驱动开发。

自动化测试节省了时间并提高了软件质量。手动测试在开始时很快:只需运行代码，看看它是否工作。然而，随着时间的推移，您会在应用程序中开发越来越多的功能，这使得手动测试变得非常耗时。此外，您可能会忘记测试某个功能。自动化测试的正确实现涵盖了每一件事，每次都在几秒钟内完成。拥有良好的自动化测试也使其他人更容易理解你的代码，并使团队能够一起构建东西，而不用担心破坏彼此的特性。

单元测试单独验证组件的功能。它是最低级别的测试；它确保程序的每一个方面都能单独正常工作(集成和系统测试一起验证组件及其交互，但这超出了本教程的范围)。例如，在面向对象的程序中，您可以为每个对象编写单元测试，并根据复杂性为各个方法编写单元测试。在 Django 中，我们对每个模型和视图进行单元测试。

### Django 中的单元测试是如何工作的？

按照本教程，从 [GitHub](https://github.com/philipkiely/django_taskmaster_tested) 中克隆项目。按照与[如何用 Django](https://mkdev.me/en/posts/how-to-create-an-api-with-django) 创建 API 的前三段相同的步骤来设置项目。

当你使用`python manage.py startapp appname`创建一个 Django 应用时，Django 在`appname`目录中创建的一个文件叫做`tests.py`。这个文件用来存放应用程序中模型和其他组件的单元测试。默认情况下，该文件包含一行代码:`from django.test import TestCase`。一个测试用例包含对同一段代码的多个相关测试。`TestCase`是一个 Django 对象，我们将继承它来构建自己的单元测试。这个类有两个方法，`setUp(self)`和`tearDown(self)`，它们在单独的测试函数之前和之后运行，以提供和清理一个测试数据库，这个数据库独立于你用`python manage.py runserver`访问的任何数据库。打开项目的`todo`文件夹中的`tests.py`来检查代码。

```
class SigninTest(TestCase):

    def setUp(self):
        self.user = get_user_model().objects.create_user(username='test', password='12test12', email='test@example.com')
        self.user.save()

    def tearDown(self):
        self.user.delete()

    def test_correct(self):
        user = authenticate(username='test', password='12test12')
        self.assertTrue((user is not None) and user.is_authenticated)

    def test_wrong_username(self):
        user = authenticate(username='wrong', password='12test12')
        self.assertFalse(user is not None and user.is_authenticated)

    def test_wrong_pssword(self):
        user = authenticate(username='test', password='wrong')
        self.assertFalse(user is not None and user.is_authenticated) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们测试登录功能。因为我们使用的是内置的 Django 方法，只要数据库没有被破坏，这应该可以正常工作，所以我们只需要简单的测试:如果给出了正确的信息，就进行身份验证，如果给出了不正确的信息，就不要进行身份验证。这个测试用例让我们看到了更多关于 Django 中单元测试的东西。第一个是测试用例中的所有测试方法必须以`test_`开始，以便在我们执行`python manage.py test`命令时运行。测试用例中的其他方法被认为是辅助函数。另一个重要的部分是所有的测试方法都必须将`self`作为参数，其中`self`是对`TestCase`对象的引用。`TestCase`，我们继承它来创建我们的类，它提供了评估布尔值的断言方法。如果作为参数传递的值与断言一致，则`self.assertSomething()`调用通过，否则失败。只有当方法中的每个断言都通过时，测试方法才通过。

```
class TaskTest(TestCase):

    def setUp(self):
        self.user = get_user_model().objects.create_user(username='test', password='12test12', email='test@example.com')
        self.user.save()
        self.timestamp = date.today()
        self.task = Task(user=self.user,
                         description='description',
                         due=self.timestamp + timedelta(days=1))
        self.task.save()

    def tearDown(self):
        self.user.delete()

    def test_read_task(self):
        self.assertEqual(self.task.user, self.user)
        self.assertEqual(self.task.description, 'description')
        self.assertEqual(self.task.due, self.timestamp + timedelta(days=1))

    def test_update_task_description(self):
        self.task.description = 'new description'
        self.task.save()
        self.assertEqual(self.task.description, 'new description')

    def test_update_task_due(self):
        self.task.due = self.timestamp + timedelta(days=2)
        self.task.save()
        self.assertEqual(self.task.due, self.timestamp + timedelta(days=2)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们测试我们自己的模型:在`models.py`中定义的`Task`对象。为了建立测试用例，我们创建一个用户和一个任务(注意，由于任务和用户之间的外键关系，在`tearDown()`中删除用户也会删除任务)。这里，我们看到任何测试方法都可以有多个断言，并且只有当所有断言都成功时才通过。我们可以在 setup 函数之外写入数据库，这是在更新任务时进行的。否则，这是一个非常类似于注册测试的测试，事实上，大多数模型测试用例只是关于创建、读取、更新和销毁数据库中的对象，尽管带有方法的模型测试起来更有趣。

```
class SignInViewTest(TestCase):

    def setUp(self):
        self.user = get_user_model().objects.create_user(username='test',
                                                         password='12test12',
                                                         email='test@example.com')

    def tearDown(self):
        self.user.delete()

    def test_correct(self):
        response = self.client.post('/signin/', {'username': 'test', 'password': '12test12'})
        self.assertTrue(response.data['authenticated'])

    def test_wrong_username(self):
        response = self.client.post('/signin/', {'username': 'wrong', 'password': '12test12'})
        self.assertFalse(response.data['authenticated'])

    def test_wrong_pssword(self):
        response = self.client.post('/signin/', {'username': 'test', 'password': 'wrong'})
        self.assertFalse(response.data['authenticated']) 
```

Enter fullscreen mode Exit fullscreen mode

测试视图比测试模型要复杂一些。然而，由于我们正在编写 API，我们不必像在 web 应用程序中那样担心前端测试。因此，我们可以用视图测试来代替以前通过 Postman 进行的手工测试。`self.client`是 Django 测试库中的一个 HTTP 客户端。我们用它向“/signin/”发出一个 post 请求，请求中包含用户凭证。我们测试和以前一样的东西:正确的登录信息，错误的用户名和错误的密码。这是非常有用的，因为它告诉我们，如果模型测试通过了，但是视图测试失败了，那么问题不在模型上，这限制了我们调试的范围。我们对与任务相关的视图做类似的事情。

```
class AllTasksViewTest(TestCase):

    def setUp(self):
        self.user = get_user_model().objects.create_user(username='test',
                                                         password='12test12',
                                                         email='test@example.com')
        self.user.save()
        self.timestamp = date.today()
        self.client.login(username='test', password='12test12')

    def tearDown(self):
        self.user.delete()

    def test_no_tasks(self):
        response = self.client.get('/all/')
        self.assertEqual(response.data, {'tasks': []})

    def test_one_task(self):
        self.task1 = Task(user=self.user,
                          description='description 1',
                          due=self.timestamp + timedelta(days=1))
        self.task1.save()
        response = self.client.get('/all/')
        self.assertEqual(response.data, {'tasks': [OrderedDict([('id', 1),
                                                                ('description', 'description 1'),
                                                                ('due', str(self.timestamp + timedelta(days=1)))])]}) 
```

Enter fullscreen mode Exit fullscreen mode

这个案例测试“/all/”端点。测试用例本身有更多的方法，但是上面复制的片段向我们展示了所有的新东西。在设置中，我们使用`self.client.login()`,这样客户端就像一个登录的用户。然后，我们创建任务，并将它们与我们期望的格式化输出进行比较。这个例子更清楚地说明了`setUp()`和`tearDown()`方法的好处，因为一个测试中的任务不会延续到其他测试中。同样，这个测试隔离了视图组件，因为底层模型是单独测试的。

现在您已经理解了测试代码，运行`python manage.py test`来运行所有的测试。让我们检查输出:

```
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
...FF..........
======================================================================
FAIL: test_due_future (todo.tests.DueTodayTest)
---------------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/Philip/Code/WFH/mkdev_blog/djangotesting/taskmanager/todo/tests.py", line 155, in test_due_future
    self.assertFalse(self.task.due_today())
AssertionError: True is not false

======================================================================
FAIL: test_due_past (todo.tests.DueTodayTest)
---------------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/Philip/Code/WFH/mkdev_blog/djangotesting/taskmanager/todo/tests.py", line 161, in test_due_past
    self.assertFalse(self.task.due_today())
AssertionError: True is not false

---------------------------------------------------------------------------
Ran 15 tests in 2.232s

FAILED (failures=2)
Destroying test database for alias 'default'... 
```

Enter fullscreen mode Exit fullscreen mode

所有通过的测试都用`.`表示，而未通过的测试则用`F`表示。失败的测试会产生错误，解释断言失败的原因。我们还没有谈到的测试正在失败，我们将在下一节中解决这个问题。在我们继续之前，您可能会注意到这个测试代码非常冗长。事实上，我们只测试了一小部分功能，然而测试文件已经和视图文件一样多了。这是意料之中的，你真的不能有太多的测试，只要它们都是准确的。当您更改代码时，一些测试会中断，这只是向您显示您需要修复哪些测试。因此，您应该期望不断地积累测试代码，并且期望在一个普通的生产应用程序中，您的测试行数是代码行数的几倍。

### 什么是测试驱动开发？

让我暂时回到开头的故事。经过几周一个又一个错误的战斗，我和我的团队为整个代码库编写了单元测试。我们有完整的测试覆盖，这意味着每一行代码都至少经过了一次测试。这持续了几个星期，直到我们决定实质性地改变我们的数据库模式。我们没有重写测试，而是丢弃了损坏的测试，几天后我们又开始经历“意外损坏”的错误。测试驱动的开发可以防止这种倒退。

为了保持有用，你的测试需要和你的代码一起更新。一些程序员实践测试驱动开发，以走在代码变更的前面。当你开发一个特性时，你要做的第一件事就是定义这个特性需要做什么。测试驱动开发通过首先为该功能编写测试来正式化这个过程。一般的想法是，您编写一个或多个定义特性的测试，编码直到这些测试通过，然后通过编写更多的测试来重复。

回到失败的测试向我们展示了我们需要在`Task`模型中实现一个`due_today()`方法。通过检查测试，我们可以看到，如果任务今天到期，它应该返回`True`，否则它应该返回`False`。复制下面的代码来替换`Task`模型中现有的`due_today()`方法，然后再次运行测试。

```
def due_today(self):
    return self.due == date.today() 
```

Enter fullscreen mode Exit fullscreen mode

测试通过了，表明我们的特性起作用了，我们可以继续了。这种开发方法在开始时需要更多的思考和努力来定义代码的行为，但使实际的编码过程更加简单和可实现。

为了*测试*您的理解，尝试为其他现有视图编写测试，或者使用测试来定义新功能，然后添加那些特性。一个简单的方法是在`task`模型中设置一个布尔字段`completed`,一旦任务完成，该字段将被设置为 true，允许我们保留已完成的任务，而不是删除它们。然后，考虑向您自己的项目中添加测试。盯着一个大型的、未经测试的项目，并试图将它全面覆盖，这可能是令人畏惧的。不要试图一次测试所有的东西，而是在开发项目或新功能的过程中，将测试添加到项目或新功能的小块中，并构建完整的覆盖范围。

进一步参考:

*   [Django 官方教程，第 5 部分](https://docs.djangoproject.com/en/2.2/intro/tutorial05/)
*   [Django 测试概述文件](https://docs.djangoproject.com/en/2.2/topics/testing/overview/)
*   [高级测试主题，包括测试覆盖范围](https://docs.djangoproject.com/en/2.2/topics/testing/advanced/)
*   关于测试 Django 代码库的 Django 文档

* * *

这是 Philip Kiely 写的一篇 mkdev 文章。你可以[聘请我们的 Python 导师](https://mkdev.me/en/specialization/programming-python)，自己学习 Python。