# 9 大 Django 概念-第 1 部分

> 原文：<https://dev.to/steelwolf180/top-9-django-concepts-part-1-3b2n>

[![Photo by Med Badr Chemmaoui on Unsplash](img/311a1ca57e688109c849be339ee3577f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ybq4q0MR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/ly2f59p4unnn/7FM0FXhYdHc3120JTUGjXE/b8bf92284e371be5e669a7a4b04f0826/med-badr-chemmaoui-ZSPBhokqDMc-unsplash.jpg)

# 简介

[![undraw complete design ongo](img/bbbcdd43eae5cd607fa166d9555cfd1e.png)](//images.ctfassets.net/ly2f59p4unnn/26bqIuRMy2rXjVhbvF82x2/3dbaf9e881dd90666138c5319635442d/undraw_complete_design_ongo.png)

当我决定专攻 Django 开发人员后，第一次深入到 Django 时。

任何人学习理解 Django 都需要大量的概念。

由于 Django 开发方法的力量，你不得不以一种**单一的**和**固执己见的**方式进行 web 开发，用**庞大的**软件包生态系统来支持你的需求。

这可能会阻碍潜在的 Django 开发者，他们更喜欢使用 Python 开发 web 应用程序的**灵活性**、较少的**学习**和**非个人化的**方法。

这些人想要通过选择他们自己的冒险来完成事情，使用更少量的包，而不是 Django 的包含电池的方法。

在本系列的第一部分，我将只介绍 **3** 的概念及其相关的技术术语，以帮助您快速使用 Django。

# 模型视图模板(MVT)

[![MVT Diagram of Django by TutorialPoints](img/b10dcb401111f135d092342bbad81434.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4IuAp269--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tutorialspoint.com/djanimg/django_mvc_mvt_pattern.jpg)

第一个概念可能需要一些时间让你理解。当你正在浏览像 DjangoGirls 或 T2 这样的教程时。

Django 遵循类似的概念，如[模型视图控制器(MVC)](https://www.tutorialsteacher.com/mvc/mvc-architecture) 或[三层架构](https://www.techopedia.com/definition/24649/three-tier-architecture)。

它试图让开发者更容易维护代码库，让 T2 维护代码库，让 T4 管理代码库。

通过使**多个单独的**可替换部件形成整个软件系统。

## 型号

在这里，你的大部分时间花在了为你的用户或组织开发的编码功能上，这通常被称为**业务逻辑**。

每当你创建一个新的 Django 应用程序时，它都会被 Django 识别。

该模型被称为`models.py`，它驻留在应用程序目录中。

在这个文件中，它还包括数据库查询，您可以从数据库的记录中找到**答案**。

然后将查询的结果传递给你的**视图**和**模板**，并显示为网页。

## 视图

视图由处理**用户交互**的代码组成，比如用户提交表单和**格式化**你从数据库得到的答案**适合**你的模板。

然后将它发送到您的模板文件以生成网页。

它包括基于数据库建立的答案的逻辑，如 HTTP 错误响应(404 或 200 状态代码)。

它也可以是用户交互数据的传输，以便由 **models.py** 进一步处理。

请注意，您应该**永远不要**在**视图中包含大量数据处理的代码。**

因为它会显著降低你的网页显示速度。

每当你发现一个网站的显示速度有问题时，他们就被称为胖视图和瘦模型。

因此，在开发 Django web 应用程序时，最好坚持使用**胖模型和瘦视图**。

## 模板

顾名思义，它是一个用于为网站生成网页 HTML 文件的模板。

你可以认为它类似于 [markdown 语言](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)。

它用于在 GitHub 或 GitLab 中生成静态站点或项目 wikis。

学习嵌入一个 [Django 语言语法](https://docs.djangoproject.com/en/2.2/ref/templates/language)来为你的模板显示数据。

# 对象关系映射(ORM)

[![undraw mind map cwng](img/2e51ff6c2dabb311a9170da06ed40a58.png)](//images.ctfassets.net/ly2f59p4unnn/1xhry9I4lvu4phhHMn8Khw/29f2fde94dcedb456d894e040609af2f/undraw_mind_map_cwng.png)

对于 Django 来说，ORM 允许你专注于处理不同类型的数据库。

在没有任何与特定数据库相关的知识的情况下，对数据库执行 [CRUD(创建、读取、更新、删除)](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)操作。

这是一个优势，因为您正在使用一种标准化的方式对数据库执行 **CRUD** 操作。

这种方法的缺点是为了迎合每个数据库的某些特性或设计差异而进行重构所花费的时间。

请注意，对于生产级环境，建议您使用 **Postgres** 而不是 **MongoDB** 。

原因可以在这篇文章中找到，作者 **PyDanny** 为 [2 勺 Django](https://twoscoopspress.com/products/two-scoops-of-django-1-11) 所作的名为[何时使用 MongoDB 搭配 Django](https://www.pydanny.com/when-to-use-mongodb-with-django.html) 的文章。

# 管理面板

[![undraw server down s4lk](img/387274b59ec6de10cb1f37ff9fed5e6b.png)](//images.ctfassets.net/ly2f59p4unnn/Yb7jM5bZpYf3pzZtjv6os/5166e4958159a6db3201415ecac42f59/undraw_server_down_s4lk.png)

这是迄今为止 Django 最好的特点。

因为你不需要花很多时间来创建或定制你自己的用户管理。

该面板允许您从`models.py`开始管理数据库中每个表的记录。

这可以在 Django 管理面板下进一步定制，以满足您的需求。

通过在 Django 应用程序中导入这样的代码，命名为`admin.py` file。

```
from django.contrib import admin
from catalog.models import Author, Genre, Book, BookInstance

admin.site.register(Book)
admin.site.register(Author)
admin.site.register(Genre)
admin.site.register(BookInstance) 
```

Enter fullscreen mode Exit fullscreen mode

关于如何定制 Django 管理面板的更多细节。

可以在 [Django 项目文档](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#modeladmin-objects)、Mozilla 的 [Django 教程或者](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Admin_site) [Dumbfounds](https://youtu.be/g5DTIiFAiSk) 的优秀视频教程中找到

# 结论

[![undraw into the night vumi](img/195cd7a124d7e40e054d4ec7f2f872bf.png)](//images.ctfassets.net/ly2f59p4unnn/1vxZYiia4QmvmdlzLpFRPe/2e3a099236a18135483253cbe11d7d12/undraw_into_the_night_vumi.png)

我希望这 3 个概念对你有用，可以帮助你加速学习 Django。

因为这些术语在我刚开始的时候对我来说很混乱。

虽然我对这个 3 部分系列的内容有一个粗略的想法。

如果你能在评论区给我一些关于 Django 概念的建议，我将不胜感激，这些建议可能会让你感到困惑。

如果你喜欢我的文章，请**注册**Max[冒险家简讯](http://eepurl.com/dOUoUb)获取我每周在 **Python** 、**创业**和 **Web 开发**偶然发现的牛逼内容。

你也可以**关注**我来获取我在 **Dev** 上的文章的**最新**更新

除此之外，我希望能帮助任何有兴趣创业的人。

我的空闲时间是从晚上 9 点到 GMT+8 点的凌晨 2 点，再见！！！。

这篇文章最初发表在 Max 的博客上，位于[Django 的 9 大概念-第 1 部分:4 分钟](https://www.maxongzb.com/top-9-django-concepts-part-1-4-mins/)和[照片由 Med Badr Chemmaoui 在 Unsplash 上拍摄](https://unsplash.com/photos/ZSPBhokqDMc)

# 参考文献

*   [DjangoGirls Django 教程](https://tutorial.djangogirls.org/en/)
*   [Mozilla 开发者网络的 Django 教程](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django)
*   [模型视图控制器(MVC)](https://www.tutorialsteacher.com/mvc/mvc-architecture)
*   [三层架构](https://www.techopedia.com/definition/24649/three-tier-architecture)
*   [Django 十大错误](https://www.toptal.com/django/django-top-10-mistakes)
*   [降价语言](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
*   [Django 语言语法](https://docs.djangoproject.com/en/2.2/ref/templates/language)
*   姜戈奥姆
*   [CRUD(创建、读取、更新、删除)](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)
*   [何时使用 MongoDB 和 Django](https://www.pydanny.com/when-to-use-mongodb-with-django.html)
*   两勺姜戈
*   [Django 项目文件](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#modeladmin-objects)
*   Mozilla 的 Django 教程
*   [笨蛋](https://youtu.be/g5DTIiFAiSk)
*   [来自 UnDraw 的插图](https://undraw.co/illustrations)