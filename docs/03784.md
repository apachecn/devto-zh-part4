# Django - DB bulk_create()

> 原文：<https://dev.to/serhatteker/django-db-bulkcreate-1cb8>

## bulk_create()

从 [Django 文档](https://dev.tobulk_create):

> 该方法以有效的方式将提供的对象列表插入到数据库中(通常只有一个查询，不管有多少个对象):

因此，与其以低效的方式将数据一个接一个地插入数据库，不如使用这种方法。

方法 api 详细信息:

```
bulk_create(objs, batch_size=None, ignore_conflicts=False) 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
>>> MyModel.objects.bulk_create([
...     MyModel(title='This is a test'),
...     MyModel(title='This is only an another test'),
... ]) 
```

Enter fullscreen mode Exit fullscreen mode

`batch_size`参数控制在单个查询中创建多少个对象。默认情况下，在一个批处理中创建所有对象，除了 SQLite，默认情况下，每个查询最多使用 999 个变量。

在支持它的数据库上(除了 PostgreSQL < 9.5 和 Oracle 之外的所有数据库)，将`ignore_conflicts`参数(该参数是在`Django 2.2`中添加的)设置为`True`，告知数据库忽略插入任何不满足约束(如重复的唯一值)的行的失败。启用该参数将禁止在每个模型实例上设置主键(如果数据库通常支持的话)。

另一个使用`python list comprehension`创建多 db 模型对象的例子:

```
objs = [
    Message(
        name=e.name,
        subject=e.subject,
        content=e.content,
        sender_name=e.sender_name
    )
    for e in query_list
]
msg = Message.objects.bulk_create(objs=objs)

# or use it like below but it is better to explicitly
# assign the keyword arguments as above
# msg = Message.objects.bulk_create(objs) 
```

Enter fullscreen mode Exit fullscreen mode

**重要**:

请查看[文档](https://dev.tobulk_create)中该方法的警告数量。如模型的`save()`方法不会被调用，`pre_save`和`post_save`信号不会被发送。

全部完成！