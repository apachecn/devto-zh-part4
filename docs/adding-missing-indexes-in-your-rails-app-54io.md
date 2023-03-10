# 在 Rails 应用程序中添加缺失的索引

> 原文：<https://dev.to/drbragg/adding-missing-indexes-in-your-rails-app-54io>

对你的应用程序来说，索引你的数据库是一个重要的但经常被忽视的性能提升。

Rails 使得在移植中用`add_reference`方法索引模型关系变得非常容易。`add_reference`方法不仅会在父表上创建一个 _id 列，还会为 _id 添加一个索引。

如果你需要添加一个索引到现有的关系中，你可以使用`add_index`方法。

如果你没有从一开始就使用`add_reference`或手动索引你的关系的习惯，找到你所有丢失的索引会是一件痛苦的事情。幸运的是，在我寻找最简单或最快的做事方法的无限追求中(也就是我超级懒)，我偶然发现了一个可以找到任何缺失索引的脚本。

从 rails 控制台内部运行以下命令:

```
c = ActiveRecord::Base.connection

c.tables.collect do |t|  
  columns = c.columns(t).collect(&:name).select {|x| x.ends_with?("_id" || x.ends_with("_type"))}
  indexed_columns = c.indexes(t).collect(&:columns).flatten.uniq
  unindexed = columns - indexed_columns
  unless unindexed.empty?
    puts "#{t}: #{unindexed.join(", ")}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

鸣谢:[http://tomafro . net/2009/09/quick-list-missing-foreign-key-indexes](http://tomafro.net/2009/09/quickly-list-missing-foreign-key-indexes)(目前不工作，因此有此贴)

将返回的是缺少索引的表和其中的列的列表。