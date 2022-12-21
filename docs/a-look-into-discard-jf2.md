# 对丢弃的审视

> 原文：<https://dev.to/thealiilman/a-look-into-discard-jf2>

*原载于[ali-ilman.com/blog](//ali-ilman.com/blog)T3】*

有时，我们希望软删除应用程序中的特定记录。
什么是软删除？

> 软删除是一个术语，用于将数据库中的记录标记为已删除。这种情况的一个用例是，出于记录目的，您希望它对公众隐藏，但对管理员可见。

在 Rails 的世界里，有各种各样的方法可以做到这一点。您可以编写自己的软删除功能，也可以使用像 discard 这样的 gem。

## 一看成丢弃

[Discard](https://github.com/jhawthorn/discard) 是 John Hawthorn 创造的一种宝石，将宝石描述为*对 ActiveRecord 做正确的软删除*。gem 基本上是一个*简单的 ActiveRecord mixin，用于添加将记录标记为丢弃的约定*。

#### 设置

对于这个例子，我将创建一个应用程序，其中有作者、书籍和出版商表。
一个`Author`可以有很多`Books`，一个`Publisher`可以有很多`Authors`，一个`Publisher`可以有很多`Books`到`Authors`。

```
class Author < ApplicationRecord
  belongs_to :publisher, optional: true
  has_many :books, dependent: :nullify
end

class Book < ApplicationRecord
  belongs_to :author
end

class Publisher < ApplicationRecord
  has_many :authors, dependent: :nullify
  has_many :books, through: :authors
end 
```

将它添加到您的 gem 文件中，然后运行`bundle`。

```
gem 'discard', '~> 1.0' 
```

对于您希望可丢弃的记录，在我们的情况下，`Author`，像这样在作者模型中包含 discard。

```
class Author < ApplicationRecord
  include Discard::Model
end 
```

然后，生成一个迁移，将相关属性添加到 *authors* 表中。

```
rails generate migration add_discarded_at_to_authors discarded_at:datetime:index 
```

#### 如何丢弃一条记录

```
author = Author.first
author.discard # => true
author.discarded_at # => Sun, 01 Sep 2019 10:48:31 UTC +00:00
author.discarded? # => true 
```

#### 如何取消一条记录

```
author = Author.first
author.undiscard # => true
author.discarded_at # => nil
author.discarded? # => false 
```

#### 访问记录

丢弃为可丢弃的记录添加以下 4 种方法。

```
Author.with_discarded # an alias for Author.all
Author.kept # returns a collection of undiscarded authors
Author.discarded # returns a collection of discarded authors
Author.undiscarded # an alias for Author.kept 
```

#### 回调？！？！？！

如果需要，丢弃增加了`before_`、`around_`和`after_`回调供您使用。

```
class Author < ApplicationRecord
  ...
  before_discard { puts 'Hallo before_discard!' }
  after_discard { puts 'after_discard!' }

  before_undiscard { puts 'Waving at you before_undiscard...' }
  after_undiscard { puts 'Yay after_undiscard!' }
  ...
end
> Author.first.discard
# other logs
Hallo before_discard!
# other logs
after_discard!
=> true

> Author.first.undiscard
# other logs
Waving at you before_undiscard...
# other logs
Yay after_undiscard!
=> true 
```

然而不幸的是，`around_`回调似乎不起作用。例如，`Author.first.discard`。它会查询作者并在`around_discard`中运行代码，但不会丢弃作者。可能是创业板内部的 bug。🤔

#### 总结

丢弃去除了替代物带来的魔力，例如[偏执狂](https://github.com/rubysherpas/paranoia)和[扮演偏执狂](https://github.com/ActsAsParanoid/acts_as_paranoid)。

我在一些项目中使用过偏执狂，其中一个项目规模相当大。我记得最初让大多数模型*偏执*。我们遇到了问题，其中之一是`has_many dependent: :destroy`关联。那些依赖的记录也需要是偏执的，否则记录将被永久地破坏。😱另一个问题是，虽然我不能准确地调用它，其中一个记录有一个 has_many_ association，它正在使用集合中仍然存在的`id`搜索一个软删除的记录，但是找不到具有现有`id`的所述记录！🤯

有人可能会说丢弃违背了配置的*约定，这是 Rails 的原则之一，并且会增加代码库的冗长性。一个例子是，您必须不断地将`.kept`添加到一个模型或一个关联中，以访问一组未被丢弃的记录。但就个人而言，它很容易设置和使用。另外，与其他两个 gem 不同，discard 不会改变模型的 default_scope。我建议你放弃，以避免可能的长期痛苦。😉*

你可以在这里查看上面[的 app 的代码，在这里你可以选择不同的分支来查看用](https://github.com/thealiilman/loeschen)[丢弃](https://github.com/thealiilman/loeschen/tree/feature/soft-delete-with-discard)或者[偏执](https://github.com/thealiilman/loeschen/tree/feature/soft-delete-with-paranoia)的实现。