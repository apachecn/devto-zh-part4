# 使用 Xamarin 中的 SQLite 进行注册、登录和更新。形式

> 原文：<https://dev.to/logeshpalani98/register-login-and-update-using-sqlite-in-xamarin-forms-48kk>

## 简介

本文演示了如何在 SQLite 中注册、登录和更新用户数据。我们用 Xamarin 中的 SQLite DB 执行 CRUD 操作。表单，可以轻松地从本地数据库中存储和检索对象。

### 输出:

[![](img/057f694c187e7b50d51fffa0a59a169e.png)](https://1.bp.blogspot.com/-OTghAkJekjc/XQEvZBHKG6I/AAAAAAAAOJc/n_nXQGOK-PkAgDohThfeq0uoDMWzlmCWACLcBGAs/s1600/Screenshot%2B%252820%2529.png)

## 先决条件

*   Visual Studio 2019
*   这个示例项目只针对 Android 和 iOS。

## 第一步:

您可以创建新的 Xamarin。通过进入文件>>新建并选择 C#语言来形成应用程序。然后，选择 Xamarin。表单应用程序，然后单击创建。在下一个窗口中，输入您的应用程序和解决方案名称，然后单击 Create 按钮创建项目。

(例如:项目名称—_ SqliteLoginDemo_)

[![](img/baeb39e7a2f5977a4e47b147ff031b4b.png)](https://1.bp.blogspot.com/-DzfpRMMsR0U/XQEvsuFyifI/AAAAAAAAOJk/Q6tjBHtjGTQvql03in2vKR_-MgYqiILfACLcBGAs/s1600/Screenshot%2B%252813%2529.png)

## 第二步:

创建项目后，将 SQLite Nuget 包添加到您的项目中。

*   包名: *SQLite。Net-PCL*

为此，打开解决方案资源管理器并选择您的解决方案。右键单击并选择*“管理解决方案的 NuGet 包”*在弹出窗口中，点击导航到*浏览*选项卡，点击浏览“SQLite。Net-PCL”并选择下面的 NuGet 包并选择您的项目，然后安装它。

## 第三步:

现在，在共享项目中添加名为 ISQLiteInterface 的接口。为此，转到解决方案资源管理器，右键单击选择 Add >> Class，然后选择 Interface，并将其命名为 ISQLiteInterface。双击打开接口类，接口代码如下。一种方法称为 ISQLiteConnection，它将从本机应用程序返回数据库路径连接。

## 第四步:

接下来，我们将在 Android 和 iOS 中创建一个 SQLite DB 路径。为此，请展开 SqliteLoginDemo。Android 项目并创建一个名为*isqlitedinterface _ Android . cs*的类，并按照 iOS 的相同步骤创建一个名为*isqlitedinterface _ iOS . cs*的接口。下面给出了特定于平台的代码。在类中，我们在命名空间上方添加元数据代码，因为该类是为依赖项服务派生的。

*Android:isqlitedb interface _ Android . cs*

*IOS:_ _ isqlitedb interface _ IOS . cs*

## 第五步:

现在，从共享项目转到“创建模型”。为此，转到解决方案资源管理器> > sqlitdemo(Shared)> >右键单击并选择 Add >> class，给出类名并单击 Create。该类包含五个字段，第一个字段是主键和自动增量。其他字段是用户名、密码和电话号码。

*User.cs*

## 第六步:

之后，创建另一个名为 UserDB.cs 的 Helper 类，在这个类中，我们创建一个新的私有 SQLiteConnection。接口返回 DB 路径存储在这个 SQLiteConnection 中。这些查询是基于 LINQ 方法编写的。不需要编写 SQL 查询。GetUser 方法将从数据库中返回所有用户。GetSpecificUser 方法传递一个 id 参数来返回该特定用户。AddUser 方法将添加新用户。如果用户 id 已经存在，它将返回“已经存在”updateUser 方法用于更新现有的用户数据，如忘记的用户密码。deleteUser 方法用于删除特定的用户。最后一个是 LoginValidate

方法，用于检查用户是否存在。

*用户数据库. cs*

## 第七步:

最后，到应用的 UI 设计。应用程序页面是主页，登录页面，注册页面，主页。首先，设计 app 的主页面。该页面包含三个可视元素。

1-图像:这是应用程序的徽标

2-按钮:登录

3-按钮:注册

该页面代码如下所示。
T1】main page . xaml

### 步骤 7.1:

现在，打开主页后面的代码。添加以下代码。这里，我们实现了一个双击事件来导航到另一个页面。

*MainPage.xaml.cs*

## 第八步:

之后，创建另一个名为 RegisterPage 的 ContentPage。该页面包含用于向数据库注册或添加新用户数据的不同可视元素，并且该设计页面消耗一些用户数据[验证行为](https://www.c-sharpcorner.com/article/input-validation-in-xamarin-forms-behaviors/)，如电子邮件验证和密码验证。

1.  条目—电子邮件 Id
2.  条目—用户名
3.  输入—密码
4.  输入—确认密码
5.  条目—手机号码
6.  按钮—注册
7.  按钮—登录:如果您已经存在，请转到登录页面 *RegistrationPage.xaml*

### 步骤 8.1:

之后，打开 RegisterPage 后面的代码。该类别代码如下所示。在这个页面代码中，逻辑是检查数据库用户是否已经注册，或者是否需要添加一个新用户。如果用户单击了注册按钮，首先检查输入的数据是有效还是无效，然后检查数据库，看用户是否已经注册。如果用户没有注册，添加新的用户数据，否则，它将返回已经注册的用户。

*RegistrationPage.xaml.cs*

## 第九步:

接下来，创建一个名为 LoginPage 的新 ContentPage。LoginPage 设计包含不同的视觉元素。

1.  条目:邮件 id
2.  条目:密码
3.  按钮:验证在上述字段中输入的数据。
4.  按钮:忘记密码

*LoginPage.xaml*

### 步骤 9.1:

现在打开 LoginPage 后面的代码，这个类实现了不同的事件函数。登录事件实现检查用户凭证是否正确。如果用户存在，请转到主页。

*LoginPage.xaml.cs*

## 第十步:

在这一步中，创建一个名为 homepage 的新 ContentPage。在这个页面中，我们设计了一个简单的 ListView 来添加数据库中的注册用户数据，下面给出了这个页面的代码。

*HomePage.xaml*

### 步骤 10.1:

之后，打开主页后面的代码。这里，我们从数据库绑定用户数据。

*HomePage.xaml.cs*

### 输出:

[![](img/82d659497ea044143dd4aa8fa87217e4.png)](https://1.bp.blogspot.com/-OTghAkJekjc/XQEvZBHKG6I/AAAAAAAAOJg/QEijEC9-sIEIRlwiNo6HcQ0pvP56PWr-wCEwYBhgL/s1600/Screenshot%2B%252820%2529.png)

## 结论

我希望你学会了如何使用 Xamarin.Forms 执行 CRUD 操作。谢谢，如果你对我的文章有任何改进的建议，请评论。