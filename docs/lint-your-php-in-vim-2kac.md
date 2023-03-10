# 在 Vim 中 Lint 您的 PHP

> 原文：<https://dev.to/camilo/lint-your-php-in-vim-2kac>

> 清洁的目的不仅仅是清洁，而是感受生活在那种环境中的快乐。
> 
> ~玛丽·近藤

每个已经写了一段时间 PHP 的人最终都会遇到混乱的代码。温和的学习曲线、宽容的类型系统和简单的执行模型都有助于编写快速、肮脏并最终令人沮丧的 PHP。

2013 年，PHP 框架互操作性小组( [PHP-FIG](https://www.php-fig.org) )采用了 PSR-2，这是一种代码标准，旨在“减少扫描来自不同作者的代码时的认知摩擦”结果呢？阅读不同项目中不同作者的 PHP 代码从未如此简单。无论你是钻研你工作场所的专有代码还是 Laravel 雄辩的 ORM 代码，你都会发现它是可读的(即使不太能理解！).

我们的目标是在 Vim 中设置自动化工具，在您编写代码时清理代码。通过使它成为你的常规流程的一部分并获得快速的反馈，你将能够满怀信心地编写你的代码，一两年后当你再次打开这个文件时，你仍然能够阅读它！

> 如果你每天打扫你的浴室，你就永远不必打扫你的浴室。
> 
> ~呼唤·韦斯特的母亲，*尖声尖气:一个大嗓门女人的笔记*

我们将利用一些现有的工具:PHP 代码嗅探器
(PHP-CS)和 PHP-CS-Fixer。

## 安装 PHP-CS

要使用 composer 将 PHP-CS 添加到您的项目中，运行:

```
composer require --dev "squizlabs/php_codesniffer=\*" 
```

默认情况下，PHP-CS 根据 PEAR 编码标准进行检查。然而，它配备了其他几种配置，包括`PSR2`。我们可以使用`phpcs`命令运行 PHP-CS 来检查违反标准的情况。

```
./vendor/bin/phpcs --standard=PSR2 path/to/file 
```

然而，由于我们也希望在我们的
项目中实现标准化和自动化，我们将在我们的项目中创建一个配置 PHP-CS 的文件。PHP-CS 寻找的文件是一个`phpcs.xml`文件。

```
<?xml version="1.0"?>
<ruleset name="Custom Standard" namespace="MyProject\CS\Standard">
  <description>Our custom coding standard based on PSR-2</description>
  <rule ref="PSR2"/>
</ruleset> 
```

因为我们使用的是 PSR-2 标准，所以我们的规则集非常简单。PHP-CS 文档显示了更多的选项，包括排除一些文件或规则的能力，或者包括来自其他编码标准的规则。

现在，当我们运行`.vendor/bin/phpcs`时，我们将根据我们的`phpcs.xml`文件对我们的每个文件进行 PSR-2 编码标准检查！

## 安装 PHP-CS-Fixer

一般来说，我们不仅希望我们的计算机告诉我们什么时候出错了，我们还可以让它帮我们解决编码问题！这个工具是 PHP-CS-Fixer。

同样，我们将使用 Composer 来安装 PHP-CS-Fixer

```
composer require --dev friendsofphp/php-cs-fixer 
```

我们将能够使用`.vendor/bin/php-cs-fixer fix path/to/file`运行它。

我们还将在项目中创建一个`.php_cs.dist`文件，这样我们就可以标准化我们将要实施的规则。该文件将如下所示:

```
<?php

$finder = PhpCsFixer\Finder::create();

return PhpCsFixer\Config::create()
    ->setRules([
        '@PSR2' => true,
    ])
    ->setFinder($finder)
; 
```

## 与 Vim 集成

为了将这些工具引入 Vim，我们将依靠插件 ALE 。ALE 可以做很多事情，包括林挺(使用 PHP-CS)、修复(使用 PHP-CS-Fixer)，甚至使用语言服务器协议的更深层次的 IDE 类特性(将在后面的文章中讨论)。

首先，[根据您自己的 Vim 设置安装 ALE](https://github.com/w0rp/ale#installation)。

然后我们想添加一些配置，这样 ALE 就知道在哪里可以找到我们的工具。将以下内容添加到您的项目的`.vimrc`中，假设您正在使用`:exrc`来允许基于项目的 vim 配置。

```
let g:ale_php_phpcs_executable='./vendor/bin/phpcs'
let g:ale_php_php_cs_fixer_executable='./vendor/bin/php-cs-fixer'
let g:ale_fixers = {'php': ['php-cs-fixer']} 
```

这应该用 PHP-CS 和 PHP-CS-Fixer 设置 ALE！PHP-CS 应该异步运行，它发现的问题会在您键入时弹出。

为了运行 PHP-CS-Fixer，在一个文件中使用命令`:ALEFix`,它将运行 Fixer 并写入这些更改。

您也可以在每次保存时运行修复程序，方法是在您的`.vimrc`文件中添加`let g:ale_fix_on_save`。