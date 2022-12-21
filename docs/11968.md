# 如何在 Django 中设置 REST 服务或 Web 应用程序

> 原文：<https://dev.to/divyeshaegis/how-to-set-up-a-rest-service-or-a-web-application-in-django-33gp>

[![How-to-set-up-a-REST-Service-or-a-Web-Application-in-Django](img/1089a0f92969544fe1379180119a7951.png)](https://postimages.org/) 
**简介**:

Django 是一个非常通用的框架，主要用于开发 web 应用程序，但也广泛用于创建移动应用程序后端和 REST APIs 等。在这里，我们将快速看一下如何使用 Django 实现这些事情。

你们可能都知道，REST 代表“代表性状态转移”。基本上，用户(或一端的某个软件代理)提供一些输入(或执行一些活动),这些输入(或活动)的结果使用允许向服务器发送请求的协议发送到服务器端(大多数情况下使用 HTTP 协议，但不一定是 HTTP，只要它能支持请求/响应方案)。服务器收到请求后，对系统的状态做出适当的改变(因此我们称之为“状态转移”)。

**简要讨论如何创建 Django 项目**:

Django 运行在 HTTP 协议上，因此在创建 REST API 或 RESTful 应用程序时非常方便。它允许用户在 web 界面(或者任何能够向 Django 应用程序发送 HTTP 请求对象的界面)上提供输入。在本文的示例中，我们将考虑 web 应用程序接口，但是您可以轻松地将它们扩展到移动应用程序或任何其他可以发送类似 HTTP 请求的应用程序。

为了理解如何做这些事情，我们需要了解一点 Django。这是我关于同一主题的 5 篇系列文章中的第一篇，所以在这篇文章中，我将向你介绍一些非常重要的 Django 文件，以及 Django 应用程序的基本结构。(请注意，‘Django’读作‘jango’，而不是 D-jango。第一个字符 D 不发音。)

一个“Django 项目”是由许多“Django 应用程序”组成的，这些应用程序按照一定的逻辑缝合在一起。创建 Django 项目的第一件事是在 python 库中安装 Django。这通常通过运行命令“pip install Django==”来完成。您可以跳过版本号部分，在这种情况下，将会安装您的操作系统和 python 发行版可以处理的 Django 的最新版本。很公平。

一旦安装成功完成，您将在您的计算机路径中找到一个名为“Django-admin”的文件。(运行“which django-admin”查看它在哪里，在我的系统上它在“/usr/bin/django-admin”中)。要创建一个项目，您需要在您选择的目录中执行以下命令。最好创建一个新目录，将“cd”放入其中，并运行以下命令:

```
#> django-admin  startproject 

```

您的项目名称必须提供创建 Django 项目。例如，让我们假设我正在创建一个名为“testyard”的项目。为了创建我的项目，我需要执行以下命令:

```
 #> django-admin startproject testyard
```

The above command creates the following directory structure in the directory where you executed the above mentioned command.

```
 testyard/
    manage.py
    testyard/
        __init__.py
        settings.py
        urls.py
        wsgi.py 

```

**对上述**创建的文件的讨论:

第一个文件 manage.py 非常重要。这个文件最终将帮助您创建应用程序、运行 Django 测试 web 服务器、运行 Django 管理任务(本文没有涉及，但我们将在后面的文章中讨论)以及许多其他活动。**初始化**。py 文件确保我们将在一个面向对象的环境中工作。接下来是两个最重要的文件:settings.py 和 urls.py。我们将首先讨论 settings.py 文件，然后讨论 urls.py。

settings.py:这个文件包含 Django 应用程序工作的设置。这个文件中一些比较重要的配置参数是数据库连接参数(用户名、密码、数据库名、数据库主机、端口、引擎等。)、静态文件位置、模板加载器、中间件类(我们将在另一篇文章中讨论这一点，但请记住，这些程序在到达“查看”功能之前与请求对象进行交互，因此它们能够在处理请求之前更改请求)、已安装的应用程序、root urlconf(我们将在本文后面讨论这一点)等。

您也可以在同一位置自由编写自己的应用程序特定设置文件。但是，您需要给它取一个不同的名称，然后将 settings.py 导入其中。settings.py 中有许多其他参数，我希望您仔细检查位于"[https://github . com/sup MIT 13/python-ff-ext/blob/master/urldbabp/settings.py "](https://github.com/supmit13/python-ff-ext/blob/master/urldbapp/settings.py%E2%80%9D)位置的 settings . py 文件，找出这些参数。

urls.py:该文件包含客户端(浏览器或 bot)请求的路径到特定视图函数的映射，当 HTTP 请求请求给定路径时，需要调用该函数。您将在这里找到这样一个 urls.py 文件的示例:"[https://github . com/sup MIT 13/python-ff-ext/blob/master/urldbabp/URLs . py "](https://github.com/supmit13/python-ff-ext/blob/master/urldbapp/urls.py%E2%80%9D)。这个文件也称为“urlconf”。

名为 wsgi.py 的文件允许您创建的应用程序在 uwsgi 和 nginx(生产环境的东西)上运行。我们将在本文的后面查看这个文件。

一旦创建了这些文件，就可以开始在项目中创建应用程序了。但是首先，您需要弄清楚前面的过程是否适用于服务器。为此，您需要运行应用程序的开发服务器

为此，您需要在命令提示符下运行以下命令。请注意，为了运行它，您需要位于“manage.py”所在的目录中。

# >python manager . py runserver 0 . 0 . 0 . 0:8080

该命令将启动开发服务器。现在，您应该能够进入浏览器，键入 localhost:8080/login/，如果 urls.py 中有该 url 路径的条目，您应该能够看到登录屏幕。或者，您可以键入 localhost: 8080/来查看您的 Django 服务器是否正在运行。

这使您有足够的能力开始使用 python/Django 创建 REST API。在接下来的部分中，我们将演示如何编写代码来创建 REST API。

Django 遵循 MVC 设计模式的一种变体，它通常被称为 MVT 模式。“M”代表模型(DB 模式)，V 代表 views.py，它在 MVC 框架中是控制器部分，而“T”代表模板，它在 MVC 框架中是视图组件。

因为我们将首先查看 REST API，所以我们将专注于 views.py。首先，查看以下链接中的 handlers . py:"[https://github . com/sup MIT 13/python-ff-ext/blob/master/urldbabp/savwhatyoulookedat/handlers . py "](https://github.com/supmit13/python-ff-ext/blob/master/urldbapp/savewhatyoulookedat/handlers.py%E2%80%9D)。在这个程序中，views.py 中的代码已经放入 handlers.py 中，但是实际上，它们以相同的方式工作。在本例中，您可以将 handlers.py 中的代码视为 views.py 中的代码。(实际上，这是我很久以前刚开始涉足 Django 时写的一些代码。因此我做了一些东西来看看 Django 是否足够灵活，并且发现它确实很灵活。

Django 的结构和行为:

“views.py”文件将包含一个或多个函数(注意，它也可能包含类，我们将在另一篇文章中查看 views.py 中的类)，函数将只包含一个参数:“request”。“请求”基本上是 HttpRequest 类的一个实例(由 Django 定义)。Django 应用程序中的所有视图函数都使用这个 HttpRequest 类的一个实例，并返回 HttpResponse 类的一个实例。

为什么我们需要在 views.py 文件中包含多个函数？这是因为，您可能希望提供多个页面或响应，每个页面或响应都有不同的 URL 路径。例如，您可能希望提供一个登录页面、一个注册页面、一个检查试图登录到您的应用程序的用户的登录凭证的活动，以及一个仪表板页面。每个页面都有不同的 URL 路径，比如说，[http://mywebsite.com/login](http://mywebsite.com/login)用于登录页面，[http://mywebsite.com/registration](http://mywebsite.com/registration)用于注册页面，等等。因此，每个 URL 都需要一个单独的函数来处理请求。因此，在 views.py 文件中，我们需要为每一个动作提供一个函数。

我们如何将上面提到的每个活动与特定的 views.py 函数关联起来？这就是 urls.py 文件发挥作用的地方。urls.py 有一个特定 Django 应用程序的 views.py 中特定函数的每个 URL 路径的映射(还记得我们在开始提到的 Django 项目由一个或多个 Django 应用程序组成。我们稍后将进入应用程序部分)。urls.py 看起来像下面这样:

```
 urlpatterns = patterns('',
    (r'^time/$', current_datetime),
    (r'^savewhatyoulookedat/login/$', userLogin),
    (r'^savewhatyoulookedat/register/$', userRegister),
    (r'^savewhatyoulookedat/$', saveURL),
    (r'^savewhatyoulookedat/logout/$', userLogout),
    (r'^savewhatyoulookedat/downloadPlugin/$', firefoxPluginDownload),
    (r'^savewhatyoulookedat/managedata/$', manageData),
    (r'^savewhatyoulookedat/commandHandler/$', executeCommand),
    (r'^savewhatyoulookedat/showimage/$', showImage),
    (r'^savewhatyoulookedat/search/$', searchURL),
    # (r'^admin/doc/', include('django.contrib.admindocs.urls')),
    # Uncomment the another line to  enable the authority:
    # (r'^admin/', include(admin.site.urls)),
) 

```

基本上，如您所见，映射实际上是在应该匹配 URL 路径的正则表达式和 views.py 文件中的函数之间进行的。当您在 views.py 文件中创建更多的活动处理程序时，您需要在 urls.py 文件中为每个处理程序添加一个条目。对于包含多个应用程序的 Django 项目，urls.py 文件可能如下所示:

```
 urlpatterns += patterns('',
  url(r'^%s$'%mysettings.REGISTER_URL, 'skillstest.Auth.views.register', name='newuser'),
  url(r'^%s$'%mysettings.DASHBOARD_URL, 'skillstest.views.dashboard', name='dashboard'),
  url("%s$"%mysettings.LOGIN_URL, 'skillstest.Auth.views.login', name='login'),
url(r'%s$'%mysettings.MANAGE_TEST_URL, 'skillstest.Tests.views.manage', name='managetests'),
url(r'%s$'%mysettings.CREATE_TEST_URL, 'skillstest.Tests.views.create', name='createtests'),
url(r'%s$'%mysettings.EDIT_TEST_URL, 'skillstest.Tests.views.edit', name='edittests'),
url(r'%s$'%mysettings.ABOUTUS_URL, 'skillstest.views.aboutus', name='aboutus'),
url(r'%s$'%mysettings.HELP_URL, 'skillstest.views.helpndocs', name='helpndocs'), url(r'%s$'%mysettings.CAREER_URL, 'skillstest.views.careers', name='careers'),
... ... ... 

```

“Auth”和“Tests”是“skillsets”项目中应用程序的名称。不要担心大写的变量——它们在别处有定义，对我们这里的例子无关紧要。

现在，让我们看看如何在项目中创建应用程序。我们通过执行以下命令来实现这一点:

```
 python manage.py startapp 

```

例如，如果我们的应用程序名为“Letsplay ”,那么我们将运行

python manage . py startapp let splay

上面的命令创建如下所示的目录结构:

```
 Letsplay/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py 

```

在上面的结构中，我们将主要关注 views.py 和 models.py 文件。然而，我们也将首先触及其他问题。

如果您想在 Django 中定制您的管理面板，那么“admin.py”文件是必需的。通常，如果您尝试访问 URL "[http://localhost:8080/admin "](http://localhost:8080/admin%E2%80%9D)，您会看到一个管理面板。这将向您显示您拥有的所有模型(我们稍后将讨论模型)，您的 Django 项目的配置设置(当然是以只读模式)，等等。
“apps . py”文件允许 Django 应用程序的创建者输入一些应用程序特定的参数。Django 项目中的每个应用程序都有自己的 apps.py 文件。

“tests.py”文件允许应用程序创建者为应用程序编写测试。这个文件需要符合一定的结构。它需要为特定的测试用例定义一个类。这个类需要有一个名为“setup”的方法，然后它应该将测试定义为类本身的方法。除非您是 Django 纯粹主义者，否则您不会使用这个文件来定义您的测试。通常，在现实生活场景中，我们有一个使用 Django 创建的应用程序，另一个使用其他技术创建的组件，以及几个装配在一起作为服务工作的其他组件。在这种情况下，我们需要编写测试来检查整个方案的功能，而不仅仅是 Django 部分。因此，几乎习惯于使用 python 或其他语言，如 Perl 或 ruby(或测试人员喜欢的任何语言)，将测试创建为不同的套件。

总的来说，您编写的任何应用程序(使用 Django 或任何其他语言和框架的 python)最终都会与数据库交互。所有 Django 应用程序也倾向于这样做。这就是 models.py 文件介入的地方。“models.py”文件基本上为您提供了 Django 应用程序中的 ORM(对象关系映射)方案。因此，对于首选数据库中的每个表，在 models.py 文件中都为其定义了一个类。它看起来像这样:

```
 from django.db import models
import os, sys, re, time, datetime
import inspect

"""
'Topic' is basically category or domain.
"""
class Topic(models.Model):
    topicname = models.CharField(max_length=150)
    user = models.ForeignKey(User, null=False)
    createdate = models.DateField(auto_now=True)
    isactive = models.BooleanField(default=True)

    class Meta: 
        verbose_name = "Topics Table"
        db_table = 'Tests_topic'

    def __unicode__(self):
        return "%s"%(self.topicname)

class Subtopic(models.Model):
    subtopicname = models.CharField(max_length=150)
    subtopicshortname = models.CharField(max_length=50)
    topic = models.ForeignKey(Topic, blank=False, null=False)
    createdate = models.DateField(auto_now=True)
    isactive = models.BooleanField(default=True)

    class Meta:
        verbose_name = "Subtopics Table"
        db_table = 'Tests_subtopic' # Name of the table in the database

    def __unicode__(self):
        return "%s (child of %s)"%(self.subtopicname, self.topic.topicname)

class Session(models.Model):
    sessioncode = models.CharField(max_length=50, unique=True)
    status = models.BooleanField(default=True) # Will be 'True' as soon as the user logs in, and will be 'False' when user logs out.
    # The 'status' will automatically be set to 'False' after a predefined period. So users will need to login again after that period.
    # The predefined value will be set in the settings file skills_settings.py. (skills_settings.SESSION_EXPIRY_LIMIT)
    user = models.ForeignKey(User, null=False, blank=False, db_column='userid_id')
    starttime = models.DateTimeField(auto_now_add=True) # Should be automatically set when the object is created.
    endtime = models.DateTimeField(default=None)
    sourceip = models.GenericIPAddressField(protocol='both', help_text="IP of the client's/user's host")
    istest = models.BooleanField(default=False) # Set it to True during testing the app.
    useragent = models.CharField(max_length=255, default="", help_text="Signature of the browser of the client/user") # Signature of the user-agent to guess the device used by the user.
    # This info may later be used for analytics.

    class Meta:
        verbose_name = "Session Information Table"
        db_table = 'Auth_session'

    def __unicode__(self):
        return self.sessioncode

    def isauthenticated(self):
        if self.status and self.user.active:
            return self.user
        else:
            return None

    def save(self, **kwargs):
        super(Session, self).save(kwargs) 

```

类中的属性是数据库中相应表中的字段。DB 表的名称在每个主题和子主题类的“类元”中定义，属性名为“db_table”。与这些表相关联的数据库是在 settings.py 文件中定义的(还记得我们讨论 settings.py 文件属性的时候吗？).对于 models.py 文件中使用的数据类型，您需要查阅 Django 文档，因为有相当多的数据类型和关系，这里无法处理它们。事实上，它们的文档非常丰富。然而，我们在上面只使用了其中的几个，它们是不言自明的。

事实上，姜戈很受欢迎有两个原因。

1.  它为开发人员提供了所有的锅炉板代码，因此编码人员不需要编写所有无聊的东西。

2.  它为编码者提供了 ORM，因此在数据库中检索或设置特定表的某一行中的值是非常容易的。这是它的“好的一面”。也有“负面”的一面。当您使用 ORM 时，您不使用 SQL 语句，因此如果操作有点复杂，ORM 会变得非常低效。使用 SQL 语句，可以做一些优化，让语句运行得更快，但是使用 ORM，就没有这种可能了。为此，Django 提供了一条出路。您可以创建“原始”SQL 语句来查询您的数据库，但是大多数开发人员很少使用这种方法。只有当您发现 ORM 操作数据库的方式明显效率低下时，才应该使用“原始”SQL 语句。

无论如何，现在让我们进入这份文件的最后阶段。这恰好是创建 REST 应用程序中最重要的阶段。我们现在来看看 views.py 文件。请参考下面的示例代码:

```
 # User login handler
def login(request):
    if request.method == "GET":
        msg = None
        if request.META.has_key('QUERY_STRING'):
            msg = request.META.get('QUERY_STRING', '')
        if msg is not None and msg != '':
            msg_color = 'FF0000'
            msg = skillutils.formatmessage(msg, msg_color)
        else:
            msg = ""
        # Display login form
        curdate = datetime.datetime.now()
        tmpl = get_template("authentication/login.html")
        c = {'curdate' : curdate, 'msg' : msg, 'register_url' : skillutils.gethosturl(request) + "/" + mysettings.REGISTER_URL }
        c.update(csrf(request))
        cxt = Context(c)
        loginhtml = tmpl.render(cxt)
        for htmlkey in mysettings.HTML_ENTITIES_CHAR_MAP.keys():
            loginhtml = loginhtml.replace(htmlkey, mysettings.HTML_ENTITIES_CHAR_MAP[htmlkey])
        return HttpResponse(loginhtml)
    elif request.method == "POST":
        username = request.POST.get('username') or ""
        password = request.POST.get('password') or ""
        keeploggedin = request.POST.get('keepmeloggedin') or 0
        csrfmiddlewaretoken = request.POST.get('csrfmiddlewaretoken', "")
        userobj = authenticate(username, password)
        if not userobj: # Incorrect password - return user to login screen with an appropriate message.
            message = error_msg('1002')
            return HttpResponseRedirect(skillutils.gethosturl(request) + "/" + mysettings.LOGIN_URL + "?msg=" + message)
        else: # user will be logged in after checking the 'active' field
            if userobj.active:
                sessobj = Session()
                clientip = request.META['REMOTE_ADDR']
                timestamp = int(time.time())
                # timestamp will be a 10 digit string.
                sesscode = generatesessionid(username, csrfmiddlewaretoken, clientip, timestamp.__str__())
                sessobj.sessioncode = sesscode
                sessobj.user = userobj
                # sessobj.starttime should get populated on its own when we save this session object.
                sessobj.endtime = None
                sessobj.sourceip = clientip
                if userobj.istest: # This session is being performed by a test user, so this must be a test session.
                    sessobj.istest = True
                elif mysettings.TEST_RUN: # This is a test run as mysettings.TEST_RUN is set to True
                    sessobj.istest = True
                else:
                    sessobj.istest = False
                sessobj.useragent = request.META['HTTP_USER_AGENT']
                # Now save the session...
                sessobj.save()
                # ... and redirect to landing page (which happens to be the profile page).
                response = HttpResponseRedirect(skillutils.gethosturl(request) + "/" + mysettings.LOGIN_REDIRECT_URL)
                response.set_cookie('sessioncode', sesscode)
                response.set_cookie('usertype', userobj.usertype)
                return response
            else:
                message = error_msg('1003')
                return HttpResponseRedirect(skillutils.gethosturl(request) + "/" + mysettings.LOGIN_URL + "?msg=" + message)
    else:
        message = error_msg('1001')
        return HttpResponseRedirect(skillutils.gethosturl(request) + "/" + mysettings.LOGIN_URL + "?msg=" + message)
-------------------------------------------------------------------------------------------------------------------

# User registration handler
def register(request):
    privs = Privilege.objects.all()
    privileges = {}
    for p in privs:
        privileges[p.privname] = p.privdesc
    if request.method == "GET": # display the registration form
        msg = ''
        if request.META.has_key('QUERY_STRING'):
            msg = request.META.get('QUERY_STRING', '')
        if msg is not None and msg != '':
            var, msg = msg.split("=")
            for hexkey in mysettings.HEXCODE_CHAR_MAP.keys():
                msg = msg.replace(hexkey, mysettings.HEXCODE_CHAR_MAP[hexkey])
            msg = "

%s

"%msg
        else:
            msg = ""
        curdate = datetime.datetime.now()
        (username, password, password2, email, firstname, middlename, lastname, mobilenum) = ("", "", "", "", "", "", "", "")
        tmpl = get_template("authentication/newuser.html")
        #c = {'curdate' : curdate, 'msg' : msg, 'login_url' : skillutils.gethosturl(request) + "/" + mysettings.LOGIN_URL, 'register_url' : skillutils.gethosturl(request) + "/" + mysettings.REGISTER_URL, 'privileges' : privileges, 'min_passwd_strength' : mysettings.MIN_ALLOWABLE_PASSWD_STRENGTH, }
        c = {'curdate' : curdate, 'msg' : msg, 'login_url' : skillutils.gethosturl(request) + "/" + mysettings.LOGIN_URL, 'hosturl' : skillutils.gethosturl(request),\
             'register_url' : skillutils.gethosturl(request) + "/" + mysettings.REGISTER_URL,\
             'min_passwd_strength' : mysettings.MIN_ALLOWABLE_PASSWD_STRENGTH, 'username' : username, 'password' : password, 'password2' : password2,\
                 'email' : email, 'firstname' : firstname, 'middlename' : middlename, 'lastname' : lastname, 'mobilenum' : mobilenum, \
             'availabilityURL' :  mysettings.availabilityURL, 'hosturl' : skillutils.gethosturl(request), 'profpicheight' : mysettings.PROFILE_PHOTO_HEIGHT, 'profpicwidth' : mysettings.PROFILE_PHOTO_WIDTH }
        c.update(csrf(request))
        cxt = Context(c)
        registerhtml = tmpl.render(cxt)
        for htmlkey in mysettings.HTML_ENTITIES_CHAR_MAP.keys():
            registerhtml = registerhtml.replace(htmlkey, mysettings.HTML_ENTITIES_CHAR_MAP[htmlkey])
        return HttpResponse(registerhtml)
    elif request.method == "POST": # Process registration form data
        username = request.POST['username']
        password = request.POST['password']
        password2 = request.POST['password2']
        email = request.POST['email']
        firstname = request.POST['firstname']
        middlename = request.POST['middlename']
        lastname = request.POST['lastname']
        sex = request.POST['sex']
        usertype = request.POST['usertype']
        mobilenum = request.POST['mobilenum']
        profpic = ""
        #userprivilege = request.POST['userprivilege']
        csrftoken = request.POST['csrfmiddlewaretoken']
        message = ""
        # Validate the collected data...
        if password != password2:
            message = error_msg('1011')
        elif mysettings.MULTIPLE_WS_PATTERN.search(username):
            message =  error_msg('1012')
        elif not mysettings.EMAIL_PATTERN.search(email):
            message =  error_msg('1013')
        elif mobilenum != "" and not mysettings.PHONENUM_PATTERN.search(mobilenum):
            message = error_msg('1014')
        elif sex not in ('m', 'f', 'u'):
            message = error_msg('1015')
        elif usertype not in ('CORP', 'CONS', 'ACAD', 'CERT'):
            message = error_msg('1016')
        elif not mysettings.REALNAME_PATTERN.search(firstname) or not mysettings.REALNAME_PATTERN.search(lastname) or not mysettings.REALNAME_PATTERN.search(middlename):
            message = error_msg('1017')
....
....
    return HttpResponse(html)

The above code has 2 functions, and we will discuss them shortly. But before that, please take a look at the corresponding urls.py file for these 2 above functions:

urlpatterns = patterns('',
    (r'^savewhatyoulookedat/login/$', login),
    (r'^savewhatyoulookedat/register/$', register)
) 

```

如上所述，当您试图从浏览器(或任何其他网络客户端)访问以下 URL 时，将会调用“登录”功能:
[http://localhost:8080/savwhatyoulookedat/log in/](http://localhost:8080/savewhatyoulookedat/login/)

当您尝试访问以下网址时，将调用“注册”功能:
[http://localhost:8080/savewhatyoulookedat/register/](http://localhost:8080/savewhatyoulookedat/register/)

请注意“请求”对象是如何与 Django ORM 产品的其他一些对象一起使用的。例如，在登录函数中，有一个会话模型的实例。会话模型后面的 DB 表被命名为“Auth_session ”,它是在上面的 models.py 文件中指定的。因此，每当用户点击上面提到的某个 URL 时，视图就会运行一些 DB 查询，并计算出向客户机发送什么响应。这就是 RESTful 应用程序的工作方式，正如您所看到的，Django 真的让开发一个 RESTful 应用程序变得很容易。

**结论** :
[Python Django 开发是一个非常广泛的框架](https://www.nexsoftsys.com/technologies/python-development-services.html)，由于我们在讨论 REST 应用程序，所以我故意省略了 Django 模板。我将在另一篇文章中解释模板，但是因为 REST 应用程序并不总是需要 HTML 界面，所以我现在跳过它。此外，我们刚刚触及了 Django 的一些概念，这里没有足够的空间来详细讨论它们。我建议你通读这篇文章，尽你所能去理解，然后看看 Django 的官方文档，了解更多关于这个主题的细节。

希望这对你有用。