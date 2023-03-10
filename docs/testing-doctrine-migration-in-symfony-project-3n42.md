# Symfony 项目中的测试原则迁移

> 原文：<https://dev.to/alphpkeemik/testing-doctrine-migration-in-symfony-project-3n42>

1.  结账到主支行
    `git checkout master`。

2.  更新数据库以匹配当前原则模式，同时删除多余的表格
    `php bin/console d:s:u --force --complete`，其中`d:s:u`是由 Symfony 控制台提供的`doctrine:schema:update`的自动简短版本。

    > NB！它从数据库中删除所有不在教条模式中的表！
    > 如果您想保留一些表(迁移表也可以被删除)，那么运行`php bin/console d:s:u --dump-sql --complete`
    > 来显示多余的表，手动删除它们，然后运行`php bin/console d:s:u --force`来使模式与主模式保持一致。

3.  添加所有迁移`php bin/console d:m:v --all --add -n`
    ，其中`d:m:v`是由 Symfony 控制台提供的`doctrine:migrations:version`的自动简称。

4.  签出到您的功能分支`git checkout KEY-123`

5.  运行迁移`php bin/console d:m:m -n`，其中`d:m:m`是`doctrine:migrations:migrate`的自动简称，由 Symfony 控制台提供。