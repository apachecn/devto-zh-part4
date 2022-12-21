# 创建您的第一个 Laravel 包

> 原文：<https://dev.to/chrisrhymes/creating-your-first-laravel-package-1g5o>

我一直对为 Laravel 制作一个包感兴趣，所以我想我会制作一个简单的示例包，并分享我从这个过程中学到的东西。这个包非常简单，只是包含了一些额外的收集方法，所以我决定把它叫做 [extra-collect](https://github.com/chrisrhymes/extra-collect) 。

## 文件夹结构

我首先创建一个新文件夹并运行 composer init 来生成 composer.json 文件。我按照命令添加了名称、`chrisrhymes/extra-collect`和描述`A Laravel package containing a collection of collections`。

和大多数 php 包一样，我创建了一个 src 目录，所有 PHP 文件都将存放在这个目录中。然后，我在 src 目录中创建了一个名为 ExtraCollectServiceProvider.php 的文件。以下是目前为止的文件结构。

```
/src/ExtraCollectServiceProvider.php
composer.json 
```

Enter fullscreen mode Exit fullscreen mode

## 自动加载器

然后，我将以下代码添加到 composer.json 中，以便从 src 目录中自动加载服务提供者和任何带有 ChrisRhymes\ExtraCollect 名称空间的 php 类。

```
"autoload": {
    "psr-4": {"ChrisRhymes\\ExtraCollect\\": "src/"}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 自动包发现

下一步是对自动包发现进行排序。从 Laravel 5.5 开始，您可以在 composer.json 中添加一些额外的或“额外的”行，以允许 Laravel 在安装软件包时自动注册您的服务提供商。

我按照 Laravel docs 站点中的例子，将包的服务提供者类添加到 providers 数组中。如果你有任何外观，你可以在别名数组中注册它们，但是因为我在这个包中没有任何外观，所以我没有注册。

```
"extra": {
    "laravel": {
        "providers": [
            "ChrisRhymes\\ExtraCollect\\ExtraCollectServiceProvider"
        ],
        "aliases": {
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 服务商

我现在关注服务提供者类。服务提供者需要扩展`Illuminate\Support\ServiceProvider`类，并拥有 boot 和 register 方法。我在服务提供者的 boot 方法中添加了新的集合方法，这意味着我还需要为`Illuminate\Support\Collection`类添加一个 use 语句。

如果你使用的是 IDE，那么它可能会抱怨找不到`Illuminate\Support\ServiceProvider`或`Illuminate\Support\Collection`类。为了解决这个问题，我添加了 Laravel 框架作为开发依赖项，这样它就可以找到这些类。

```
composer require --dev laravel/framework 
```

Enter fullscreen mode Exit fullscreen mode

然后为我创建了一个 vendor 文件夹，并为我安装了 Laravel 框架包，这样 IDE 就不会抱怨了，因为它现在可以找到类了。

## 。吉蒂尔

显然，你不希望 vendor 文件夹成为你的包的一部分，所以这就是我把它作为 dev 依赖项添加的原因。当我将 vendor 文件夹推送到 GitHub 时，为了避免它包含在项目中，我创建了一个`.gitignore`文件并添加了 vendor 目录，当我在那里时，也将 composer.lock 文件添加到了`.gitignore`中。

## 测试

在我将我的包推送到 GitHub 之前，我想确保代码按预期工作，所以我认为创建一些测试是个好主意。服务提供商使用了一些 Laravel 类，所以我需要一种方法将框架包含到我的测试中。

幸运的是有一个名为[orchestra test bench](https://github.com/orchestral/testbench)的包可以做到这一点，所以我用 composer 安装了这个包

```
composer require --dev "orchestra/testbench=^3.8" 
```

Enter fullscreen mode Exit fullscreen mode

软件包声明您必须扩展 Orchestral TestCase，而不是 Laravel TestCase，所以我创建了一个 tests 目录，其中包含一个 TestCase 类来完成这个任务。

在 TestCase 类中，我添加了 getPackageProviders 方法，并包含了我的 packages 服务提供者类，因此它可以在测试中使用。

```
protected function getPackageProviders($app)
{
    return [
        ExtraCollectServiceProvider::class
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

因为测试在 tests 目录中，而不是 src 目录中，所以类不会被自动加载。为了解决这个问题，我在 composer.json 文件中添加了以下内容，这样任何带有 Test 名称空间的内容都可以从 tests 目录中加载。

```
"autoload-dev": {
    "psr-4": {
        "ChrisRhymes\\ExtraCollect\\Test\\": "tests/"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我创建了测试，每个集合方法一个类，确保测试类扩展了我的新 TestCase 类。每个测试收集一个数组并应用相关的收集方法，然后测试输出是预期的结果。

然后我创建了一个 phpunit.xml 文件，说明在哪里可以找到测试，并使用以`Test.php`结尾的 php 类。

最后，我将以下脚本添加到我的 composer.json 文件中，使用 composer test 命令
运行测试

```
"scripts": {
    "test": "phpunit"
} 
```

Enter fullscreen mode Exit fullscreen mode

我跑了`composer test`，幸运的是所有的测试都通过了。

## 自述

我创建了一个简单的自述文件来帮助向他人解释收集方法是如何工作的。我决定遵循官方的 Laravel docs [Collections 页面](https://laravel.com/docs/5.8/collections)的文档风格，提供一个使用示例和预期的输出。

## 部署和安装软件包

示例包准备就绪后，我在 GitHub 中创建了一个新的存储库(按照 composer.json 的名称称为 extra-collect)，将 git remote repo 添加到我的目录中，提交文件并将它们推送到 GitHub。

我非常兴奋地开始使用这个包，看看它是如何工作的。我已经建立了一个 Laravel 项目，并准备在我的开发机器上运行，所以在运行`composer update`
之前，我在 composer.json 文件的 require 部分添加了我的新包

```
"chrisrhymes/extra-collect": "dev-master" 
```

Enter fullscreen mode Exit fullscreen mode

但是出了点问题，作曲家找不到我的包。它就在那里，可以在 GitHub 上公开获得，但后来我意识到 composer 需要更多的帮助。

我需要用 packagist 告诉 composer 包在哪里。我注册了一个 packagist 帐户，然后添加了我的新包，将它链接到 GitHub repo。我惊喜地发现它使用起来如此快捷和简单。

我再次尝试`composer update`，这一次它找到了这个包，安装了它，并自动注册了包服务提供商。我现在可以从一个 Laravel 项目中开始使用我的定制集合方法了。

最后一点要注意的是，我建议你在 GitHub 中为你的包标记一个版本，这样人们就可以指定一个版本号，而不是像上面的例子那样使用 dev-master。

我希望您发现这是开始创建您的第一个 Laravel 包的有用指南。