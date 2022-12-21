# Rails 5.2 回调中的变化

> 原文：<https://dev.to/jetrockets/rails-5-2-changes-in-callbacks-40io>

在 5.1 版本中，您可能会在 after_save 回调中看到弃用警告(与 ActiveRecord::Dirty 模块中的更改相关)。
但是从 5.2 开始应用这些变化。

例如，我将使用 Rails 4.2.11 和 Rails 5.2.3，并用 email 属性对用户建模。咱们来做:

```
u = User.new(email: 'old@domain.com')
u.save
u.email = 'new@domain.com'
u.save 
```

Enter fullscreen mode Exit fullscreen mode

并查看上次保存时的 after_save 回调。

### 1。属性 _ 已更改？

**铁轨 4**

```
> email_changed?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

**轨道 5.2**

```
> email_changed?
=> false 
```

Enter fullscreen mode Exit fullscreen mode

但是可以用`saved_changes?`

```
> saved_change_to_email?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

### 2。变了？

**铁轨 4**

```
> changed?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

**轨道 5.2**

```
> changed?
=> false 
```

Enter fullscreen mode Exit fullscreen mode

但是可以用`saved_changes?`

```
> saved_changes?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

### 3。变化

**铁轨 4**

```
> changes
=> {"email"=>["old@domain.com", "new@domain.com"]} 
```

Enter fullscreen mode Exit fullscreen mode

**轨道 5.2**

```
> changes
=> {} 
```

Enter fullscreen mode Exit fullscreen mode

但是可以用`saved_changes`

```
> saved_changes
=> {"email"=>["old@domain.com", "new@domain.com"]} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。以前的 _ 更改

**铁轨 4**

```
> previous_changes
=> {"email"=>[nil, "old@domain.com"]} 
```

Enter fullscreen mode Exit fullscreen mode

**Rails 5.2**
现在，这个方法返回刚刚保存的更改(像`saved_changes`)。

```
> previous_changes
=> {"email"=>["old@domain.com", "new@domain.com"]} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法没有替代品。