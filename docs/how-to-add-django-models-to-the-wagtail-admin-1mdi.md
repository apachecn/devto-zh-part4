# 如何将 Django 模型添加到 Wagtail 管理中

> 原文：<https://dev.to/revsys/how-to-add-django-models-to-the-wagtail-admin-1mdi>

## 版本

*   Python 3.7
*   姜戈 2.2
*   Wagtail 2.6

当使用 Wagtail 时，您可能会发现您对一些数据库模型使用 Wagtail 页面模型，而对其他数据库模型使用常规的 Django 模型。

Django `User`模型就是一个很好的例子。当你登录 Wagtail admin 时，你可以在`Settings`子菜单中看到 Django `User`型号。`User`车型不是 Wagtail 车型；这和你在 Django 项目中看到的不使用 Wagtail 的`User`模型是一样的。Wagtail 只是为你把它暴露给管理员。

[![Users menu under the Settings menu in the Wagtail admin](img/3ad8bb60a7488e5d8d84044a646fa174.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--16ncmH1t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n4iczjc4zch9pya74ykc.png)

我们可以对 Django 模型做同样的事情:我们可以将它们暴露给 Wagtail admin，这样我们就不必维护两个单独的管理界面来管理我们的网站内容。

对于这个例子，让我们假设我们正在使用这些模型:

```
from django.db import models 

class Pizza(models.Model):

    name = models.CharField(max_length=30)
    toppings = models.ManyToManyField("Topping")

class Topping(models.Model):

    name = models.CharField(max_length=30) 
```

## 添加单个模型

关于如何实现这一点，Wagtail docs 非常清楚，但是让我们一步步来。

首先，确保`wagtail.contrib.modeladmin`在你的`INSTALLED_APPS` :
中

```
# settings.py 
INSTALLED_APPS = [
    ...
    "wagtail.contrib.modeladmin",
] 
```

接下来，在与您想要向 Wagtail 管理员公开的模型相同的应用程序中，添加一个名为`wagtail_hooks.py`的文件。

```
# wagtail_hooks.py 
from wagtail.contrib.modeladmin.options import ModelAdmin, modeladmin_register 

from .models import Pizza

class PizzaAdmin(ModelAdmin):
    model = Pizza 
    menu_label = "Pizza"  
    menu_icon = "pick" 
    menu_order = 200 
    add_to_settings_menu = False 
    exclude_from_explorer = False 
    list_display = ("name",)
    list_filter = ("toppings",)
    search_fields = ("name",)

modeladmin_register(PizzaAdmin) 
```

让我们在`ModelAdmin`类中浏览这些选项:

*   `model`:您正在添加的模型的名称。
*   `menu_label`:留空以使用您的模型中的`verbose_name_plural`。给它一个值，为 Wagtail 菜单指定一个新标签。
*   Wagtail admin 中的每个菜单项都有一个图标，你可以指定你想使用的那个。这里有一个可用图标的列表。
*   你希望这个模型以什么顺序出现。000 是第一，100 是第二，以此类推。注意:如果您向管理员添加多个模型，如果其中两个具有相同的`menu_order`，您将不会得到一个错误；Wagtail 会为你挑选。
*   `add_to_settings_menu`:是否希望该菜单项出现在 Wagtail admin 的**设置**子菜单中。
*   `exclude_from_explorer`:如果**不希望**浏览器(管理中的搜索框)从这个模型返回结果，设置为 True。如果你**想要**浏览器从这个模型返回结果，设置为 False。(很混乱。)
*   `list_display`:与 Django admin 相同；在 Wagtail admin 中列出您希望在该模型的列表页面上显示的字段。
*   `list_filter`:与 Django admin 相同；在 Wagtail admin 的侧边栏中提供您想要用来过滤的字段。
*   `search_fields`:与 Django admin 相同；提供您希望浏览器用来返回搜索结果的字段。

最后一步是注册 admin 类。一旦你完成并启动了你的服务器，你就可以在 Wagtail 管理中看到你的模型了:

我们可以对 Django 模型做同样的事情:我们可以将它们暴露给 Wagtail admin，这样我们就不必维护两个单独的管理界面来管理我们的网站内容。

对于这个例子，让我们假设我们正在使用这些模型:

```
from django.db import models 

class Pizza(models.Model):

    name = models.CharField(max_length=30)
    toppings = models.ManyToManyField("Topping")

class Topping(models.Model):

    name = models.CharField(max_length=30) 
```

## 添加单个模型

关于如何实现这一点，Wagtail docs 非常清楚，但是让我们一步步来。

首先，确保`wagtail.contrib.modeladmin`在你的`INSTALLED_APPS` :
中

```
# settings.py 
INSTALLED_APPS = [
    ...
    "wagtail.contrib.modeladmin",
] 
```

接下来，在与您想要向 Wagtail 管理员公开的模型相同的应用程序中，添加一个名为`wagtail_hooks.py`的文件。

```
# wagtail_hooks.py 
from wagtail.contrib.modeladmin.options import ModelAdmin, modeladmin_register 

from .models import Pizza

class PizzaAdmin(ModelAdmin):
    model = Pizza 
    menu_label = "Pizza"  
    menu_icon = "pick" 
    menu_order = 200 
    add_to_settings_menu = False 
    exclude_from_explorer = False 
    list_display = ("name",)
    list_filter = ("toppings",)
    search_fields = ("name",)

modeladmin_register(PizzaAdmin) 
```

让我们在`ModelAdmin`类中浏览这些选项:

*   `model`:您正在添加的模型的名称。
*   `menu_label`:留空以使用您的模型中的`verbose_name_plural`。给它一个值，为 Wagtail 菜单指定一个新标签。
*   Wagtail admin 中的每个菜单项都有一个图标，你可以指定你想使用的那个。这里有一个可用图标的列表。
*   你希望这个模型以什么顺序出现。000 是第一，100 是第二，以此类推。注意:如果您向管理员添加多个模型，如果其中两个具有相同的`menu_order`，您将不会得到一个错误；Wagtail 会为你挑选。
*   `add_to_settings_menu`:是否希望该菜单项出现在 Wagtail admin 的**设置**子菜单中。
*   `exclude_from_explorer`:如果**不希望**浏览器(管理中的搜索框)从这个模型返回结果，设置为 True。如果你**想要**浏览器从这个模型返回结果，设置为 False。(很混乱。)
*   `list_display`:与 Django admin 相同；在 Wagtail admin 中列出您希望在该模型的列表页面上显示的字段。
*   `list_filter`:与 Django admin 相同；在 Wagtail admin 的侧边栏中提供您想要用来过滤的字段。
*   `search_fields`:与 Django admin 相同；提供您希望浏览器用来返回搜索结果的字段。

最后一步是注册 admin 类。一旦你完成并启动了你的服务器，你就可以在 Wagtail 管理中看到你的模型了:

[![Pizzas menu in sidebar in the Wagtail admin](img/9171d91a302b44e445f2216a875e9880.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PAQJc4-S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nnu9vamd1svpgh770bra.png)

## 添加相关型号

在我们的示例模型中，我们有两个模型:`Pizza`和`Toppings`。我们可以手动将`Topping`模型添加到 Wagtail admin 中，并让它出现在`Pizza`模型的正下方。我们刚刚学会了怎么做！

但它与`Pizza`模型关系如此密切，如果我们能够在一个子菜单中将这两个模型联系在一起，这可能会很好，有点像**设置**在管理中是它自己的子菜单，包含用户、重定向、站点等。

回到`wagtail_hooks.py` :

```
# wagtail_hooks.py 
from wagtail.contrib.modeladmin.options import (
    ModelAdmin, 
    ModelAdminGroup, 
    modeladmin_register 
)

from .models import Pizza, Topping

class PizzaAdmin(ModelAdmin):
    ...
    menu_order = 000 
    ...

class ToppingAdmin(ModelAdmin):
    model = Topping 
    menu_label = "Toppings"  
    menu_icon = "edit" 
    menu_order = 100 
    add_to_settings_menu = False 
    exclude_from_explorer = False 
    list_display = ("name",)
    search_fields = ("name",) 
```

将我们的两个模型联系在一起以同样的方式开始:我们为每个模型创建一个从`ModelAdmin`继承的类，并标识必要的属性，如`model`和`menu_icon`，以控制它们的列表页面和搜索行为。

然后，我们添加一个继承自`ModelAdminGroup` :
的新类

```
# wagtail_hooks.py 
from wagtail.contrib.modeladmin.options import (
    ModelAdmin, 
    ModelAdminGroup, 
    modeladmin_register 
)

from .models import Pizza, Topping

class PizzaAdmin(ModelAdmin):
    ...
    menu_order = 000 
    ...

class ToppingAdmin(ModelAdmin):
    ...
    menu_order = 100 
    ... 

class PizzaGroup(ModelAdminGroup):
    menu_label = "Pizzas" 
    menu_icon = "pick"
    menu_order = 500 
    items = (PizzaAdmin, ToppingAdmin)

modeladmin_register(PizzaGroup) 
```

在`PizzaGroup`类中，我们有一些相同的属性:

*   我们在 Wagtail 管理菜单中设置我们想要这组相关模型的名称
*   我们想在这个菜单中使用哪个图标
*   相对于其他菜单项，我们希望这个菜单出现在侧边栏的什么地方

我们还添加了一个新的属性`items`，在这里我们列出了哪些`ModelAdmin`类我们想成为这个组的一部分。在我们的例子中，我们希望`PizzaAdmin`和`ToppingAdmin`属于这个组，所以我们增加了它们。

请注意我们对`PizzaAdmin`和`ToppingAdmin`中的`menu_order`所做的更改:现在它们被设置为`000`和`100`。当`ModelAdmin`类将成为一个组的一部分时，设置`menu_order`你希望它们如何相互关联，而不是与 Wagtail 管理中的其他菜单项关联。然后将`ModelAdminGroup`类的`menu_order`设置为您希望它在管理侧菜单中出现的顺序的正确值。

然后我们向 Wagtail admin 注册整个组，而不是单独的`ModelAdmin`类。当我们重新加载 admin 时，我们会看到:

[![Pizzas menu in the Wagtail admin that opens to reveal a Pizza and Toppings menu](img/5ca7598134d4a25c29bf8d559fe986b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LpKrz9iB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0523sil6cswbmqofbgxe.png)

在最左边，有一个新的菜单项 **Pizzas** 展开了一个子菜单。子菜单包含到**披萨**和**配料**管理界面的链接！

**注意**:如果您启用了 Django 管理，并且您的模型已经在 Django 管理中，这不会从常规的 Django 管理中禁用它们。您可以自由地在 Wagtail 管理和 Django 管理中访问您的模型，或者此时您可以选择从 Django 管理中删除您的模型(或者完全禁用 Django 管理，如果您愿意的话)。

## 有用链接

*   [model admin 上的 Wagtail 文档](http://docs.wagtail.io/en/v2.6.1/reference/contrib/modeladmin/)
*   [一个 Wagtail StreamField 图标列表](https://thegrouchy.dev/general/2015/12/06/wagtail-streamfield-icons.html)

特别感谢 Jeff Triplett 和 Jacob Burch 对本文的帮助。