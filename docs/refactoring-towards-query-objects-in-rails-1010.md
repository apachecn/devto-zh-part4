# Rails 中面向查询对象的重构

> 原文：<https://dev.to/victorhazbun/refactoring-towards-query-objects-in-rails-1010>

### 查询对象

它们存储复杂的 SQL 查询、数据聚合和过滤方法。

### 问题

假设你有以下观点。

```
class PostsController < ApplicationController
  def index
    @posts = Post
      .joins('LEFT OUTER JOIN users ON users.id = posts.author_id')
      .where(published: true)
      .where('view_count > ?', params[:min_view_count])
      .where('users.first_name LIKE ?', "#{params[:author_name]}%")
end 
```

Enter fullscreen mode Exit fullscreen mode

对于一个控制器来说有很多代码，所以让我们重构它以使用一个查询对象。

### 后置宾语

```
class PostsQuery
  def self.call(params = {}, relation = Post.all)
    relation = published(relation)
    relation = minimal_view_count(relation, params[:view_count])
    relation = author_first_name_like(relation, params[:first_name])
    relation
  end

  def self.published(relation)
    relation.where(published: true)
  end

  def self.minimal_view_count(relation, view_count)
    return relation unless view_count.present?
    relation.where('view_count > ?', view_count)
  end

  def self.author_first_name_like(relation, first_name)
    return relation unless first_name.present?
    with_authors(relation)
      .where('users.first_name LIKE ?', "#{first_name}%")
  end

  def self.with_authors(relation)
    relation.joins('LEFT OUTER JOIN users ON users.id = posts.author_id')
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 实现

很好，现在让我们看看控制器是什么样子。

```
class PostsController < ApplicationController
  def index
    @posts = PostsQuery.call(params)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 最后的想法

不仅看起来更好，测试这种方法也更简单。

这不仅适用于 Rails 应用程序，这种模式可以用在 Ruby 的任何地方。