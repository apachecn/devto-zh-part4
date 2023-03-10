# 如何(不)在 Rails 中使用 unscoped

> 原文：<https://dev.to/prathamesh/how-to-not-use-unscoped-in-rails-52dc>

活动记录提供了`unscoped`来删除之前添加到模型中的所有作用域。

```
class Article
  default_scope { where(published: true) }
end

Article.all # SELECT * FROM articles WHERE published = true

Article.unscoped # SELECT * FROM articles 
```

Enter fullscreen mode Exit fullscreen mode

比方说，我想获取某个作者的所有文章，不管是发表的还是其他的。

```
author = Author.find_by(email: "prathamesh@example.com")
author.articles.unscoped 
```

Enter fullscreen mode Exit fullscreen mode

我期待给定的作者所有的文章被退回。但是有一个惊喜，我得到的不是特定作者的文章，而是数据库中的所有文章！

```
author.articles.unscoped 
# SELECT "articles".* FROM "articles" 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么发生的？

当在模型上调用`unscoped`时，它调用 [unscoped](https://github.com/rails/rails/blob/b9ca94caea2ca6a6cc09abaffaad67b447134079/activerecord/lib/active_record/scoping/default.rb#L33) 并返回该模型的一个范围，而没有任何先前的范围。

但是我们打电话给一个协会。让我们看看`unscoped`方法是在关联的什么地方定义的。

```
[5] pry(main)> author.articles.method(:unscoped)
=> #<Method: Article::ActiveRecord_Associations_CollectionProxy#unscoped>
[6] pry(main)> author.articles.method(:unscoped).source_location
=> ["/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/relation/delegation.rb",
 96] 
```

Enter fullscreen mode Exit fullscreen mode

结果，我们没有在关联对象上定义一个`unscoped`方法。相反，它只是通过各种中间对象委托给关联模型上的`unscoped`。

这些中间调用的完整堆栈跟踪如下:

```
"/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/scoping/default.rb:34:in `unscoped'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/association_scope.rb:24:in `scope'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/association_scope.rb:7:in `scope'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/association.rb:90:in `association_scope'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/association.rb:79:in `scope'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/collection_association.rb:287:in `scope'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/collection_proxy.rb:932:in `scope'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/collection_proxy.rb:1104:in `scoping'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/relation/delegation.rb:114:in `method_missing'",

 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/scoping/default.rb:34:in `unscoped'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/relation/delegation.rb:114:in `public_send'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/relation/delegation.rb:114:in `block in method_missing'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/relation.rb:281:in `scoping'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/associations/collection_proxy.rb:1104:in `scoping'",
 "/Users/prathamesh/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/relation/delegation.rb:114:in `method_missing'", 
```

Enter fullscreen mode Exit fullscreen mode

当我们在关联上调用`unscoped`时，它实际上被转换为在模型本身上调用`unscoped`。所以`author.articles.unscoped`被翻译成了`Article.unscoped`。这就是为什么我们得到所有没有作者约束的文章。

这种行为难倒了我，因为我期望它也能在关联上“正常工作”。

如果我们想`unscoped`某个作者的文章，我们需要通过文章来获取，而不是通过作者。

```
Article.unscoped.where(author: author)
# SELECT "articles".* FROM "articles" WHERE "articles"."author_id" = ? 
```

Enter fullscreen mode Exit fullscreen mode

所以下次你在代码中使用`unscoped`时，确保你没有意外地调用一个关联。

**亲提示**

Ruby 有一个调试任何程序的秘密武器。这是一个名为`method`的[方法](https://ruby-doc.org/core-2.5.3/Method.html)。您可以在任何对象上调用它，并传递方法名来获取方法对象。一旦方法对象被捕获，我们就可以询问它的[源位置](https://ruby-doc.org/core-2.5.3/Method.html#method-i-source_location)。我用它来调试这篇博文中的代码。