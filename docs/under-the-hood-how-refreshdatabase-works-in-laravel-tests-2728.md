# 引擎盖下:RefreshDatabase 如何在 Laravel 测试中工作

> 原文：<https://dev.to/daniel_werner/under-the-hood-how-refreshdatabase-works-in-laravel-tests-2728>

## 简介

为任何应用程序编写测试时，每个测试独立运行而不相互影响是至关重要的。如果您的测试使用数据库，那么每个测试都应该使用干净的数据库。我说的“干净”是指一种已知的状态，新迁移或播种的状态，不一定是空的状态。Laravel 使用 RefreshDatabase 特性提供了一种简单的方法来处理这个问题。关于特征用法的更多信息，请查看文档[这里](https://laravel.com/docs/5.8/database-testing#resetting-the-database-after-each-test)。

## 测试在 Laravel 中的生命周期

在我们深入了解刷新数据库如何工作的细节之前，让我们看一下测试的生命周期。Laravel 附带了 PHPUnit，因此测试的生命周期与 PHPUnit 测试的生命周期非常相似。我不会涵盖整个测试运行过程的细节，只坚持当前主题中有趣的点:

*   每次测试前，运行**设置**方法:
    *   它创建/刷新 laravel 应用程序
    *   **设置特性**
    *   调用 afterApplicationCreatedCallbacks、设置事件、清除 facade 的已解析实例等。
*   实际的**测试**正在运行
*   每次测试后，运行**拆卸**方法:
    *   它调用**beforeapplicationdestoredcallbacks**
    *   关闭嘲弄，重置变量等。

## 刷新数据库特征

如果测试使用 RefreshDatabase 特征， **setUpTraits** 从该特征调用 **refreshDatabase()** 方法，有趣的部分从这里开始。在您可以在内存和常规数据库中使用的测试中，根据您如何设置测试环境，它将相应地刷新数据库。

### 内存中的数据库

在内存数据库的情况下，事情非常简单，只需迁移数据库，它运行非常快，所以它可以在每次测试之前完成。很简单。

```
 protected function refreshInMemoryDatabase()
    {
        $this->artisan('migrate');

        $this->app[Kernel::class]->setArtisan(null);
    } 
```

### 常规数据库

在其他情况下，它只迁移数据库，如果它没有被迁移，例如在运行第一个测试之前。

```
 if (! RefreshDatabaseState::$migrated) {
            $this->artisan('migrate:fresh', [
                '--drop-views' => $this->shouldDropViews(),
                '--drop-types' => $this->shouldDropTypes(),
            ]);

            $this->app[Kernel::class]->setArtisan(null);

            RefreshDatabaseState::$migrated = true;
        } 
```

当数据库被迁移后，它开始一个数据库事务:

```
 foreach ($this->connectionsToTransact() as $name) {
            $connection = $database->connection($name);
            $dispatcher = $connection->getEventDispatcher();

            $connection->unsetEventDispatcher();
            $connection->beginTransaction();
            $connection->setEventDispatcher($dispatcher);
        } 
```

并在事务被**回滚**的情况下注册一个**beforeApplicationDestroyed**回调。这些回调在**拆卸**方法中运行，所以每次测试后**。回滚确保数据库不会被测试更改，下一个测试可以在干净的数据库上运行。** 

```
 $this->beforeApplicationDestroyed(function () use ($database) {
            foreach ($this->connectionsToTransact() as $name) {
                $connection = $database->connection($name);
                $dispatcher = $connection->getEventDispatcher();

                $connection->unsetEventDispatcher();
                $connection->rollback();
                $connection->setEventDispatcher($dispatcher);
                $connection->disconnect();
            }
        }); 
```

## 潜在陷阱

虽然上面描述的方法在大多数情况下都工作得很好，但是在一些情况下，我花了几个小时来搜索、调试为什么我的测试会失败以及数据库不能正确刷新。

正如您可能会想到的，解决方案是**不要在测试中提交**打开的数据库事务，并且**避免在 mysql** 中使用导致隐式提交的语句。我犯的第二个错误是在我的一个 seeders 中运行 sql 转储，这创建了一个表，因此隐式地提交了事务。你可以在 mysql [文档](https://dev.mysql.com/doc/refman/8.0/en/implicit-commit.html)中找到更多关于隐式提交的信息。自我辩护:我知道数据库表应该用迁移来创建，种子应该用工厂来编写，但有时我们需要做非最优的事情:-)。

## 结论

为 Laravel 应用程序编写测试时，RefreshDatabase 是一个非常有用的特性，只需要在测试中小心事务提交。

希望这篇文章是有用的，如果你有任何补充，评论，请让我知道在评论部分。

帖子[在引擎盖下:RefreshDatabase 如何在 Laravel 测试中工作](https://42coders.com/under-the-hood-how-refreshdatabase-works-in-laravel-tests/)首先出现在 [42 Coders](https://42coders.com) 上。