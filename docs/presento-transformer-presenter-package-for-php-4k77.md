# PHP 的 Presento - Transformer & Presenter 包

> 原文：<https://dev.to/nahid/presento-transformer-presenter-package-for-php-4k77>

# Presento

一个 PHP 数据准备和展示包。

github:[https://github.com/nahid/presento](https://github.com/nahid/presento)

## 为什么要 Presento？

Presento 是一个简单但功能强大的工具，用于准备和呈现数据。
当我们构建一个基于 API 的应用程序时，我们需要在*通过响应呈现*数据之前*转换*数据。这个软件包将使你更容易完成这项任务。

不够清楚？

别担心，你会从[用法示例](#usage)中得到更好的想法。

### 要求

```
PHP >= 7.0
ext-json 
```

## 安装

使用 composer 安装软件包:

```
composer require nahid/presento 
```

## 用法

Presento 有两个重要的用途。一个是数据的**呈现**，另一个是数据的**转换**。

让我们看一些例子来了解如何使用它。

我们将使用下面的数据集来展示例子。假设我们从某个数据源获取了这个数据集，并需要在将其发送到响应之前进行一些转换或修改。

```
$response = [
    "id" => 123456,
    "name" => "Nahid Bin Azhar",
    "email" => "talk@nahid.im",
    "type" => 1,
    "is_active" => 1,
    "created_at" => "2018-01-02 02:03:04",
    "updated_at" => "2018-01-02 02:03:04",
    "deleted_at" => "2018-01-02 02:03:04",
    "projects" => [
        [
            "id" => 1,
            "name" => "Laravel Talk",
            "url"   => "https://github.com/nahid/talk",
            "license" => "CC0",
            "created_at" => "2016-02-02 02:03:04"
        ],
        [
            "id" => 2,
            "name" => "JsonQ",
            "url"   => "https://github.com/nahid/jsonq",
            "license" => "MIT",
            "created_at" => "2018-01-02 02:03:04"
        ]
    ]
]; 
```

#### 简单演示示例

当将这些数据发送给 API 响应时，我们只想发送`id`、`name`、`email`、`type`、`is_active`和`projects`。

我们可以简单地通过准备一个演示者来做到这一点，如下所示。

```
// UserPresenter.php

class UserPresenter extends \Nahid\Presento\Presenter
{
    public function present() : array
    {
        return [
            'id',
            'name',
            'email',
            'type',
            'is_active',
            'projects',
        ];
    }
} 
```

你可能已经猜到了如何使用它，对吗？

```
$user = new UserPresenter($response);
dump($user->get()); 
```

它会显示如下内容:

```
[
    "id" => 123456,
    "name" => "Nahid Bin Azhar",
    "email" => "talk@nahid.im",
    "type" => 1,
    "is_active" => 1,
    "projects" => [
        [
            "id" => 1,
            "name" => "Laravel Talk",
            "url"   => "https://github.com/nahid/talk",
            "license" => "CC0",
            "created_at" => "2016-02-02 02:03:04"
        ],
        [
            "id" => 2,
            "name" => "JsonQ",
            "url"   => "https://github.com/nahid/jsonq",
            "license" => "MIT",
            "created_at" => "2018-01-02 02:03:04"
        ]
    ]
] 
```

很简单，对吧？

#### 【关键】演示示例中的别名

假设您想将一些“键”更改为不同的东西，比如将`id`键更改为`user_id`。
你怎么能这样做？

只要做以下事情。

```
// UserPresenter.php
class UserPresenter extends \Nahid\Presento\Presenter
{
    public function present() : array
    {
        return [
            'user_id' => 'id',
            'name',
            'email',
            'type',
            'is_active',
        ];
    }
} 
```

这将如下格式化数据:

```
[
    "user_id" => 123456,
    "name" => "Nahid Bin Azhar",
    "email" => "talk@nahid.im",
    "type" => 1,
    "is_active" => 1,
] 
```

#### 演示示例中的深度遍历

通过使用`.`(点)符号，您可以很容易地深入研究并从嵌套级别获取数据。

假设您想要将第一个包的`name`显示为数据中的`top_package`。

这是你如何做它。

```
// UserPresenter.php
class UserPresenter extends \Nahid\Presento\Presenter
{
    public function present() : array
    {
        return [
            'id',
            'name',
            'email',
            'type',
            'is_active',
            'top_package' => 'projects.0.name',
            'projects',
        ];
    }
} 
```

数据的格式如下:

```
[
    "id" => 123456,
    "name" => "Nahid Bin Azhar",
    "email" => "talk@nahid.im",
    "type" => 1,
    "is_active" => 1,
    "top_package" => "Laravel Talk",
    "projects" => [
        [
            "id" => 1,
            "name" => "Laravel Talk",
            "url"   => "https://github.com/nahid/talk",
            "license" => "CC0",
            "created_at" => "2016-02-02 02:03:04"
        ],
        [
            "id" => 2,
            "name" => "JsonQ",
            "url"   => "https://github.com/nahid/jsonq",
            "license" => "MIT",
            "created_at" => "2018-01-02 02:03:04"
        ]
    ]
] 
```

注意数据中的`top_package`键。

#### 简单变压器示例

假设我们的 UserPresenter 是这样的:

```
// UserPresenter.php
class UserPresenter extends \Nahid\Presento\Presenter
{
    public function present() : array
    {
        return [
            'user_id' => 'id',
            'name',
            'email',
            'type',
            'is_active',
        ];
    }
} 
```

我们希望将`user_id`显示为*散列值*，而不是我们数据库中的增量整数值。这意味着我们想要改造`user_id`。

为此，我们需要创建一个 Transformer 类，如下所示:

```
// UserTransformer.php
class UserTransformer extends \Nahid\Presento\Transformer
{
    public function getUserIdProperty($value)
    {
        return md5($value);
    }
} 
```

注意，由于我们将转换`user_id`属性，我们将我们的 transformer 方法命名为`getUserIdProperty`。因此，如果您也想转换`name`属性，您只需要在这个类中创建另一个名为`getNameProperty`的方法，并在其中添加转换逻辑。

现在，我们需要让*展示者*知道如何在展示之前*转换*数据。

为此，我们需要在`UserPresenter`类中添加以下方法。

```
// UserPresenter.php
public function transformer()
{
    return UserTransformer::class;
} 
```

因此，我们的最终输出将是:

```
[
    "user_id" => "e10adc3949ba59abbe56e057f20f883e",
    "name" => "Nahid Bin Azhar",
    "email" => "talk@nahid.im",
    "type" => 1,
    "is_active" => 1,
] 
```

很简单吧，伙计？

#### 嵌套演示者示例

您可能注意到在我们的数据集中有一个`projects`的集合。如果每个`project`是一个单独的资源，那么您可能有一个单独的演示者。像这样:

```
// ProjectPresenter.php
class ProjectPresenter extends \Nahid\Presento\Presenter
{
    public function present() : array
    {
        return [
            'id',
            'name',
            'url',
            'license',
            'created_at',
        ];
    }

    public function transformer()
    {
        return ProjectTransformer::class;
    }
} 
```

您能为*用户*数据中的每个`projects`使用这个演示者吗？

太好了。就这么办:

```
// UserPresenter.php
public function present() : array
{
    return [
        'user_id' => 'id',
        'name',
        'email',
        'type',
        'is_active',
        'projects' => [ProjectPresenter::class => ['projects']],
    ];
} 
```

现在，*用户*的`projects`列表中的每一个`project`都将按照`ProjectPresenter`中的定义呈现。

#### 基础数据格式转换示例

正如你所看到的，我们到目前为止使用的数据集是一个普通的*数组*。但有时情况可能并非如此。你可能需要做一些不同的工作，比如 Laravel 框架的**雄辩模型**。
在这种情况下，您可以简单地在您的*表示器*中添加一个名为`convert`的方法，将基本数据转换成数组格式。

我们来看一个例子:

```
// UserPresenter.php
public function convert($data)
{
    if ($data instanceof Model) {
        return $data->toArray();
    }

    return $data;
} 
```

就是这样。