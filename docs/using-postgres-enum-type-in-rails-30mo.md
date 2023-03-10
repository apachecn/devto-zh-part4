# 在 Rails 中使用 Postgres 枚举类型

> 原文：<https://dev.to/diegocasmo/using-postgres-enum-type-in-rails-30mo>

上周，我写了使用主动支持关注点封装数据访问和验证的文章，其中我解释了如何使用主动支持关注点为活动记录枚举属性定义数据访问和验证规则。该属性将值映射到数据库中的整数，类似于以下示例:

```
class Project < ActiveRecord::Base
  enum status: [ :active, :archived ]
  validates :status, inclusion: { in: statuses.keys }
end 
```

Enter fullscreen mode Exit fullscreen mode

根据您试图解决的问题，这种方法可能会导致几个缺点，包括:

1.  存储在数据库中的无意义的整数值。
2.  除非整数值在迁移中受到“限制”,否则可以通过普通 SQL 绕过活动模型验证，有效地允许将属性设置为任何整数。
3.  普通 SQL 查询需要使用整数值，而不是活动记录枚举。

在这篇博文中，我将展示如何使用 Postgres enum 类型和 Rails 来避免前面提到的陷阱。

### Postgres 枚举类型

Postgres 支持枚举类型，即包含一组静态有序值的数据类型。要创建枚举类型，请使用 Postgres `CREATE TYPE`命令。在一个 Rails 项目中，生成一个迁移，如下所示`rails g migration AddStatusToProjects` :

```
class AddStatusToProjects < ActiveRecord::Migration[5.2]
  def up
    execute <<-SQL CREATE TYPE project_status AS ENUM ('active', 'archived'); SQL
    add_column :projects, :status, :project_status
  end

  def down
    remove_column :projects, :status
    execute <<-SQL DROP TYPE project_status; SQL
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

迁移创建了一个`project_status`枚举类型。接下来，它向类型为`project_status`的`projects`表中添加一个`status`列。通过使用 Postgres 枚举类型，`status`属性在数据库级别被约束为`active` | `archived`之一。最后，在环境配置文件中设置`config.active_record.schema_format = :sql`，以便数据库模式包含`project_status`枚举类型定义。

### 活动记录枚举

需要稍微修改一下`status`活动记录枚举的初始定义。简单地使用一个散列来显式地映射属性和数据库值之间的关系，如下所示:

```
class Project < ActiveRecord::Base
  enum status: { active: 'active', archived: 'archived' }
  validates :status, inclusion: { in: statuses.keys }
end 
```

Enter fullscreen mode Exit fullscreen mode

### 单元测试

使用`shoulda-matchers` gem:
可以极大地简化单元测试

```
RSpec.describe Project, type: :model do

  it { should define_enum_for(:status).
      with_values(
        active: 'active',
        archived: 'archived'
      ).backed_by_column_of_type(:enum) }

  it { should allow_values(:active, :archived).for(:status) }
end 
```

Enter fullscreen mode Exit fullscreen mode

第一个单元测试验证了`status`列是在`Project`模型中定义的，并且它的列类型是`enum`。此外，它确保`status`枚举值被正确定义为在`status`散列中指定的值。最后，最后一个测试确认了`Project`模型允许使用`:active`和`:archived`符号设置`status`属性。

### 结论

就这样了！`Project`模型为`status`属性定义并验证一个枚举，该属性被映射到数据库中有意义的字符串值。此外，`status`列在数据库级别被约束为 Postgres 枚举类型中的指定值之一。

如果你有兴趣了解更多关于 Rails 中的 enum，我强烈推荐你阅读[Ruby on Rails——如何在 5 个步骤中创建完美的 enum](https://naturaily.com/blog/ruby-on-rails-enum)。这篇由 błażej·皮丘尔撰写的博文，在过去的几周里，已经成为了一个很好的指南，帮助我提高了对 Rails 中 enum 的理解和使用。