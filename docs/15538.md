# Rails 中的安全导航与尝试(第 1 部分:基本差异)

> 原文：<https://dev.to/jahboo/safe-navigation-vs-try-in-rails-part-1-basic-differences-183a>

有一些防止类似`undefined method for nil:NilClass`的错误的方法。

*   Rails 方法`try(...)`
*   安全航行操作员(`&.`)
*   逻辑运算符`&&`(与)

下面是这些选项的样子:

```
user.try(:company).try(:name) 
```

Enter fullscreen mode Exit fullscreen mode

```
user&.company&.name 
```

Enter fullscreen mode Exit fullscreen mode

```
user && user.company && user.company.name 
```

Enter fullscreen mode Exit fullscreen mode

但是也有一些不同。

**1。如果模型用户没有关系`compppany`(可能只是错别字或重命名的模型关系/属性):**

```
user.try(:compppany).try(:name)
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

你将收到`nil`并且从未被人知道这个错别字。

```
user&.compppany&.name
=> NoMethodError: undefined method `compppany' for #<User:0x000000123456789> 
```

Enter fullscreen mode Exit fullscreen mode

和

```
user && user.compppany && user.compppany.name
=> NoMethodError: undefined method `compppany' for #<User:0x000000123456789> 
```

Enter fullscreen mode Exit fullscreen mode

看起来更好！

**2。如果模型用户有关系公司，但公司是`false`。`User.new(company: false)`:**T4】

```
user.try(:company).try(:name)
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

```
user&.company&.name
=> NoMethodError: undefined method `name' for false:FalseClass 
```

Enter fullscreen mode Exit fullscreen mode

安全航行得到认可`false`。厉害！

```
user && user.company && user.company.name
=> false 
```

Enter fullscreen mode Exit fullscreen mode

嗯，看起来不像我们想要的。

**3。性能**
阅读第二部分[安全航行 vs 试运行(第二部分:性能)](http://jetrockets.pro/til/posts/nswsheigth-safe-navigation-vs-try-in-rails-part-2-performance)