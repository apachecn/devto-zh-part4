# Rails 提示:Render:使用以前的查询参数新建

> 原文：<https://dev.to/gaaamii/rails-tips-render-new-with-previous-query-params-34kf>

当用 Rails 编写表单时，通常使用`render :new`来表示验证错误。与`redirect_to`不同，使用`render :new`，您可以再次显示`new`页面，而无需重置表单值。这在 UX 方面很有用。

`#create`动作就像:

```
def create
  if @foo.save
    redirect_to foos_index_path
  else
    render :new
  end
end 
```

但是，当在`new`页面指定查询参数时，会出现一个问题。呈现新页面后，查询参数将会消失。如何将查询参数保存到第二次新页面？

这可以通过给表单提供查询参数来实现。

如果其新页面的形式如下:

```
= form_for @foo, url: sign_in_path do |f|
  / ... 
```

只需给 path helper 方法一个查询参数。

```
= form_for @foo, url: sign_in_path(query: @query) do |f|
  / ... 
```

`@query`由控制器的`#new`动作传递。

```
def new
  @query = params[:query]
end 
```

对我来说效果很好。