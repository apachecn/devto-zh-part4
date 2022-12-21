# Ruby 中的继承钩子方法——以及更多的育儿经验

> 原文：<https://dev.to/appsignal/the-inherited-hook-method-in-ruby-and-more-parenting-lessons-5ch7>

孩子们和家长们，咳咳 Rubyists 们，你们好。在之前的一篇文章中，我们深入研究了祖先链。在今天的帖子中，我们将深入探讨养育和遗传。我们将探索继承钩子方法，并研究如何防止继承。

## 当孩子继承 101:继承钩子法

让我们先来看看亲子关系是如何声明的。当一个类被声明为另一个类的父类时，Ruby 提供了一种简洁的交互方式。

```
class Parent
  def self.inherited(subclass)
    puts "#{subclass} inherits from Parent"
  end
end

class Child < Parent
end 
```

运行这段代码会打印出“子从父继承”，因为当`Child`类从`Parent`继承时会调用`Parent.inherited`方法。注意，在我们的例子中，这个方法将子类作为参数— `Child`。这种机制允许您与父类交互来定义一组行为，前提是它是继承的。在这个上下文中，行为是指修改、定义或删除继承类或被继承类上的变量、方法和常量。

现在，让我们动态地定义`parent_name`方法:

```
class Parent
  def self.inherited(subclass)
    subclass.define_method :parent_name do
      "Daddy"
    end
  end
end

class Child < Parent
end

Child.new.parent_name # => "Daddy" 
```

这里，我们在继承了`Parent`类的`Child`类上定义了一个方法，但是没有将该方法直接添加到`Parent`类中。相反，它只在另一个类从`Parent`继承时定义。

好了，我们已经讨论了理论，现在让我们看看一个 Rubyist 生活中更现实的例子。

## 防止阶级继承

在 Ruby on Rails 中，数据库迁移由`ActiveRecord::Migration`类处理。让我们试着直接继承这个类。

```
class AddFirstnameToUsers < ActiveRecord::Migration
end

# => StandardError (Directly inheriting from ActiveRecord::Migration is not supported..) 
```

出现这个错误是因为 Ruby on Rails 提供了一种机制，阻止我们继承这个类。那么为什么 Ruby on Rails 要实现这种机制呢？

迁移与 Ruby on Rails 的特定版本紧密相关。事实上，这个类提供的 API 可以在两个版本之间稍微改变。因此，为了避免在升级 Ruby on Rails 时中断迁移，框架会强制您选择特定版本的 ActiveRecord::Migration 类。这确保了迁移的顺利进行。

```
class AddFirstnameToUsers < ActiveRecord::Migration[4.2]
end 
```

在上面的例子中，我们的迁移耦合到 Ruby on Rails 4.2 版本提供的`ActiveRecord::Migration` API。因此，即使我们将应用程序升级到版本 5.0，我们的迁移仍然可以顺利运行，因为它们仍然可以在版本 4.2 的`ActiveRecord::Migration` API 上运行。

## 防止继承的工作原理

既然我们理解了**为什么**遗传在这里被阻止，让我们看看**这个`ActiveRecord::Migration`如何**阻止遗传。所有的逻辑都在`ActiveRecord::Migration.inherited`方法中定义。

```
class AddFirstnameToUsers < ActiveRecord::Migration[4.2]
end 
```

当我们的`AddFirstnameToUsers`类从`ActiveRecord::Migration`继承时，`ActiveRecord::Migration.inherited`钩子方法被调用。

```
module ActiveRecord
  class Migration
    def self.inherited(subclass) #:nodoc:
      super
      if subclass.superclass == Migration
        raise StandardError, "Directly inheriting from ActiveRecord::Migration is not supported. " \
          "Please specify the Rails release the migration was written for:\n" \
          "\n" \
          "  class #{subclass} < ActiveRecord::Migration[4.2]"
      end
    end
  end
end 
```

正如我们所看到的，这个钩子方法检查子类(`AddFirstnameToUsers`)是否直接继承自`ActiveRecord::Migration`。如果是这样，就会引发错误。这是控制继承的完美切入点。

## 结论

今天，我们讨论了遗传和防止遗传的基础知识。我们讨论了继承的钩子方法，并且看到了它在与继承/继承的类动态交互时是如何的方便。

在现实世界中，玩继承的时候要小心。移除或重写现有方法或类时要非常小心。这可能会导致一些不必要的副作用。孩子们可能不再叫你爸爸。

好了，今天的帖子到此结束！