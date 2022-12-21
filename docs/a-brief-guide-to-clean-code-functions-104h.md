# 简洁代码指南:函数

> 原文：<https://dev.to/danialmalik/a-brief-guide-to-clean-code-functions-104h>

嗨伙计们，

这是我在“干净代码”系列中的第二篇文章。在我的第一篇文章中，我谈到了如何以一种“干净”的方式命名变量。现在我要谈谈“干净的函数”，就像上次一样，这些要点来自罗伯特·c·马丁的书《干净的代码》。

下面是编写函数时应该记住的一些要点。那我们开始吧。

## 功能要小！！！

这是编写函数的第一条规则。编写函数的第二条规则是它们应该更小。小功能通俗易懂，意图明确。

下面是一个用 python 写的样例代码。

```
def get_product(html_response):
   title = html_response.css('.title').html()
   price = html_response.css('.price').html()
   try:
        price = int(price)
   except ValueError:
        price = None

   sizes_html = list(html_response.css('.product .size').html())
   sizes = [parse_size(html_size) for html_size in sizes_html]
   # ...and so on 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把同样的逻辑分成更小的函数，就像这样:

```
 def get_product(html_response):
   title = get_title(html_response)
   price = get_price(html_response)
   sizes = get_product_sizes(html_response)
   # ...and so on 
def get_title(html_response):
   return html_response.css('.title').html()

def get_price(html_response):
   price = html_response.css('.price').html()
   try:
        price = int(price)
   except ValueError:
        price = None
   return price

def get_product_sizes(html_response):
   sizes_html = list(html_response.css('.product .size').html())
   sizes = [parse_size(html_size) for html_size in sizes_html]
   return sizes 
```

Enter fullscreen mode Exit fullscreen mode

这样，当目标网站发生变化时，调试和重构代码就更容易了。

### 分块和缩进

保持函数小的规则也意味着我们保持`if`、`else`和`while`语句中的代码块最少，最好是一行，并且一行应该是一个函数调用。类似地，我们应该通过将嵌套的逻辑移到另一个函数中来避免在函数中嵌套这样的结构。

## 做一件事！(单一责任原则)

> 函数应该做一件事。他们应该做好这件事。他们应该只做这件事。

一个函数只能做一件事。如果一个功能做不止一件事，那么这个功能应该被分成更多的功能。下面的例子可以说明这一点:

```
def generate_weather_report(date):
   data = weather_client.get_date(date)
   if not data:
       logger.error('Error!')
   else:
       report_data = {}
       # logic to get required data into report_data dict.
       store_into_db(report_data) 
```

Enter fullscreen mode Exit fullscreen mode

这个功能显然在执行多项任务。

*   从网上获取数据。
*   验证日期
*   生成数据
*   将数据存储到数据库中

这可以被重构为下面的代码:

```
data = get_weather_data(date)
is_valid = validate_data(data)
if is_valid:
   report_data = generate_weather_report(data)
   store_into_db(data)
else:
   logger.error('Error!')

def get_weather_data(date):
   return  weather_client.get_date(date)

def generate_weather_report(date):
       report_data = {}
       # logic to get required data into report_data dict.
       return report_data 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们的函数`generate_weather_report`只做一件事。

### 章节

类似地，函数中不同的逻辑部分，如初始化、声明、验证和实际逻辑也应该划分到函数中。

## 切换报表

*请注意 python 中没有 switch 语句。以下示例仅用于解释目的。*

很难做出小的`switch`语句，因为`switch`语句的目的是做 *`N`* 的事情。
虽然在某些情况下`switch`语句可以避免，也应该避免。
例如:

```
def month_index_to_string(index):
   switch index:
       case 1:
           return "January"
       case 2:
           return "February"
       default:
           return "INVALID"
       ... 
```

Enter fullscreen mode Exit fullscreen mode

可以写成:

```
MONTHS = {
   1: "January",
   2: "February",
   ...
}

def month_index_to_string(index):
   try:
       return MONTHS[index]
   except KeyError:
       return "INVALID" 
```

Enter fullscreen mode Exit fullscreen mode

## 使用描述性名称

函数的意图应该从它的名字中清晰可见，并且它的实现不能偏离它的名字。不要害怕使用长函数名。理想情况下，函数名应该以动词开头，例如`get_employees_data`、`delete_employee`等。
你可以在我的[第一篇文章](https://dev.to/danialmalik/a-beginner-s-guide-to-clean-code-part1-naming-conventions-139l)中找到更多关于函数名的细节。

## 函数参数

理想情况下，函数应该没有参数(幂零函数)。接下来是一个(一元的)，然后是两个(二元的)，最后是三个(三元的)自变量函数，应该尽可能避免使用。如果可能的话，不能使用超过三个参数的函数。几个参数可能意味着函数不止做一件事。从测试的角度来看，测试带有许多参数的函数也是很困难的，因为测试所有参数的组合是很困难的(或者几乎是不可能的)。如果你必须向函数传递大量数据，试着通过一个对象发送数据。

```
def evaluate_person_salary(person_id, hours_worked, hourly_rate, overtime_rate)
   #logic 
```

Enter fullscreen mode Exit fullscreen mode

这绝对是邪恶的。应该写成:

```
# person_id, hours_worked, hourly_rate, overtime_rate are data members of the person_data object. def evaluate_person_salary(person_data, hours_worked):
   #logic 
```

Enter fullscreen mode Exit fullscreen mode

如果在某些情况下，您不能将参数分组到单个对象中，那么您就做错了，应该将该函数拆分。

### 标志自变量

标志参数是可怕的，永远不应该使用。将一个`boolean`传递给一个函数意味着该函数正在执行多个任务，这明显违反了 *SRP* 原则。

### 参数列表

有时我们想给一个函数传递可变数量的参数，例如`"{}:{}-{}".format(date, person, message)`。
如果这些参数被同等对待，那么它们将被视为单个参数(在本例中为`List`)。

## 没有副作用

一个函数必须只做一件事，没有其他副作用。有时我们认为我们的功能只做一件事，但实际上它做的不止一件事，那些其他的事情是它的副作用。考虑下面这个例子:

```
def check_password(username, password):
   if authenticate_user(username, password):
       return True
   else:
       return False
...
def authenticate_user(username, password):
   if User.objects.get(username=username).check_password(password):
       login(username)
       return True
   return False 
```

Enter fullscreen mode Exit fullscreen mode

在这里，`check_password`似乎只做了一件事，检查密码，但是当我们看到`authenticate_user`的定义时，我们知道它也登录了用户。

## 降压法则

作者的 stepdown 规则对于编写易读的代码非常有帮助。根据这条规则，您应该以这样一种方式编写函数，即函数调用后面应该跟有它们在下面代码中的定义。

这样，在读取代码文件时，当您看到一个函数调用时，您不必向上滚动来查找它的定义，因为当您以恒定的速度阅读代码时，您会在下面找到它的定义。这种方法将在下一节的例子中说明。

## 不重复自己

这是编程最重要的原则之一。你不应该重复你自己。如果你写了一个其他地方也需要的类函数，把它移出类，使它成为一个实用程序/帮助器函数。如果在两个或更多的类中需要相同的函数，用该函数创建一个父类，并从它继承其他类。

最近，当我为某个项目编写测试用例时，我不得不使用这种方法。测试用例的设置过程基本相同，所以我决定用`setUp`函数创建一个基类，然后从它继承其他测试用例。这样，我就不必为所有的测试用例重复自己了。
下面描述了`setUp`函数的抽象代码。你也会注意到*下降*的方法。你不必一次又一次地上下移动来理解代码。

```
class SampleTestsBaseClass(TestCase):
   """Base class for all test cases of "Sample" module."""

   def setUp(self):
       """Setup all test data required for testing any of the test cases. """
       super(SampleTestsBaseClass, self).setUp()
       self.topic = self._create_topic()
       self.course = self._create_course()
       self.team = self._create_team(self.course.id, topic_id=self.course.teams_topic['id']))
       self.user = self._create_user()
       self.team_membership = self._create_team_membership(self.team, self.user)

   def _create_topic(self):
       """ Return a topic dict. """
       topic = {u'name': u'Topic', u'description': u'The best topic!', u'id': u'0', 'url': 'example.com/topic/0'}
       return topic

   def _create_course(self, **kwargs):
       """ Create and return a course with test data """
       course = CourseFactory.create(kwargs)
       return course

   def _create_team(self, course_id, topic_id):
       """ Create a CourseTeam for provided course_id and topic_id
       """
       team = CourseTeamFactory.create(
           course_id=course_id,
           topic_id=topic_id,
           name='Test Team',
           description='Testing Testing Testing...'
       )
       return team

   def _create_user(self, username, password, **kwargs):
       user = UserFactory.create(username=username, password=password, **kwargs)
       return user

   def _create_team_membership(self, team, user):
       """Create a team membership object that is responsible for joining of a user in a team.
       """
       membership = CourseTeamMembershipFactory.create(team=team, user=user)
       return membership

# Now inherit the test cases from the above base class. class Module1TestCase(SampleTestsBaseClass):
   ...

class Module2TestCase(SampleTestsBaseClass):
   ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以用这个*基类*继承所有其他相关的测试用例，而不必重复 *`setUp`* 代码。

## 结论

功能是任何系统的基本组成部分。这些是系统的动词，而类是名词。这篇文章是关于以一种易于阅读、理解、维护和重构的方式编写函数。应该在牢记 *DRY* (不要重复自己)原则的同时实现功能。通过遵循上述原则和实践，你的功能将是描述性的、干净的和组织得很好的。