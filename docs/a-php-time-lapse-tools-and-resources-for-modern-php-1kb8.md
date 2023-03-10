# PHP 延时:现代 PHP 的工具和资源

> 原文：<https://dev.to/jubstuff/a-php-time-lapse-tools-and-resources-for-modern-php-1kb8>

PHP 作为一种简单的脚本语言已经有了很大的发展，现代 PHP 有几个独特的特征(看我在那里做了什么？)

但是，通过简单的谷歌搜索以及关于如何编写现代 PHP 的大量材料，你会发现许多过时的博客帖子或资源应该被忽略。

在这篇文章中，我试图收集一些帮助我成为更好的 PHP 开发人员的优秀资源。

但首先，时间流逝。

* * *

我仍然记得我用 PHP 创建的第一个网站。那是在高中时期:我在自学 Turbo Pascal，我能够用 HTML 做简单的网站。

然后看到我一个朋友编程，不是 Turbo Pascal。我记得所有那些美元符号都有点奇怪。

那是什么？这是一种新的编程语言，叫做 PHP。你可以用它来建立动态网站。
你用*动态*是什么意思？

没有说话，他写了这样的东西:

```
echo '<ul>';
for($i=0; $i<1000; $i++){
    echo '<li>Item number ' . $i . '</li>';
}
echo '</ul>'; 
```

并重新加载页面(他使用 [EasyPHP](https://www.easyphp.org/) 作为本地服务器)。

一千个`li`元素在飞行中创造出来。一千。如果用静态 HTML 来写，我会花一些时间。我想，更好，我需要学习如何做到这一点！

我将 EasyPHP 安装程序复制到一些随机的软盘上，还有一些 PHP 文件可以学习。那天晚上，我写了我的第一个 *Hello，World* 和一个很棒的网站，它的标题、侧边栏、主要内容和页脚都被分割成单独的文件，并通过`include`获取。我感觉很强大！

* * *

*快进五年*

* * *

我在上大学，找到了一份网页开发的兼职工作。我对我将要学习的所有东西感到兴奋。他们告诉我，我需要将一个应用程序从 PHP4 迁移到 PHP5。当然，我对此毫无准备:

```
//UPDATE LAST ACTION
$DB->Execute("UPDATE users SET last_action=NOW() WHERE id=".$_SESSION['fw_userid'] );

//CHECK FOR EXPIRED PASSWORD
if ($CONF['days_cpw'] > 0)
{
    list($yy,$mm,$dd) = explode("-",$_SESSION['user']['last_pw_change']);
    $olddata = mktime(2,0,0,$mm,$dd,$yy);
    $span = time() - $olddata;
    $days_passed = $span / (60*60*24);
    if ($days_passed > $CONF['days_cpw'] && !strstr($_SERVER[SCRIPT_NAME],"mod_user") && !strstr($_SERVER[SCRIPT_NAME],"calendar_iframe")  && !strstr($_SERVER[SCRIPT_NAME],"cron_web_start") )
    {
            header("location: ".$CONF['url_base'].$CONF['dir_modules'].'admin/pages/mod_user.php?id='.$_SESSION['fw_userid'].'&action=cp');
    }
}

//Template inizialization
require_once ("template.php");

// Load Modules configuration in memory
load_modules_config();

//Include permission manager
require_once ("permission.php"); 
```

当然 PHP4 不是问题...这是一次艰难的旅程，但是很有趣！

* * *

*快进十五年*

* * *

我想尝试一个简单的 web 应用程序的新想法。我`cd`到一个文件夹中，创建一个 PHP 文件。

然后`php -S localhost:8000 -t public/`将为我开发一个服务器。

我需要读取一些 CSV 文件:`composer require league/csv`这是我下载一个健壮的 CSV 处理库所需要做的。

因为我想确保我的应用程序是健壮的，所以让我们安装一个测试框架:`composer require --dev phpunit/phpunit ^8`。

我不喜欢到处使用`require_once`:幸运的是 Composer 免费提供了一个 [PSR-4 兼容的自动加载器](https://www.php-fig.org/psr/psr-4/)。

现在，让我们试着为此创建一个 API:我可以从多个 [PSR-15 服务器请求处理器](https://www.php-fig.org/psr/psr-15/)中进行选择，这些处理器可以消化 [PSR-7 HTTP 消息](https://www.php-fig.org/psr/psr-7/)，并且只关注我的业务逻辑。

* * *

## 工具&资源

如果你想知道更多，这里有几个资源和工具帮助我学习更多的语言。

### PHP:正确的方法

如果你可以选择一个资源来了解现代 PHP，那么 [PHP:正确的方法](https://phptherightway.com/)就是这个。

这不是语言的问题，而是生态系统和这些年来出现的最佳实践的问题。

绝对必读。

### 通过 Composer 进行依赖管理

[![Composer logo](img/41fc8e7254aa61a0a51a0fa66b2a3e1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A4KfpW2d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://giustino.blog/wp-content/uploads/2019/07/logo-composer.png)

我可以毫不夸张地说，Composer 是 PHP 发生的最好的事情之一。我总是发现 PEAR 使用起来很混乱，而 Composer 是即时且无处不在的。

当然，没有包来管理的依赖管理器有点没用，所以我还需要提到 [Packagist](https://packagist.org/) ，一个(几乎)所有 PHP 库都由 Composer 上传和获取的存储库。

我认为 Composer 在最近从单一框架到库组成的转变中起了很大的作用

一些关于这个主题的精彩读物:

*   [没有框架的现代 PHP](https://kevinsmith.io/modern-php-without-a-framework)作者[凯宾·史密斯](https://twitter.com/_kevinsmith)
*   创建自己的 PHP 框架 - Symfony docs

### 现代 PHP

[![Modern PHP book cover](img/66c0a4c3aae78e3ab299622da40256da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jS3dzScR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://giustino.blog/wp-content/uploads/2019/07/modern-php-cover.jpg)

即使[乔希·洛克哈特](https://twitter.com/codeguy)的这本书有点旧，它在解释 PHP 和其他最佳实践中引入的“新”特性方面做得很好。这是对 PHP 的一个很好的补充:正确的方法。

### Psysh

作为 Python 开发人员，我最怀念的是 iPython REPL。发现 [Psysh](https://psysh.org/) 后，我的怀旧之情渐渐淡去。

它是 PHP 最好的 REPL，它的`doc`特性让我每次都可以不用离开终端就可以检查 PHPdoc。

[![The doc command for array_map](img/6243e112f013919fdde5c30a4a3f6f08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O9ZByyL4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://giustino.blog/wp-content/uploads/2019/07/psysh-doc-command.png)

### 测试

如果不提到一些与测试相关的资源，这样的列表是不完整的。但这将是一个非常明显的问题。

我还是觉得就 PHP 而言，最好的起点是 [PHPUnit 手册](https://phpunit.readthedocs.io/en/8.2/)。

然后，如果你想学习更多关于测试和 TDD 的知识，这些是我的主要建议:

*   测试指导下的面向对象软件开发:关于软件设计和 TDD 以及测试的最佳书籍之一，
*   [测试驱动开发示例](https://www.amazon.it/Test-Driven-Development-Example-Kent-Beck/dp/0321146530):如果你对 TDD 很认真的话，这是一本必读的书，
*   Web 测试人员之道:如果你对测试一无所知，这是一个温和的介绍。

### 在 PHP 中实现遗留应用的现代化

[![Modernizing Legacy applications with PHP book cover](img/a2644379fcad8e5f5d1acdc1e29b9da4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GPEWGQ9o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://giustino.blog/wp-content/uploads/2019/07/modernizing-legacy-applications-php-book-cover.png)

我们都经历过。在面试中，你被推销了未来科技，一旦你到了新的工作岗位，你面对的是一大堆漏出来的泥巴。你只想逃避。

由 [Paul M. Jones](http://paul-m-jones.com/) 撰写的《用 PHP 实现遗留应用程序的现代化》将帮助你一步一步地理清这些混乱。写得真好！。

* * *

这些是我最喜欢的关于现代 PHP 的资源:我错过了什么吗？你呢？