# 在 Rails 中用 attribute_method_suffix 为所有属性附加一个方法

> 原文：<https://dev.to/n350071/append-a-method-to-all-attributes-with-attributemethodsuffix-in-rails-2ngb>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的 Rails 笔记

### n350071🇯🇵9 月 11 日 193 分钟阅读

#rails](/n350071/my-rails-note-47cj)

## 👍您可以通过一次编写向每个属性添加相同的逻辑方法

```
class Person < ActiveRecord::Base
  attribute_method_suffix '_changed?'
  attribute_method_suffix '_short?'

  private
    def attribute_changed?(attr)
      ...
    end
    def attribute_short?(attr)
      send(attr).length < 10
    end
end

person = Person.find(1)
person.name_changed?    # => false
person.name = 'Hubert'
person.name_changed?    # => true
person.name_short?      # => true 
```

📚[属性 _ 方法 _ 后缀](https://apidock.com/rails/ActiveRecord/AttributeMethods/ClassMethods/attribute_method_suffix)