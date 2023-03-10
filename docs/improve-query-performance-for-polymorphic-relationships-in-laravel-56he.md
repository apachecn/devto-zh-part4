# 提高 Laravel 中多态关系的查询性能

> 原文：<https://dev.to/roelofjanelsinga/improve-query-performance-for-polymorphic-relationships-in-laravel-56he>

[!["Start sprint race"](img/be2bb97192ac88fc464ef7be8958529a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eYjVIeP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0dvmysrxxskreqqz9shg.jpeg)

# 提高 Laravel 中多态关系的查询性能

这篇文章是写给那些在 Laravel 中使用多态关系并注意到一些性能问题的开发者的。这篇文章假设你正在使用 MySQL 或 PostgreSQL。如果你还在读这篇文章，这意味着你正处于这种情况，正因为如此，我不会再耽搁你了。

## 综合指数

查询性能与被标记为索引的列有很大关系。主键，通常是 id 字段，
很可能被标记为索引，这意味着您可以非常快速地在数据库表中查询具有匹配 id 的记录。但是，当您不在感兴趣的字段上使用索引时，数据库必须计算您的查询并查看表中的所有记录，以确定它是否与您的查询匹配。
如果你在被请求的字段上使用索引，数据库已经确切知道你想要什么，并且能够
非常快速地返回被请求的记录。

这就是我们要为多态关系做的。如果您的数据库表没有 100，000 条或更多的记录，这对您并没有太大的好处。您的查询会非常快，但是您不会注意到太大的差异。在我的例子中，有问题的表有超过 400 万条记录，所以查询速度慢真的让我很吃惊，因为 400 万并不是一个很大的数字，查询应该很慢。这时我注意到*_type 和*_id 列没有被标记为索引。

那么，为什么是综合指数呢？为了查询相关的模型，您需要*_type 和*_id 列。
这两列一起形成了一个单一的关系，这就是为什么我们要为这两列的组合
创建一个单一的索引。

## Laravel 迁移

现在你明白我们在做什么了，让我们来看看代码。

首先，通过 make:migration 进行新的迁移。下面我将给出我用来在 activity_log 表上创建索引的特定迁移配置
。在这种情况下，spatie/activitylog 包中多态关系的索引并不是现成的。
从那以后，我向 GitHub 库提出了一个 Pull 请求，这个请求得到了批准。
所以任何未来的软件包用户都不会有同样的问题。

```
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateIndexesOnActivityLogs extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('activity_log', function (Blueprint $table) {
            $table->index(['subject_id', 'subject_type'], 'subject');
            $table->index(['causer_id', 'causer_type'], 'causer');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('activity_log', function (Blueprint $table) {
            $table->dropIndex('subject');
            $table->dropIndex('causer');
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看 up()方法时，可以看到传递给$this->index()的第一个参数是一个数组。这意味着我正在创建一个名为 subject 的索引，它包含 subject_id 和 subject_type 的组合。
名为 causer 的索引包含 causer_id 和 causer_type 的组合。在您迁移完这个迁移之后，
您应该会再次有非常快速的查询。

我希望这篇文章对你有用，它确实帮助我解决了一些疑问。