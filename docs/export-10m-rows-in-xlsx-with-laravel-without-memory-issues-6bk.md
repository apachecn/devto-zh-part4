# 使用 Laravel 在 XLSX 中导出超过 1000 万行，没有内存问题

> 原文：<https://dev.to/rap2hpoutre/export-10m-rows-in-xlsx-with-laravel-without-memory-issues-6bk>

*TL；DR:这篇文章介绍了 FastExcel for Laravel，给出了 PHP 生成器的一个快速概述，最后展示了如何使用它们从集合中生成 Excel 文件，同时保留内存。*

# 关于 FastExcel

[Laravel FastExcel](https://github.com/rap2hpoutre/fast-excel) 意在做 Laravel 风味的[喷口](https://github.com/box/spout)，目标是简化**进出口**。它可以被认为是一个比 Laravel Excel 更快(和内存友好)的替代品，具有不同的方法和更少的功能。两步入门。

用 composer 安装:

```
composer require rap2hpoutre/fast-excel 
```

Enter fullscreen mode Exit fullscreen mode

然后输出一个**型号**或者一个**系列**到`XLSX`、`CSV`或者`ODS` :

```
fastexcel($collection)->export('file.xlsx'); 
```

Enter fullscreen mode Exit fullscreen mode

关于[项目主页](https://github.com/rap2hpoutre/fast-excel)的 [`README`](https://github.com/rap2hpoutre/fast-excel/blob/master/README.md) 的更多信息。

# 发电机

[生成器](https://www.php.net/manual/en/language.generators.syntax.php)是很多年前在 PHP 5 中引入的。生成器函数看起来就像一个普通的函数，除了它不是返回值，而是由生成器*产生*值。然后可以迭代生成器函数。

这种函数的目标之一是在不构建数组的情况下对数据进行*惰性迭代*。因此，在处理大型数据集时，它可以节省内存。引用 [PHP 文档](https://www.php.net/manual/en/language.generators.overview.php):

> 生成器允许您编写使用 foreach 迭代一组数据的代码，而无需在内存中构建数组，这可能会导致您超出内存限制，或者需要大量的处理时间来生成。

假设你有一个数据库中有超过 1000 万条目的`User`模型，你想在你的代码中迭代它，而不是仅仅调用`User::all()`，你可以使用一个生成器:

```
function usersGenerator() {
    foreach (User::cursor() as $user) {
        yield $user;
    }
}

$users = usersGenerator();
foreach($users as $user) {
    // Do something with each user without hitting memory limit
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的示例运行查询，只逐个获取用户。它只使用加载 1 个用户`N`次所需的内存。

# 使用 FastExcel 和生成器导出大型数据集

从 v1.3.0 开始， [FastExcel](https://github.com/rap2hpoutre/fast-excel) 接受发电机功能作为参数。使用前面的例子，您可以将生成器传递给`fastexcel`函数:

```
function usersGenerator() {
    foreach (User::cursor() as $user) {
        yield $user;
    }
}

// Export consumes only a few MB, even with 10M+ rows.
$users = usersGenerator();
fastexcel($users)->export('test.xlsx'); 
```

Enter fullscreen mode Exit fullscreen mode

[FastExcel](https://github.com/rap2hpoutre/fast-excel) 在使用 generator 的时候在内部一行一行的创建，所以不会消耗额外的内存。这将是一个很长的过程，所以确保它不会碰到`max_execution_time`(您可以使用队列、任何异步技术、增加最大执行时间，或者甚至从 CLI 运行它)。尽管如此，您的服务器不会因为一次导出而耗尽所有内存。

因此，多亏了生成器，你现在可以用一个 Laravel 项目中的几行代码将数千个模型导出到`XLSX`、`CSV`和`ODS`文件中。

在知识库中了解更多关于图书馆的信息:[https://github.com/rap2hpoutre/fast-excel](https://github.com/rap2hpoutre/fast-excel)