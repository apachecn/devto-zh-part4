# 使用 Ridgepole 简化数据库模式管理

> 原文：<https://dev.to/masakazutakewaka/easier-database-schema-management-with-ridgepole-9n5>

# 与团队共享 ActiveRecord 的模式迁移

当多个团队在同一个 Rails 应用程序上工作并快速部署时，这是非常困难的。人们创建迁移，却不知道其他团队同时也在创建迁移。
您不能忽略它们，因为在 Rails 中不能有多组迁移。您只能有一组迁移。
因此，当您提交迁移时，您必须跟踪其他团队提交给存储库的迁移。
正因为如此，不拖慢其他团队的后腿并不容易。

# 脊极

[https://github.com/winebarrel/ridgepole](https://github.com/winebarrel/ridgepole)

Ridgepole 是一个数据库模式管理器。
您将 Ruby DSL 写入“Schemafile ”,并使用 CLI 上的`ridgepole --apply`将其应用到您的数据库。

使用 Ridgepole，您不需要在每次想要对数据库中的表添加一些更改时都创建迁移文件。

Ridgepole 比较 Schemafile 中的表和数据库中的实际表。
然后应用与`ALTER TABLE`的差异(如果有)。

它还支持“预演”。基本上你可以得到将要执行的 SQL，而不需要实际执行它。
如果你想对 ActiveRecord 的模式迁移做类似的事情，你需要迁移迁移，然后回滚。
(之前有一个支持预演的 ActiveRecord 的 PR，但是没有合并。https://github.com/rails/rails/pull/31630

# 与 Rails 集成

有一些宝石。

[https://github.com/space-pirates-llc/ridgepoler-rails](https://github.com/space-pirates-llc/ridgepoler-rails)

[https://github.com/masakazutakewaka/rafter](https://github.com/masakazutakewaka/rafter)
(这是我的宝石。我希望你去看看😁欢迎公关🙏)

谢谢你把这个看完！！

### 参考文献

*   [https://techlife.cookpad.com/entry/2014/08/28/194147](https://techlife.cookpad.com/entry/2014/08/28/194147)