# 在 Laravel 表单请求中使用唯一验证规则

> 原文：<https://dev.to/chrisrhymes/using-the-unique-validation-rule-in-a-laravel-form-request-1bf2>

有时，您希望确保某个字段在数据库中是唯一的，这样某人就不能两次添加相同的项目，例如文章的标题。Laravel 为此提供了一些方便的工具，包括独特的验证规则，但是要充分利用它的能力，还需要对其他验证规则进行更多的配置。

在继续填充字段之前，让我们从数据库中如何定义字段开始。

## 数据库迁移

当您知道您有一个希望是唯一的字段时，例如本例中的文章标题，您可以定义该字段在迁移中需要是唯一的。

```
$table->string('title')->unique(); 
```

Enter fullscreen mode Exit fullscreen mode

这将在数据库中生成一个索引，当您尝试保存记录时，该索引将防止表中的两行具有相同的标题。这样做很好，但是当有人试图保存他们的帖子时，您希望以一种更加用户友好的方式来处理这个异常。这就是验证规则发挥作用的地方。

## 表单请求验证

如果您有一个用户必须填写才能创建帖子的表单，那么您可以直接在用于存储和更新的控制器方法中进行验证，也可以将验证转移到表单请求类中。我个人更喜欢将验证转移到一个表单请求类中，因为这样会使您的控制器更整洁，并且验证规则在一个地方。

您可以使用 artisan 命令来生成一个表单请求类，在我们的例子中，我们称它为 StorePostRequest

```
php artisan make:request StorePostRequest 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以将新创建的类注入到您的存储中，并在后置控制器
中更新方法

```
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StorePostRequest;

class PostController extends Controller
{
    public function store(StorePostRequest $request)
    {
        // Store model
    }

    public function update(StorePostRequest $request, Post $post)
    {
        // Update model
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以将唯一的验证规则添加到 StorePostRequest 类中。我们希望使 title 成为一个必需的属性，然后确保 title 在 posts 表中是惟一的。该规则具有唯一的格式:表，列。

```
public function rules()
{
    return [
        'title' => 'required|unique:posts,title'
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

已排序。如果我们尝试创建两个相同标题的帖子，它将返回一个验证错误。

## 但是当我们尝试编辑文章时会发生什么呢？

当我们尝试更新帖子时，它会再次检查标题是否是必需的，并且没有相同标题的帖子，这正是我们想要的，对吗？

实际发生的情况是，它检查 posts 表并找到我们之前保存的模型，该模型与我们试图更新的文章具有相同的标题，因此它返回一个验证错误。

为了解决这个问题，我们需要更新我们的验证规则，让它知道它需要检查这个标题在表中是唯一的，除了我们之前保存的模型。

除了指出要忽略哪个模型之外，我们还可以向唯一验证规则添加另一个值。我们可以用几种方式来写它，但是我更喜欢第二种，因为我发现当我稍后再回到它的时候，它更有可读性。

```
public function rules()
{
    return [
        'title' => "required|unique:posts,title,{$this->post->id}"
    ]; 
} 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
use Illuminate\Validation\Rule;

public function rules()
{
    return [
        'title' => [
            'required',
            Rule::unique('posts', 'title')->ignore($this->post)
        ]
    ]; 
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的是告诉 unique 规则，以确保文章中的标题是唯一的，但是忽略这个文章，这样我们就可以用与当前保存的模型相同的标题来更新文章，而不会生成验证错误。

但是`$this->post`从何而来？在设置`$this->post`的 StorePostRequest 类中没有设置变量。相反，我们必须在几个层次上查看继承链。

StorePostRequest 扩展了 FormRequest，它扩展了具有 getter 方法的 Request，该方法将在我们调用`$this->post`时返回传递给路由的 post。所以`$this->route($key)`，这里的`$key`就是本例中的`post`。

我希望这有助于更多地解释如何充分利用惟一验证规则。有关在表单请求中使用 URI 参数的更多信息，请参见 Laravel 文档的[授权表单请求](https://laravel.com/docs/5.8/validation#authorizing-form-requests)部分。