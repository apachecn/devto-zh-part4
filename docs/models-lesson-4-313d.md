# 模型-第 4 课

> 原文：<https://dev.to/jonathanfarinloye/models-lesson-4-313d>

## 目录

1.  [型号](#models)
2.  [创建模型](#create)
3.  [注册模型](#register)

## 车型

我们可以将模型描述为现实世界数据或对象的表示。例如博客帖子。如果我们要围绕一篇博客文章建立一个模型，我们可以有一个 ***标题*** ， ***发布日期*** ，也许是一个 ***封面图片*** ，肯定是文章的 ***正文*** 。让我们一起研究这个问题，看看我们如何对这些信息进行建模。

正如你可能想到的，既然我们在谈论博客，我们现在应该在博客应用程序中工作。由于我们正在处理创建模型，我们将使用 ***models.py*** 文件。

然而，在创建模型之前，让我们编辑我们的 ***settings.py*** 文件，以准备我们的 Django 项目来接受媒体文件，在本例中是封面图像。移动到 ***settings.py*** 文件，在底部添加:

```
123. MEDIA_ROOT = os.path.join(BASE_DIR, 'media/')
124.
125. MEDIA_URL = '/media/' 
```

MEDIA_ROOT 设置为项目基目录中名为“MEDIA”的文件夹。而 MEDIA_URL 是用于访问媒体文件的 URL。

## 创建模型

模型类似于对象。它们实际上是对象，创建它们看起来像这样:

```
1.  from django.db import models
...
...
4.  class Blog(models.Model): 
```

> 你可以在这里阅读更多关于 Django 模型的信息。
> 除了 ***博客*** 之外，你还可以给它取另一个名字，但既然我们是在和博客 app 打交道，这个名字就合理了。

在我们的模型中，我们需要:

1.  文章标题的字段
2.  日期字段
3.  用于封面图像的字段
4.  帖子内容的字段

Django 站点给了我们很多可以在我们的对象中使用的字段。在网站上搜索，我们找到了一些我们可能需要的。

1.  标题的 CharField
2.  每次日期的日期时间字段
3.  封面图像的图像字段
4.  帖子的文本字段

让我们创建我们的模型，看看它是什么样子:

```
4. class Blog(models.Model):
5.     title = models.CharField(max_length=50)
6.     raw_date = models.DateTimeField()
7.     cover = models.ImageField(upload_to='images/')
8.     content = models.TextField() 
```

标题设置为模型。CharField，max_length 属性设置为 50 个字符。max_length 属性是 CharField 的必需参数。
日期时间字段和日期字段不需要位置参数。然而，它可以采用一些可选的参数，例如 ***auto_now*** ，每次模型更新时，它都将其设置为当前的日期和时间。这里可以找到其他[。
同样，为了将日期格式化成一种更可展示的格式，我们可以使用](https://docs.djangoproject.com/en/2.2/ref/models/fields/#datefield) [strftime](https://docs.python.org/3/library/time.html#time.strftime) 。要做到这一点，我们需要给对象添加一个函数。

```
10.     def date(self):
11.         return self.upload_date.strftime('%b %d, %Y') 
```

正如在[站点](https://docs.python.org/3/library/time.html#time.strftime)上解释的，这种格式化方法 ***%b %d，%Y*** ，会给出类似 2019 年 6 月 12 日或 2019 年 11 月 13 日的结果。

下一个字段是用于内容的文本字段。我们使用 TextField 而不是 CharField，因为 TextField 是为长文本内容设计的。如果你还记得，我们说过 CharField 需要一个 ***max_length*** 属性。 ***文本字段*** 不需要该属性。

你的 ***models.py*** ，此时，应该是这样的:

```
1.  from django.db import models
2.  
3.
4.  # Create your models here. 5.  class Blog(models.Model):
6.      title = models.CharField(max_length=50)
7.      raw_date = models.DateTimeField()
8.      cover = models.ImageField(upload_to='images/')
9.      content = models.TextField()
10.      
11.      def date(self):
12.          return self.raw_date.strftime('%b %d %Y') 
```

## 注册模特

<figure>[![Current admin site containing only default content](img/cb506f4bf62debbd03f599e788b0e68b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qoXQOPOq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/apnh5gwy9r3dr5l01jsk.png) 

<figcaption>这是我们的管理站点现在的样子</figcaption>

</figure>

注册我们创建的模型是为了让它在管理站点中“显示”，这样我们就可以添加内容和编辑内容。要注册一个模型，我们打开博客应用程序中的 ***admin.py*** 文件，添加几行代码。

```
1. from django.contrib import admin
2. from .models import Blog
3. # Register your models here. 4. admin.site.register(Blog) 
```

> 这将在管理站点上注册名为 Blog 的模型。

<figure>[![New admin site showing Blog model](img/8e83b67e1fec1ba8126d7a83217e6e06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2YCawttr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfvsgmn9h88y4tqe4ouz.png) 

<figcaption>刷新管理站点显示博客模型</figcaption>

</figure>

然而，如果你尝试点击博客部分，你会得到一个操作错误。

[![Operational Error on trying to load Blog](img/e6bf642b8c8518d1c8893e6961165f3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6mfatBjI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/89neqxzxr48jogz81vig.png)

异常值为“没有这样的表 blog_blog”。这可能会告诉你这是一个数据库问题。记住我们在前面章节中学到的关于[数据库](https://dev.to/jonathanfarinloye/moving-forward-lesson-2-1ibe#database)的东西，我们需要迁移我们对 ***models.py*** 文件所做的更改。几乎所有对 ***models.py*** 的更改都需要在继续之前进行迁移。为此，请打开您的终端，进行迁移，然后再迁移。

```
/awesome-project$ python manage.py makemigrations 
```

然后:

```
/awesome-project$ python manage.py migrate 
```

> 注意:迁移文件夹是在博客目录中自动创建的。切勿删除此文件夹，因为这可能会给你的应用和数据库带来严重问题。从经验来说😵。如果你好奇的话，这是可以修复的。😉

加载管理网站并检查博客会打开一个页面，显示我们有 0 篇博客文章。点击“添加”按钮添加文章。

<figure>[![Add post page](img/0b4a381c87b26ed8ae0009480f52ad38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BUoJWL7l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5z8aqcqmhpboyw6zfy5x.png) 

<figcaption>显示了我们创建的所有字段。🍉给你</figcaption>

</figure>

保存新的文章，我们看到它被命名为“博客对象(1)”。我们不想这样。为了调整这个我们另一个函数:

```
14.     def __str__(self):
15.         return self.title 
```

> 这种变化不需要迁移。如果您尝试迁移，它会显示“未检测到任何更改”。

如果您现在查看管理站点中的博客应用程序，您会看到“博客对象(1)”已更改为博客文章的标题。

<figure>[![Blog app on the admin site](img/2839d912fe44dfffc5b1499e16ea17af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TUsQ4Uol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vvlyqkj7jf6vzl52ii5o.png) 

<figcaption>博客 app 长什么样</figcaption>

</figure>

我们可以让这个页面保持原样，但为了使它更容易，我们可以做一些定制。打开你的 ***admin.py*** 文件，并将其编辑成如下所示:

```
1.  from django.contrib import admin
2.  from .models import Blog
3.  # Register your models here. 4.
5.
6.  class BlogAdmin(admin.ModelAdmin):
7.      list_display = ('title', 'date', 'cover')
8.      search_fields = ['title', 'content']
9.    
10.
11.  admin.site.register(Blog, BlogAdmin) 
```

刷新博客应用程序，我们看到一个新的面貌。

<figure>[![Blog app now includes date, link to image, as well as a search bar](img/1670ed8abf2d72ef9dc47284b585af3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sOoRINsK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vys5uv7ukokov4sep73n.png) 

<figcaption>现在包括日期、图片链接以及搜索栏</figcaption>

</figure>

我们要做的最后一件事是添加一个 slug 字段。为此，我们首先向模型中添加一个新字段:

```
5.  class Blog(models.Model):
6.      title = models.CharField(max_length=50)
7.      raw_date = models.DateTimeField()
8.      cover = models.ImageField(upload_to='images/')
9.      content = models.TextField()
10      slug = models.SlugField() 
```

您可以为 SlugField 设置 max_length 属性，也可以不设置。如果不设置，Django 默认使用 50。我们要做的下一件事是将 slug 设置为一个 ***预填充字段*** 。这将根据一个或多个其他字段自动设置 slug。我们希望我们的 slug 依赖于标题，所以在 ***admin.py*** 中，我们给类添加了一个新行:

```
1.  from django.contrib import admin
2.  from .models import Blog
3.  # Register your models here. 4.
5.
6.  class BlogAdmin(admin.ModelAdmin):
7.      list_display = ('title', 'date', 'cover', 'slug')
8.      search_fields = ['title', 'content']
9.      prepopulated_fields = {'slug': ('title',)}
10.
11.
12. admin.site.register(Blog, BlogAdmin) 
```

第 8 行将 slug 设置为一个依赖于标题的“预填充”字段。我们还在列表显示中添加了 ***鼻涕虫*** ，看看我们的鼻涕虫是什么。如果您现在尝试进行迁移(由于添加了一个新字段)，Django 会给您两个选择，要么为已经存在的行提供一次性的默认值，要么退出并在 models.py 中设置默认值。
要从这个问题向前推进，我们可以退出并在 ***models.py*** 中设置一个默认值。

```
10.     slug = models.SlugField(default='random-text') 
```

再次进行迁移，迁移，然后转到已经存在的博客文章，稍微修改一下名称(您可以添加一个空格并删除它😉)只是为了骗系统自动设置名字。一旦完成，保存并检查一切正常。完成后，您可以删除 slug 字段中的默认属性，进行迁移并再次迁移(如果多个博客帖子具有相同的 slug，这没有多大意义🤷🏾‍♂️🤷🏾‍♀️).

你可以在这里阅读更多关于定制 Django ModelAdmin [的内容。在下一篇文章中，我们将看到如何在不进入管理站点的情况下从站点添加博客文章。
<small>项目可以在这里找到</small>
<small>周末也就没有文章了。把你学到的东西拿来玩，用它来创造美好的东西。在那之前享受你的周末吧，🍊🍎🍍</small>](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#module-django.contrib.admin)

### 欢呼✨✨