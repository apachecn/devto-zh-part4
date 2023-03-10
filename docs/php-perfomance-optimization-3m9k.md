# PHP 性能优化

> 原文：<https://dev.to/inspector/php-perfomance-optimization-3m9k>

一旦最初的构建阶段结束，应用程序开始为真正的用户服务，那么这就是稳定性和可靠性以及开发速度发挥重要作用的时候了。

有数百种方法可以在 PHP 应用程序中进行某种性能优化，但我认为在大多数情况下，从几个重要的点开始就足够了。

在这篇文章中，我向你展示了我的清单，希望它能成为你做一些改进的起点。

## 从 PHP 7.x 开始

最新版本的 PHP 是目前最快的 PHP 版本。根据一些评论，PHP 第 7 版比 PHP 5 快得多，也安全得多。

有一个很好的向后兼容性，所以如果你的应用程序需要一些时间来适应一些代码，这将是非常值得的。

您将能够获得只花一点时间的优势，但是通过减少应用程序正常工作所需的资源，您可以在长期内将托管成本降低几个百分点。

Kinsta(世界上最好的 WordPress 主机)提供的年度基准告诉我们性能提升有多大:

[最终版 PHP 5.6、7.0、7.1、7.2 & 7.3 基准测试(2019)](https://kinsta.com/blog/php-benchmarks/)

## SQL 查询改进

我在一个项目中，后端每小时运行 40/50K 个查询。使用 ORM(在这种情况下是雄辩的),不容易知道一些 ORM 指令是否可以生成比需要的更多的查询。

每小时 40K/50K 次查询是一个出乎意料的高数字。在做了一点调查后，我修改了一行代码，使用渴望加载来预加载一个关系，以使应用程序快 10%。

```
// Slower
$books = App\Book::all();
foreach ($books as $book) {
  echo $book->author->name;
}

// Faster (using Eager Loading)
$books = App\Book::with('author')->get();
foreach ($books as $book) {
  echo $book->author->name;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 单引号胜在双引号上(也许)

使用双引号，您可以直接在字符串文本中插入变量。PHP 解析器将自动检测这些变量，将它们的值转换成可读的文本，并将它们放在适当的位置。

```
$name = 'Valerio';
echo "Hello $name"; // Output: Hello Valerio 
```

Enter fullscreen mode Exit fullscreen mode

PHP 需要更长的时间来处理双引号字符串。由于 PHP 解析器必须提前读取整个字符串来检测其中的任何变量，并将其连接起来，因此处理时间比单引号字符串要长。

使用单引号时，变量被忽略:

```
// faster echo
echo 'Valerio';

// slower echo
echo "Valerio"; 
```

Enter fullscreen mode Exit fullscreen mode

这在字符串连接的情况下是不正确的，因为它是完全相反的。

```
$name = 'Valerio';

// slower echo
echo 'Hello ' . $name;

// faster echo
echo "Hello $name"; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里查看完整的测试(感谢 Andrey Ageev 举报我):[https://www . PHP . net/manual/en/language . types . string . PHP # 120160](https://www.php.net/manual/en/language.types.string.php#120160)

单引号在服务器上更容易使用，但只适用于文字字符串(没有变量连接)。由于 PHP 不需要提前读取整个字符串，所以服务器可以更快更愉快地工作。

当你需要通过变量连接来构建一个字符串时，双引号最终会更快。

## OPcache 配置

每次 PHP 脚本被请求时，都会被解析并编译成操作码，然后在 Zend 引擎中执行。这使得 PHP 开发人员可以跳过 Java 或 C#等其他语言所需的编译步骤——您可以对 PHP 代码进行更改，并立即看到这些更改。

但是，解析和编译步骤会增加您的响应时间，并且在非开发环境中通常是不必要的，因为您的应用程序代码很少更改。

OPcache 利用了这个“字节码”的缓存，所以下次请求相同的脚本时，它不必重新编译它。这可以节省一些宝贵的执行时间，从而使你的应用程序更快。

## 为 Laravel

大部分时间在基于 Laravel 的应用程序中工作，我可以向您推荐一个方便的包，它为您提供了使用 OPcache 的 Artisan 命令。

[apps tract/laravel-op cache](https://github.com/appstract/laravel-opcache)

## 为 Symfony

在 Symfony 的官方文档中有一个很好的逐步指南，帮助我在过去优化 Symfony 的执行，包括 OPcache 配置。

[https://symfony.com/doc/current/performance.html](https://symfony.com/doc/current/performance.html)

* * *

我是 Inspector 的创建者，这是一款面向 Laravel 开发人员和团队的实时监控工具。

检查员在用户意识到问题之前发现问题，确保您的业务安全。

*试用 Inspector，免费:*[https://www . Inspector . dev](https://www.inspector.dev)