# 晶体中的依赖注入

> 原文：<https://dev.to/blacksmoke16/dependency-injection-in-crystal-2d66>

**更新:**2020 年 1 月 12 日为雅典娜版`0.8.0`
T5】更新:2020 年 6 月 16 日为雅典娜版`0.9.0`

# 【依赖注入】( DI)

依赖注入是一个强大的工具，可以使应用程序更容易测试，更具可扩展性，并增加系统组件的灵活性。

本文假设您对 DI 的概念有些熟悉。如果没有，请查看以下其他文章:

*   [依赖注入的快速介绍](https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/)
*   [依赖反转原理及代码示例](https://stackify.com/dependency-inversion-principle/)

DI 在每种语言中都是可用的，尽管它在某些语言中比在其他语言中更常见。Crystal 和 Ruby 看起来不像其他语言那样使用 DI。虽然弄清楚*为什么*已经超出了本文的范围，但是让我们来看一些有用的模式和例子，看看*DI 如何在您的应用程序中有用。*

我们将使用 Crystal 作为我们选择的语言，同时使用 Athena 作为我们的框架。Athena 的 DI 组件也可以作为[独立碎片](https://github.com/athena-framework/dependency-injection)获得。

## 经理

第一个例子是我所说的“经理”模式。当处理基于单个类/接口的多个对象实例时，这种模式最有用。即从同一类实例化的多个对象。

### 问题陈述

假设你将与其他公司合作。您希望设置一个 API 端点，在您的系统中公开特定于合作伙伴的数据，供他们使用。您还希望添加合作伙伴很容易；这样做只需要很少甚至不需要修改代码。

### 解

*   定义一个`Partner`类，实现每个合作伙伴共有的公共方法/属性。
*   定义一个负责管理`Partner`实例的`PartnerManager`类。
*   定义一个用于将合作伙伴的 id(从 API route 获得)转换成一个`Partner`实例的`PartnerParamConverter`。
*   定义一个`PartnerController`来分组与合作伙伴相关的 API 端点的逻辑。

### 代码

```
require "athena"

private PARTNER_TAG = "partner"

# Define a type that all partners wil be based off of, then register some partners.
@[ADI::Register(_id: "GOOGLE", name: "google")]
@[ADI::Register(_id: "FACEBOOK", name: "facebook")]
record Partner, id : String

# We can also use `ADI.auto_configure` to handle applying the
# correct tag to each `Partner` instance.
ADI.auto_configure Partner, {tags: [PARTNER_TAG]}

# Define another service that will have all partners injected into it.
# This manager will be injected into our param converter to handle
# resolving a `Partner` from their id.
@[ADI::Register(_partners: "!partner")]
struct PartnerManager
  @partners : Hash(String, Partner) = {} of String => Partner

  def initialize(partners : Array(Partner))
    # Create a mapping of partner ID to the partner instance.
    partners.each do |partner|
      @partners[partner.id] = partner
    end
  end

  # Returns a `Partner` with the provided *partner_id* or raises an
  # `ART::Exceptions::NotFound` exception if one could not be found.
  def get(partner_id : String) : Partner
    @partners[partner_id]? || raise ART::Exceptions::NotFound.new "No partner with an ID '#{partner_id}' has been registered."
  end
end

@[ADI::Register]
struct PartnerParamConverter < ART::ParamConverterInterface
  def initialize(@partner_manager : PartnerManager); end

  # :inherit:
  def apply(request : HTTP::Request, configuration : Configuration) : Nil
    # Grab the partner's ID from the request's attributes, then resolve it into a Partner.
    # Path/query params are automatically populated into the attributes.
    partner = @partner_manager.get request.attributes.get "id", String

    # Add the resolved partner object to the request's attributes
    # for it to later be resolved for the controller action argument.
    request.attributes.set configuration.name, partner, Partner
  end
end

# The controller that would house all partner related endpoints.
class PartnerController < ART::Controller
  @[ART::ParamConverter("partner", converter: PartnerParamConverter)]
  get "partner/:id", partner : Partner do
    # Notice we have access to the actual `Partner` object.
    # From here you can do whatever you need with the object.
    "Resolved #{partner.id}!"
  end
end

ART.run

# GET /partner/FOO    # => {"code":404,"message":"No partner with an ID 'FOO' has been registered."}
# GET /partner/GOOGLE # => "Resolved Google!" 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么重要

从这里开始，如果您想添加另一个合作伙伴，您只需注册另一个合作伙伴服务，一切都会正常工作。`@[ADI::Register(_id: "YAHOO", name: "yahoo")]`。

这也使得代码通用。除了它们的`Partner`类的实例之外，没有任何特定于任何特定合作伙伴的东西被定义。

### 备选方案

*   在控制器中硬编码一组`Partner`对象。
    *   这不是一个理想的解决方案，因为它会将`Partner`结构和`PartnerController`紧密耦合。了解所有可能的合作伙伴不应该是`PartnerController`的责任。由于额外的依赖性，这还会使控制器的更新/维护/测试更加困难。
*   将合作伙伴数据存储在数据库中。
    *   由于我们的系统实际上并没有在合作伙伴的系统中做任何事情，所以这些数据大部分都是死数据。它的存在只是为了解析 ID。拥有不必要的数据只是浪费。

## 即插即用

下一个例子与其说是一个模式，不如说是 DI 的一个核心特性。通过改变注入类的服务来改变类的功能的能力。

### 问题陈述

你有一个工人类，它将做一些工作，然后将输出写到`x`。`x`是类似亚马逊`S3`、本地文件系统、`Redis`等的外部服务。开始这个工人只会支持他们中的一个，比如说`S3`。然而，我们希望能够以这样一种方式设计类，即实现是通用的，并且如果需要的话，将来可以与任何其他服务一起工作。

### 解

*   定义一个“接口”，在 Crystal 的例子中，我们将使用一个模块来定义我们的作者的公共 API
*   为`S3`注册一个接口的初始实现
    *   为`Redis`注册另一个实现，但默认将`S3`作为默认实现
*   处理将适当的编写器注入我们的`Worker`类
*   为我们的类创建一个测试。

### 代码

#### 初始界面实现

```
require "athena"

# Define an abstract class that will act as our base interface.
# It also will ensure our subclasses implement the correct method.
module WriterInterface
  abstract def write(content : String) : Nil
end

# Create an implementation of `WriterInterface` for S3 and register it.
@[ADI::Register]
class S3Writer
  include WriterInterface

  # :inherit:
  def write(content : String) : String
    # Write the content
    "Wrote data to S3"
  end
end

# Register our worker also as a service, and set it as public.
# Ideally everything would be a service, however in most cases
# at least one service will need to be public in order to be
# the "entrypoint" into the application.
@[ADI::Register(public: true)]
class Worker
  @writer : WriterInterface

  # DI will automatically resolve the correct `WriterInterface` based on the
  # type restriction of the argument, and optionally the argument's name (more on that later).
  def initialize(writer : WriterInterface)
    # Manually assign the ivar to make changing the writer instance easier;
    # as we would only have to update these two variables within initialize versus
    # throughout the class.
    @writer = writer
  end

  def do_work
    # Do some work
    @writer.write "did work"
  end
end

# Grab out worker instance from the container and see what work it does.
ADI.container.worker.do_work # => Wrote data to S3 
```

Enter fullscreen mode Exit fullscreen mode

目前，由于我们只有一个`WriterInterface`的实现，这个例子并不比仅仅在`Worker`中实例化`S3Writer`更有益。然而，当我们引入多个实现时，这种情况会发生变化。

#### 多个接口实现

基于前面的例子:

```
# Add another implementation for `Redis`
@[ADI::Register]
class RedisWriter
  include WriterInterface

  # :inherit:
  def write(content : String) : String
    # Write the content
    "Wrote content to Redis"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们现在有一个小问题。由于`ADI`基于参数类型限制解析服务，并且我们现在有多个`WriterInterface`的实现，它如何知道注入哪一个呢？我们有两个选择:

##### 别名

`ADI`有[服务别名](https://athena-framework.github.io/dependency-injection/Athena/DependencyInjection/Register.html#aliasing-services)的概念，当遇到`WriterInterface`类型限制时，允许定义一个“默认”服务来使用。

```
# The `S3Writer` will now be injected by default
# when the `WriterInterface` type restriction is encountered.
@[ADI::Register(alias: WriterInterface)]
class S3Writer
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

##### 自变量名称

虽然在大多数情况下有一个默认实现是好的，但是如果我们想要另一个*实现呢？这种情况可以通过更新参数名来处理，这样解析逻辑现在将基于类型限制*和*参数名。* 

```
@[ADI::Register(public: true)]
class Worker
  @writer : WriterInterface

  # DI would now automatically inject `RedisWriter` since its a `WriterInterface` instance
  # AND it's service name is `redis_writer`.
  def initialize(redis_writer : WriterInterface)
    @writer = redis_writer
  end

  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试

DI 允许的主要好处之一是更容易测试，因为没有什么东西与其他东西耦合得太紧，也就是说，一切都是建立在抽象(也称为接口)之上的。

```
require "spec"

# Create a mock writer.
# This allows mocking the response from the external service as we shouldn't be worried about
# how the other service works since we should only be testing our worker, not its dependencies.
class MockWriter
  include WriterInterface

  def write(content : String) : String
    "WROTE_MOCK_DATA"
  end
end

# We can now test the functionality of our `Writer` type in isolation.
describe Worker do
  describe "#do_work" do
    it "should do work" do
      Worker.new(MockWriter.new).do_work.should eq "WROTE_MOCK_DATA"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么重要

因为我们将类型限制定义为我们的`WriterInterface`模块，所以我们的`Writer`类没有与它的任何一个特定实现紧密耦合。通过更新我们的`alias`，或者简单地改变初始化器参数的名称，我们可以很容易地改变注入哪个实现。

如前所述，这样做的主要好处之一是使`Worker`类依赖于抽象，而不是具体的类。或者换句话说，防止单一实现与`Worker`类紧密耦合。只要每个实现都正确地实现了`WriterInterface`，那么`Worker`类不应该关心它使用的是哪个实现，只需要调用它的`.write`，正确地写内容。

每个`WriterInterface`实现也可以很容易地注入它们自己的依赖项，比如凭证、API 客户端等。

这种模式的另一个好处是，如果你有一个单一目的的服务，比如发送电子邮件；您可以轻松地重用该服务。比如简单注入`EmailProvider`服务，然后用它发邮件。

DI 不再需要担心*如何*和*在哪里*对象被实例化。如果`EmailProvider`有自己的依赖项，并且每次需要发送电子邮件时你都在做`sender = EmailProvider.new xxx`;这不太理想。DI 允许类用给定的参数实例化一次；然后它可以被简单地注射到需要的地方。`def initialize(@sender : EmailProvider); end`e . x。

理想情况下，`EmailProvider`服务会经过测试，因此您可以放心，无论您在哪里注入它，电子邮件都会正确发送，而不必多次测试相同的逻辑。说到测试，DI 允许我们定义一个`WriterInterface`的测试实现。当测试一个依赖于其他服务的类时，依赖关系应该总是被模仿。这让你可以控制*这些服务如何动作。如果您没有模拟它们并使用实际的实现，您的测试将会比它们应该测试的多得多。*

## 共享数据

最后一个例子将展示如何使用 DI 在不同类型之间共享数据。

### 问题陈述

您正在创建一个 JSON API。您最近已经到了需要处理用户认证的地步。您希望以允许您在请求上下文之外访问当前用户的方式进行设计。

### 解

*   定义将存储当前用户对象的服务
*   在您的`SecurityListener`句柄授权内设置该服务的用户。
    *   还要添加一些`Log`上下文来包含这个用户的信息
*   使用此服务通过端点公开用户信息

### 代码

```
require "athena"

# Our user object, this would most likely be an ORM model.
class User
  include JSON::Serializable

  getter id, customer_id, name

  private def initialize(@id : Int64, @customer_id : Int64, @name : String); end

  # Simulate a ORM query method.
  def self.find(id : Int) : self
    new 1, 12, "Fred"
  end
end

@[ADI::Register]
# Our service that will store user the currently logged in user.
class UserStorage
  property! user : User
end

@[ADI::Register]
# Our custom listener that listens on the Request event.
#
# It'll handle making sure the user's token is valid, and setting the current user.
struct SecurityListener
  include AED::EventListenerInterface

  def self.subscribed_events : AED::SubscribedEvents
    AED::SubscribedEvents{
      ART::Events::Request => 30, # Set the priority higher so it runs before routing
    }
  end

  def initialize(@user_storage : UserStorage); end

  def call(event : ART::Events::Request, dispatcher : AED::EventDispatcherInterface) : Nil
    # Logic to make sure a token is provided.
    token = "PARSED_TOKEN"

    # Logic to validate the token and get the stored user_id from it.
    user_id = 1

    # Fetch the user from the database
    user = User.find user_id

    # Add some logging context for future logs
    Log.context.set user_id: user.id, customer_id: user.customer_id

    # Set the user in user storage.
    @user_storage.user = user
  end
end

# Register the controller itself as a service,
# NOTE: Controller services must be declared as public.
@[ADI::Register(public: true)]
class UserController < ART::Controller
  # Inject our `UserStorage` object
  def initialize(@user_storage : UserStorage); end

  # The user storage service could also be injected anywhere else you need the current user.
  get "user/me", return_type: User do
    Log.info { "Returning data for #{@user_storage.user.name}" }
    @user_storage.user
  end
end

# Start the server
ART.run

# GET /user/me # => {"id":1,"customer_id":12,"name":"Fred"}
# 2020-06-16T02:25:51.593601Z   INFO - athena.routing: Matched route /user/me -- uri: "/user/me", method: "GET", path_params: {}, query_params: {} -- user_id: 1, customer_id: 12
# 2020-06-16T02:25:51.593677Z   INFO - Returning data for Fred -- user_id: 1, customer_id: 12 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么重要

请注意，由于我们用我们的`SecurityListener`设置了一些`Log`上下文，在同一纤程中，在该点之后的所有日志都将包含该数据。此外，由于我们的`SecurityListener`具有优先级`30`，它在优先级为`25`的 Athena 的路由逻辑之前运行*。这是有益的，因为这意味着如果请求未经授权，我们甚至不必调用路由器。*

类似于`Log::Context`是纤程特定的，DI 容器也是纤程特定的，或者换句话说，每个请求都是唯一的。这样做的好处是，它允许在您的服务之间共享任意数据，而不必担心请求完成时的重置。请注意，`SecurityListener`内`UserStorage`上设置的`User`对象与提供给我们`UserController`的是同一个对象。由于 DI 容器处理实例化并向我们的服务提供对象，我们可以轻松地在多个服务中提供对同一对象实例的引用，而不需要手动将其作为公共 API 的一部分传递。

框架处理“current_user”最常见的方式是重新打开`HTTP::Server::Context`并将其添加到那里，这在大多数情况下都很好。但是，当您想在某个不是控制器动作或`HTTP::Handler`的地方访问当前用户时，会发生什么呢？拥有可以在任何需要的地方轻松访问的东西要灵活得多。

`UserStorage`类也可以扩展成，比方说，`TokenStorage`，它将存储令牌，它将有一个`user`方法；处理从令牌/令牌中的数据解析出一个`User`对象的逻辑。也可能有不同的`Token`类的实现，比如`AnonymousToken`，如果当前没有登录的用户。总的来说，这种方法比像当前用户那样依赖于请求上下文来访问公共数据要灵活得多。

### 备选方案

*   将`current_user`定义为一个类变量。
    *   这实际上使得系统不是光纤安全的，并且不能一次处理一个以上的请求。
*   在数据库中存储一个引用。
    *   这将解决能够在任何地方访问用户的问题，但是不太理想。如果一个系统正在处理许多请求，那就相当于许多额外的不必要的数据库查询。
*   将用户存储在`HTTP::Request`对象中
    *   假设您不需要在请求上下文之外的任何地方访问该用户，这是一个可行的解决方案。例如，如果您想在异步消息上下文中包含一些用户数据；您将如何提供用户对象？在某种程度上，如果不使它成为公共入队 API 的一部分，就没有一种干净的方法可以做到这一点。

## 结论

如图所示，DI 可以让你的 Crystal 应用程序更少的依赖于具体的类，更多的依赖于抽象。这防止了紧密耦合，使测试更容易，并鼓励代码重用。然而，这并不意味着 DI 是 100%使用的最佳模式。意识到它的能力并能够将该模式应用到一个非常适合的问题中是关键。

如果任何人有任何其他替代解决方案(好的和坏的)，请在评论中自由分享。我很想看看那些主要来自 Ruby 背景的人如何解决这些问题。

像往常一样，如果你有任何建议、问题或想法，欢迎加入我的雅典娜 [Gitter](https://gitter.im/athena-frameworkcr/community) 频道。我也可以通过 Discord ( `Blacksmoke16#0016`)或通过[电子邮件](//mailto:george@dietrich.app)联系。