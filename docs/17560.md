# SQLAlchemy 中的显式联接

> 原文：<https://dev.to/engelmav/explicit-joins-in-sqlalchemy-1fkh>

SQLAlchemy 中的显式联接！这意味着我们在 ORM 查询本身中*向*展示什么是连接条件*，而不是内置在你的模型类中。*

下面是带有显式连接的 ORM 查询:

```
 join_results = (
        db.session.query(TableA).filter_by(table_a_col=some_var)
            .join(TableB, TableB.table_a_id == TableA.id)
            .all()
    ) 
```