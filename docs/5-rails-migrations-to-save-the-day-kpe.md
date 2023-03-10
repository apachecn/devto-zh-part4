# 拯救世界的 5 种 Rails 迁移模式

> 原文：<https://dev.to/jasterix/5-rails-migrations-to-save-the-day-kpe>

提前道歉，因为这篇文章的格式有点古怪。最初的目标是有一个编号的列表，但是没有成功。

然而，每个迁移都建立在前面的基础上，所以我希望它能帮助您理解设置 Rails 迁移的流程。

创建新的模型用户

*   rails g 模型用户:姓名:城市

```
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :name
      t.string :city

      t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

将用户表重命名为帐户

*   rails g 迁移重命名用户帐户

```
class RenameAccountsToTwitterAccounts < ActiveRecord::Migration[5.2]
  def change
    rename_table :users, :accounts
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

向帐户表中添加新列

*   rails g 迁移 AddEmailToAccounts 电子邮件:string

```
class ChangeAccountsTable < ActiveRecord::Migration[5.0]
  def change
    add_column :accounts, :email, :string
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

从数据库中删除帐户表

*   rails g 迁移卸载

```
class DropAccounts < ActiveRecord::Migration[5.2]
  def change
    drop_table :accounts
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

撤消您最近的更改

*   rails db:回滚