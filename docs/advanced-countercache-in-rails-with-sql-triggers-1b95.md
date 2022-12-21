# 具有 SQL 触发器的 Rails 中的高级 counter_cache

> 原文：<https://dev.to/michalbryxi/advanced-countercache-in-rails-with-sql-triggers-1b95>

Ruby on Rails 有一个很有用的小特性，叫做[计数器 _ 缓存](https://guides.rubyonrails.org/association_basics.html#options-for-belongs-to-counter-cache)。在一个场景中，模型`Author`与模型`Book`有关系`hasMany`，当您通过活动记录(AR)模型操作数据库时，rails 将在列`authors.books_count`中保存每个给定作者各自的图书数量。

## 问题

这对于简单的场景来说很好。但是我的要复杂得多。想象以下模型:

```
# models/visit.rb

class Visit < ActiveRecord::Base
  has_many :sells

  def amount
    sells.sum("(sells.price * sells.count) * (1 - sells.discount / 100)")
  end
end 
```

```
# models/sell.rb

class Sell < ActiveRecord::Base
  belongs_to :visit
end 
```

1.  每次向`Visit`发出请求时，都会读取属性`amount`。并可大批量向`Visit`提出要求。因此，从**性能**的角度来看，将那个值**缓存到某个地方**是有意义的。
2.  我需要计算出的值立即是正确的，所以将计算卸载到 [active_job](https://edgeguides.rubyonrails.org/active_job_basics.html) 不是一个选项。
3.  我的模型上有更多的属性需要这些类型的计算，它们通常比这个例子更复杂。
4.  Rails native `counter_cache`只能跟踪记录的`count()`。对于更复杂的计算，有[反文化](https://github.com/magnusvk/counter_culture)宝石，但对于这一个，下一点的所有问题也适用。
5.  使用 AR 中的`before_save`到[来保存计算字段](https://russt.me/2018/06/saving-calculated-fields-in-ruby-on-rails-5/)是完全可能的，但是我有一些担心:
    *   一些计算逻辑相当复杂，我不确定是否有可能用 AR 来表达只需要一个 SQL 查询的所有内容。
    *   即使有可能在一个 SQL 查询中完成所有事情，它仍然是从数据库到应用程序服务器的往返行程。所有的计算[都存在于一个事务](https://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html#module-ActiveRecord::Callbacks-label-Transactions)中，因此它会降低对属于该事务的行的任何其他操作的速度。

## 将工作卸载到数据库

我决定将工作交给 [(PostgreSQL)触发器](https://www.postgresql.org/docs/9.2/plpgsql-trigger.html)。这种解决方案的明显缺点是:

1.  只有数据库知道数据已经被改变。因此，如果您的应用程序需要*存储&读取*逻辑，您应该检查读取是否确实返回了新数据。
2.  如果在应用程序方面有更多的逻辑，那么您可能需要在数据库中复制这些逻辑。例如,“软删除”gem [偏执](https://github.com/rubysherpas/paranoia)总是将删除的记录留在原处，只是添加一个标记，表明记录应该被忽略。您的数据库自然不知道这一点。
3.  我个人仍然认为 SQL 过程/触发器是一种黑魔法，所以调试它们对我来说不是一个好的体验。

## C(create)U(pdate)D(elete)的问题

在 PostgreSQL 中，根据触发器操作(`TG_OP`)创建以下变量:

*   `NEW`为*创建*操作
*   `OLD`和`NEW`为*更新*操作
*   `OLD`为*，删除*

这些变量保存发生操作的行的各自状态。这对*更新*操作尤其重要。想象一个场景，代码将`visit_id`更改为`Sell`。然后`amount`需要在`OLD`和`NEW`记录上重新计算(分别是:它们各自的关系)。

如何以优雅的方式加上(Postgre)SQL 中正确的语法来解决这个问题是这篇文章的主题。

对于如何处理 *Rails* 中的触发器的好方法，我推荐一个很棒的宝石- [hair_trigger](https://github.com/jenseng/hair_trigger) 。gem 的工作方式如下:您只需将触发器的 **SQL** 存储在各自的**模型**中，每次代码发生变化 *hair_trigger* 都会生成迁移，删除旧的触发器并创建一个更新的触发器。

通过这种方法，你可以很容易地看到你的*模型* / *表*的“附加逻辑”。并且有权不关心如何在数据库中加载触发器。

## 显示代码

```
# models/visit.rb

class Visit < ActiveRecord::Base
  has_many :sells

  def amount
    sells.sum("(sells.price * sells.count) * (1 - sells.discount / 100)")
  end
end 
```

```
# models/sell.rb

class Sell < ActiveRecord::Base
  belongs_to :visit

  trigger.after(:insert, :update, :delete)
         .name("after_sells")
         .declare("var_curr sells; var_amount decimal; var_curr_arr sells[];") do
    <<-SQL CASE TG_OP
        WHEN 'DELETE' THEN
          var_curr_arr := ARRAY[OLD];
        WHEN 'UPDATE' THEN
          var_curr_arr := ARRAY[OLD, NEW];
        WHEN 'INSERT' THEN
          var_curr_arr := ARRAY[NEW];
      END CASE;

      FOREACH var_curr IN ARRAY var_curr_arr
      LOOP

        var_amount := (SELECT COALESCE(SUM((sells.price * sells.count) * (1 - sells.discount / 100)), 0) FROM sells WHERE sells.visit_id = var_curr.visit_id;

        UPDATE visits SET amount = var_amount WHERE visits.id = var_curr.visit_id;

      END LOOP; SQL
  end
end 
```

这是那里正在发生的事情:

1.  基于`TG_OP`操作，我们将创建数组`var_curr_arr`,其中将填充已更改的行。
2.  然后我们将`FOREACH`在这个数组上。实际上，最多会有两个元素，但是允许我们[干燥](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)我们的代码。
3.  最后，在`LOOP`中，我们将进行相应的更新。

在这之后，我们只需要让 *hair_trigger* 基于我们的触发器
的更新代码生成新的数据库迁移

```
rake db:generate_trigger_migration 
```

然后简单地运行 Rails 迁移:

```
rake db:migrate 
```

现在您可以继续，*创建/更新/删除*销售*的*串，并查看各个*访问*的*金额*是否已更新。

## 备注

*   这里的`COALESCE(..., 0)`技巧是为了确保我们不会遇到空的问题。
*   飞溅照片致谢:[克里斯·贾维斯](https://unsplash.com/@crissyjarvis)。