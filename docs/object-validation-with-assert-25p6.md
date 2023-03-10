# 使用断言进行对象验证

> 原文：<https://dev.to/blacksmoke16/object-validation-with-assert-25p6>

**更新:**2019 年 10 月 21 日更新示例为`Assert` `v0.2.0`。

# 断言

断言是一个基于注释的对象验证库，主要受 T2 Symfony 验证约束注释的启发。

```
@[Assert::NotBlank]
property name : String

@[Assert::GreaterThanOrEqual(value: 0)]
property age : Int32 
```

Enter fullscreen mode Exit fullscreen mode

## 简介

到目前为止，Crystal 中的验证主要是针对分片的。一个 shard 可以有自己的实现来验证 POST 主体数据，而同一个应用程序中的 ORM 可能完全不同。`Assert`最初包含在 [CrSerializer](https://github.com/blacksmoke16/CrSerializer) 中，但后来经过重写并作为其自己的碎片重新发布，其理念是带来某种程度的标准化，每个碎片都可以从中受益。这样做的主要好处是能够在整个应用程序中使用相同的验证。

本文旨在概述特性/示例用例。大多数示例代码来自于 [API 文档](https://blacksmoke16.github.io/assert/Assert.html)，其中包含了关于每个特性的更详细的文档。

## 特性

`Assert`考虑到灵活性和可扩展性，默认情况下它包括:

*   大量内置断言:
    *   电子邮件
    *   互联网协议（Internet Protocol 的缩写）
    *   统一资源定位器
    *   选择
    *   子对象/对象数组都有效
    *   [等。](https://blacksmoke16.github.io/assert/Assert/Assertions.html)
*   创建/使用自定义断言的能力
    *   在自定义断言中使用泛型
*   对一个对象运行断言子集的能力
*   控制验证对象的方式和时间

### 用法

为了使用`Assert`:

*   将它添加到您的`shard.yml`
*   要求吧- `require "assert"`
*   把它包含在你的对象中- `include Assert`

一个[用法示例](https://blacksmoke16.github.io/assert/Assert.html)可以在 API 文档中看到。

### 组

每个断言也可以被分配给一个组，以便运行断言的子集。

```
class Groups
  include Assert

  def initialize(@group_1 : Int32, @group_2 : Int32, @default_group : Int32); end

  @[Assert::EqualTo(value: 100, groups: ["group1"])]
  property group_1 : Int32

  @[Assert::EqualTo(value: 200, groups: ["group2"])]
  property group_2 : Int32

  @[Assert::EqualTo(value: 300)]
  property default_group : Int32
end

Groups.new(100, 200, 300).valid?                      # => true
Groups.new(100, 100, 100).valid?                      # => false
Groups.new(100, 100, 100).valid?(["group1"])          # => true
Groups.new(200, 100, 300).valid?(["default"])         # => true
Groups.new(100, 200, 200).valid?("group1", "default") # => false 
```

Enter fullscreen mode Exit fullscreen mode

这允许您在各种场景中重用相同的类/结构，因为您可以控制运行哪些断言。例如，当用户第一次注册时运行一组不同的断言，而当他们更新他们的一些信息时。

### 自定义断言&泛型

如果您的应用程序有一些独特的验证需求，而包含的断言没有涵盖这些需求；您可以创建自定义断言。

自定义断言只是一个从`Assert::Assertions::Assertion`继承的类，应用`Assert::Assertions::Register`注释，并实现一些方法。

```
@[Assert::Assertions::Register(annotation: Assert::Exists)]
# A custom assertion that validates if a record exists with the given *id*.
#
# For example, an ORM model where `.exists?` checks if a record exists with the given PK.
# I.e. `SELECT exists(select 1 from "users" WHERE id = 123);`
class Exists(PropertyType, Model) < Assert::Assertions::Assertion
  # This is a helper macro to make defining the initialize method easier
  initializer(
    actual: PropertyType
  )

  # :inherit:
  def default_message_template : String
    "'%{actual}' is not a valid %{property_name}."
  end

  # :inherit:
  def valid? : Bool
    Model.exists? @actual
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们用下面的方法定义了一个名为`Exists`的自定义断言:

*   初始化器——定义初始化器的帮助宏。
    *   参见 [API 文档](https://blacksmoke16.github.io/assert/Assert/Assertions/Assertion.html)获取更多关于初始化器用途的信息。
    *   关于宏本身的更多信息，见[断言.初始化器](https://blacksmoke16.github.io/assert/Assert/Assertions/Assertion.html#initializer(**ivars)-macro)
*   default_message_template -如果断言失败并且没有提供自定义消息，将使用的错误消息模板。
    *   断言中的实例变量可以在模板中使用，方法是用双花括号将 ivar 的名称括起来。
*   有效吗？-实现确定属性是否有效的逻辑。
    *   只要它返回`true`或`false`，这个实现可以是你想要的任何东西。

我们还需要应用`Assert::Assertions::Register`注释，它用于定义应该应用于属性的注释的名称。在这种情况下`Assert::Exists`。现在这个断言已经可以使用了。

```
class Post < SomeORM::Model
  include Assert

  def initialize; end

  @[Assert::Exists(User)]
  property author_id : Int64 = 17
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，断言将运行一个 SQL 查询来确定是否存在一个 *id* 为 17 的`User`。

依赖注入碎片，如 [Athena 的 DI 模块](https://github.com/Blacksmoke16/athena)也可以用于将当前用户/请求或一些 ACL 服务注入断言。

## 示例用例

除非有需要验证的对象，否则`Assert`没有用。对于某些项目/应用程序，可能没有必要。然而，web 框架和 ORM 很容易受益，因为验证是两者的重要部分。

### Web 框架

Web 应用程序对验证的需求最为明显。这可能是验证 API 中的 POST 主体，也可能是验证表单提交中的用户输入。但是，`Assert`不是用于客户端验证的。例如，它不会阻止用户在数字表单字段中输入字符串。当表单被提交时，它*将*被捕获，服务器运行验证。

让我们看一个例子，看看如何在使用 Kemal 的 API 中使用`Assert`。

```
require "kemal"
require "assert"
require "json"

# user.cr
class User
  include JSON::Serializable
  include Assert

  # Asserts that their age is >= 0 AND not nil
  @[Assert::NotNil]
  @[Assert::GreaterThanOrEqual(value: 0)]
  property age : Int32?

  # Assert their name is not blank
  @[Assert::NotBlank]
  property name : String

  # Assert their email is not blank AND is a valid format
  @[Assert::Email(message: "'%{actual}' is not a proper email")]
  @[Assert::NotBlank]
  property email : String

  # Assert their password is between 7 and 25 characters
  @[Assert::Size(Range(Int32, Int32), range: 7..25)]
  property password : String

  # Have the object be validated after the it is deserialized
  def after_initialize
    validate!
  end
end

# users_controller.cr
post "/users" do |env|
  env.response.content_type = "application/json"
  user = User.from_json env.request.body.not_nil!
  # Do stuff with a valid user

  # Return the user as JSON in the response
  user.to_json
rescue ex : Assert::Exceptions::ValidationError
  env.response.status_code = 400
  env.response.print ex.to_json
end

Kemal.run 
```

Enter fullscreen mode Exit fullscreen mode

这个设置使得在对象从 JSON POST 主体反序列化之后，在`User`上运行验证。如果对象无效，`#validate!`将引发一个`ValidationError`异常。我们正在捕捉这个异常，并将 JSON 错误消息返回给用户。

`Assert`还定义了`#valid?`和`#validate`方法，如果对象有效，则返回一个`Bool`，或者分别返回一个失败断言的数组。如果您只想知道对象是否有效，前者最有用；如果您想对断言数据做些什么，比如格式化错误消息，后者最有用。

```
curl -X POST \
  http://localhost:3000/users \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Jim",
    "age": -1,
    "email": "foobar",
    "password": "monkey123"
}' 
```

Enter fullscreen mode Exit fullscreen mode

会返回以下错误

```
{  "code":  400,  "message":  "Validation tests failed",  "errors":  [  "'age' should be greater than or equal to '0'",  "'foobar' is not a proper email"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

JSON 错误响应格式可以通过在异常类中重写[# to _ JSON(Builder:JSON::Builder)](https://blacksmoke16.github.io/assert/Assert/Exceptions/ValidationError.html#to_json(builder:JSON::Builder):Nil-instance-method)方法来更改；例如，如果您想按属性名对错误进行分组。

```
class Assert::Exceptions::ValidationError
  def to_json(builder : JSON::Builder)
    builder.object do
      builder.field "code", 400
      builder.field "message", @message
      builder.field "errors" do
        builder.object do
          @failed_assertions.group_by(&.property_name).each do |prop, errors|
            builder.field prop, errors.map &.message
          end
        end
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

会输出:

```
{  "code":  400,  "message":  "Validation tests failed",  "errors":  {  "age":  [  "'age' should be greater than or equal to '0'"  ],  "email":  [  "'foobar' is not a proper email"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当然，这只是一个演示。一个框架可以很容易地集成`Assert`以使过程更加流畅。比如它是如何在[雅典娜](https://github.com/blacksmoke16/athena)中使用的。

```
@[Athena::Routing::Post(path: "users")]
@[Athena::Routing::ParamConverter(param: "body", type: User, converter: Athena::Routing::Converters::RequestBody)]
def new_user(body : User) : User
  body
end 
```

Enter fullscreen mode Exit fullscreen mode

`ParamConverter`处理将请求体转换成`User`对象，在`User`上的任何验证也将在它被反序列化后执行。如果无效，将自动返回 JSON 错误。如果对象无效，控制器动作也不会执行。

虽然使用`Assert`可能会使你的对象变得更大，但它消除了在控制器代码中进行验证的需要，因为你可以确信无效的对象不会太远。它还允许您从应用程序的其他部分共享相同的验证逻辑。比如 ORM。

### ORM 模型

ORMs 模型本质上需要验证，以确保保存到数据库中的内容是正确的。继续我们的`Kemal`例子，我们可以很容易地将我们的`User`对象转换成一个`Granite`模型。

> **注意:**这只是一个例子，不包括建立 ORM

```
require "granite"
require "assert"

class User < Granite::Base
  include Assert

  table "users"

  column id : Int64?, primary: true

  # Asserts that their age is >= 0 AND not nil
  @[Assert::NotNil]
  @[Assert::GreaterThanOrEqual(value: 0)]
  column age : Int32?

  # Assert their name is not blank
  @[Assert::NotBlank]
  column name : String

  # Assert their email is not blank AND is a valid format
  @[Assert::Email(message: "'%{actual}' is not a proper email")]
  @[Assert::NotBlank]
  column email : String

  # Assert their password is between 7 and 25 characters
  @[Assert::Size(Range(Int32, Int32), range: 7..25)]
  column password : String

  # Granite includes `JSON::Serializble` by default
  def after_initialize
    validate!
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们的控制器动作也会稍微改变。

```
post "/users" do |env|
  env.response.content_type = "application/json"
  user = User.from_json env.request.body.not_nil!
  # We can now just save the user since we know its valid
  user.save

  # Return the user as JSON in the response
  user.to_json
rescue ex : Assert::Exceptions::ValidationError
  env.response.status_code = 400
  env.response.print ex.to_json
end 
```

Enter fullscreen mode Exit fullscreen mode

发布有效用户将返回:

```
{  "id":  1,  "age":  17,  "name":  "Jim",  "email":  "test@gmail.com",  "password":  "monkey123"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

`Assert`的好处:

1.  拥有一个可以跨分片使用的框架不可知的验证库。
    *   例如，在 API 控制器和 ORM 模型之间共享验证逻辑。
2.  用户可轻松扩展。
    *   自定义断言可以很容易地定义，不需要编辑任何碎片的源代码。
3.  易于集成到现有框架/碎片中。
    *   当前的项目/框架可以很容易地将`Assert`添加到它们的对象中，并控制它们何时/如何得到验证。
        *   比如`Kemal`的例子。将验证移入对象并移出控制器动作
        *   或者在保存之前确保模型有效的 ORM

像往常一样，如果您有任何问题、反馈或对新断言的想法；欢迎在 [Crystal Gitter](https://gitter.im/crystal-lang/crystal) 上给我发消息，或者在 [Github Repo](https://github.com/blacksmoke16/assert) 上制造一个问题。