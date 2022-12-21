# 为几乎所有 django 项目创建一个认证应用程序。

> 原文：<https://dev.to/stuartelimu/create-an-authentication-app-for-almost-any-django-project-9kc>

大约一周前，我收到了从[发展到](https://dev.to/)的**一年俱乐部**徽章。我已经成为注册会员一年了。

我还没有写任何文章，但我想现在是时候回报社区了，因为我已经阅读了很多文章，这些文章对我的开发之旅有很大的帮助。

我将开始一个 django 系列，在这个系列中，我将创建一个 accounts 应用程序来管理大多数使用 django 创建的项目的用户认证。

这是我们将谈论的第一部分，项目设置，创建帐户应用程序，然后最后登录。

这是一个初学者系列，但我希望你知道一些基本的 django 和 Python。

让我们开始吧😊

打开您的终端，切换到您希望项目驻留的目录。创建一个新的目录/文件夹，并将其命名为 **ausers** 或任何你想用的名字。

```
mkdir ausers 
```

转到这个目录，然后创建一个虚拟环境，我们将在其中安装 django 和其他包

我们将创建一个名为 env 的虚拟环境。创建完成后，我们将激活我们的虚拟环境

```
virtualenv env

source env/bin/activate 
```

然后我们将使用 pip 安装 django。

```
pip install django 
```

安装完成后，我们将创建一个名为 **ausers** 的 django 项目，这是我们之前给目录起的名字，所以我们在命令的末尾添加了一个句点，告诉 django 在当前目录下创建文件，而不是创建新的文件。如果你想为你的项目取一个不同的名字，你可以直接指定名字而不是句号。

```
django-admin startproject ausers . 
```

现在，您可以在您喜欢的文本编辑器中打开该目录。

我们现在将创建**账户**应用，用于用户认证

```
./manage.py startapp accounts 
```

打开 settings.py，注册**账户**应用，不要把它放在所有其他应用的最上面