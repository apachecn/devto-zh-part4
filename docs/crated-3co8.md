# 跟踪

> 原文：<https://dev.to/pshouse/crated-3co8>

# 一个有用的盒子

#### 用 Python、SQLite、peewee-ORM 制作

不要让华而不实的用户界面欺骗了你。CrateD 在幕后有一些重要的 Python 技术。在下面的演示中，我们动态地创建 Peewee 模型，该模型在 SQLite 数据库中创建表。当用户构建数据模型时，系统记录关于模型及其字段的元数据。这些元数据存储在一个特殊的表中，以便在系统重新启动时可以动态地重建模型。
[![](img/5d49b590ff5e772ded6914a255f23875.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--54XSegws--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2lah062hry61bdgbvurt.gif)

你可以在这里看到代码并亲自尝试:

[https://repl.it/@PatrickShouse/crated-demo?lite=true](https://repl.it/@PatrickShouse/crated-demo?lite=true)