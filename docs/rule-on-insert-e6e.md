# 插入时的规则

> 原文：<https://dev.to/fcfn/rule-on-insert-e6e>

你好！

在我使用的一个数据库模块中有一个错误，有时它会在数据库中插入“未定义”的内容。我想创建一个规则，用另一个值替换行中的一个值。我试过这个:

```
CREATE RULE replace_undefined AS ON INSERT TO telegraf_session
    WHERE NEW.session = 'undefined'
    DO INSTEAD INSERT INTO telegraf_session VALUES(NEW.id, REPLACE(NEW.session, 'undefined', '{}'); 
```

但是在插入这个规则时，我得到了`ERROR: infinite recursion detected in rules for relation "telegraf_session"`

我做错了什么？我认为这个条件没有得到尊重，但我不知道为什么。

谢谢！