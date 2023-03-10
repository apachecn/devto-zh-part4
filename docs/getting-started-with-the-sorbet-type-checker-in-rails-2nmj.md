# Rails 中的果汁冰糕类型检测器入门

> 原文：<https://dev.to/akshaynathan/getting-started-with-the-sorbet-type-checker-in-rails-2nmj>

在 [Monolist](https://monolist.co) ，我们正在为工程师建造一个指挥中心。我们集成了工程师常用的所有工具的 API，将他们的任务集中在一个地方。例如，我们的客户相信我们可以实时获取他们的吉拉问题、Github 推送请求和 Pagerduty 警报，这样他们就可以在 Monolist 中进行分类、优先排序和完成工作。

不幸的是，这说起来容易做起来难。这些 SaaS 工具的 API 千差万别，关于响应和返回值的假设通过我们的 Ruby on Rails 后端传播。由于 Ruby 的动态特性，当这些假设不正确时，它们会表现为运行时错误，给我们的用户带来问题，并且很难调试。我们很快意识到我们可以从类型中获益。虽然我们的前端和移动客户端是用 Typescript 编写的，但对于我们的大型 rails API，我们没有一个好的解决方案。

输入[冰糕](https://sorbet.run)。这是一个由 Stripe 编写的 ruby 类型检查器。像 Typescript 一样，Sorbet 允许逐步键入，这使我们能够慢慢地将类型引入我们的代码库。换句话说，我们可以获得逐文件添加类型的安全好处。

在这个博客系列中，我们将详细介绍将 Sorbet 添加到大型(> 100k LoC) Rails 代码库中的经验。在第一篇文章中，我们将向项目中添加 sorbet，并向第一个文件中添加类型。

## 设置

我们首先将 Sorbet 和 Sorbet 运行时添加到我们的 Gemfile 中。Sorbet 提供静态分析和运行时检查。虽然运行时检查将在生产中运行，但我们在开发中只需要静态分析，所以我们可以按环境来分离 gem。

```
gem "sorbet", group: :development
gem "sorbet-runtime" 
```

根据冰糕文档，此时我们可以运行:

```
bundle exec srb init 
```

不幸的是，这是我们遇到的第一个障碍——命令从未完成。“srb init”命令需要存储库中的每个`.rb`文件，并使用运行时反射来检测丢失的常量，并为现有代码生成 RBI 类型定义文件。如果您在`vendor/bundle/`或`node_modules/`中有大量被出售的依赖项，这可能是一个极其漫长(有时是无限的)的过程。

有一对[夫妇](https://github.com/sorbet/sorbet/issues/975) [公开](https://github.com/sorbet/sorbet/issues/1259) Github 关于这个问题的问题。幸运的是，`srb init`的帮助输出给了我们一个简单的方法来解决这个问题。我们可以简单地将`# typed: ignore`添加到我们的`vendor/`和`node_modules/`目录中的所有 ruby 文件中，而 Sorbet 会跳过它们。

```
for rb in $(find vendor node_modules -type f -name '*.rb');
  do sed -i '1i\# typed: ignore' "$rb"
done 
```

现在，当我们运行`srb init`时，命令完成。我们现在可以运行:

```
➜  bundle exec srb tc
No errors! Great job. 
```

太好了！

## 打开类型检查器

好消息是我们没有错误。坏消息是，我们实际上并没有对大部分代码进行类型检查。
这是因为当`srb init`由于缺少方法类型签名而无法对整个文件进行类型检查时，
会在文件中添加`# typed: false`或`# typed: ignore`。
这意味着`srb tc`不会对这些文件或它们的调用者中定义的类型和方法进行类型检查。

让我们来看看我们的一个文件:

```
# typed: false
class CreateStripeBillingCustomer
  def call(user:, stripe_token: nil)
    return if user.email.match?(/test\+\d*@monolist.co/)

    stripe_customer = Stripe::Customer.create({ email: user.email, source: stripe_token })

    BillingCustomer.create!({ user: user, stripeid: stripe_customer.id  })
  end
end 
```

该服务负责向我们的计费平台 Stripe 初始化用户。对于一个非测试用户，它调用 Stripe API 来初始化一个客户，并将 customer_id 存储在我们的数据库中。

计费代码尤其敏感–您不会希望因为意外的空对象而向客户重复收费。我们认为我们的账单代码将是开始添加果汁冰糕类型的好地方，这项服务因其简单而成为特别好的候选。

我们把`# typed: false`改成`# typed: true`吧。
在`# typed: true`模式下，Sorbet 只检查它知道的方法的类型。也就是说，Sorbet
只检查显式注释的方法，无论是通过手写的类型签名还是类型定义 RBI 文件。
对于任何其他方法，Sorbet 假设它们的返回值是`T.untyped`，这是一个特殊类型，匹配所有类型。
虽然`# typed: true`不是最严格的静态类型检查级别，但是我们可以从它开始，使我们能够逐步地对我们的代码库进行类型化。

一旦我们改变了注释，我们可以重新运行`srb tc`。

```
➜  bundle exec srb tc
app/services/billing/create_stripe_billing_customer.rb:6: Method create does not exist on T.class_of(Stripe::Customer) https://srb.help/7003
     6 |    stripe_customer = Stripe::Customer.create({
     7 |      name: user.display_name,
     8 |      email: user.email,
     9 |      source: stripe_token,
    10 |    })
  Autocorrect: Use `-a` to autocorrect
    app/services/billing/create_stripe_billing_customer.rb:6: Replace with freeze
     6 |    stripe_customer = Stripe::Customer.create({
                                               ^^^^^^

Errors: 1 
```

嗯，看来冰糕似乎不知道`Stripe::Customer.create`法。
在 Sorbet 中，所有方法要么必须通过一个“sig”包含一个内联类型签名，我们将在后面看到，要么必须在相应的“SIG”中包含类型签名。rbi”文件。

## 添加签名类型

果汁冰糕配有。Ruby 标准库的 rbi 文件。所有其他类型定义文件要么必须是手写的，要么从 sorbet/sorbet-typed(一个通用 gems 的中央类型库)中下载。

当我们运行 srb init 时，sorbet 将自动为安装在我们的 gem 文件中的任何 gem 提取这些类型定义。让我们看看它做了什么:

```
➜  ls sorbet/rbi/sorbet-typed/lib
actionmailer  activemodel   activesupport railties      sidekiq
actionpack    activerecord  bundler       rainbow       stripe
actionview    activestorage minitest      ruby 
```

起初，看起来我们把“条纹”宝石的所有定义都去掉了。然而，仔细观察:

```
➜  cat sorbet/rbi/sorbet-typed/lib/stripe/all/stripe.rbi
# This file is autogenerated. Do not edit it by hand. Regenerate it with:
#   srb rbi sorbet-typed
#
# If you would like to make changes to this file, great! Please upstream any changes you make here:
#
#   https://github.com/sorbet/sorbet-typed/edit/master/lib/stripe/all/stripe.rbi
#
# typed: strong

class Stripe::Card
  sig { returns(String) }
  def brand; end

  sig { params(other: String).returns(String) }
  def brand=(other); end

  sig { returns(Integer) }
  def exp_month; end

  sig { params(other: Integer).returns(Integer) }
  def exp_month=(other); end

  sig { returns(Integer) }
  def exp_year; end

  sig { params(other: Integer).returns(Integer) }
  def exp_year=(other); end

  sig { returns(String) }
  def last4; end

  sig { params(other: String).returns(String) }
  def last4=(other); end
end 
```

看起来条纹宝石的“冰糕型”定义真的很少。这对于我们来说是新的，因为我们已经非常依赖于 DefinitelyTyped，它是共享类型库的 Typescript 等价物。

然而，这很容易解决。让我们在“sorbet/rbi/lib/stripe/customer . Rb”添加一个我们自己的. RBI 文件。

```
class Stripe::Customer
  sig do
    params({
      email: T.nilable(String),
      source: T.nilable(String),
    }).returns(Stripe::Customer)
  end
  def self.create(email:, source:); end
end 
```

这里我们根据 Stripe API 文档定义了`create`方法。
我们的 sig 声明该方法接收一个可选的字符串 email 和一个可选的字符串 source，并返回一个`Stripe::Customer`。
让我们再跑一次`srb tc`。

```
➜  bundle exec srb tc
app/services/billing/create_stripe_billing_customer.rb:8: Method id does not exist on Stripe::Customer https://srb.help/7003
     8 |    BillingCustomer.create!({ user: user, stripeid: stripe_customer.id })
                                                            ^^^^^^^^^^^^^^^^^^
Errors: 1 
```

让我们将`id`方法添加到我们的`Stripe::Customer`类型中。

```
sig do
  returns(String)
end
def id; end 
```

现在……

```
➜  bundle exec srb tc
No errors! Great job. 
```

## 更强的保障

还记得我们说过`# typed: true`不是最严格的类型检查模式吗？既然我们已经用这种严格程度解决了错误，让我们看看是否可以用恰当命名的`# typed: strict`更进一步。

一旦我们更改了注释，我们就可以重新运行`srb tc`。

```
➜  bundle exec srb tc
app/services/billing/create_stripe_billing_customer.rb:3: This function does not have a `sig` https://srb.help/7017
     3 |  def call(user:, stripe_token: nil)
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  Autocorrect: Use `-a` to autocorrect
    app/services/billing/create_stripe_billing_customer.rb:3: Insert sig {params(user: T.untyped, stripe_token: T.untyped).returns(T.untyped)}

     3 |  def call(user:, stripe_token: nil)
          ^
  Autocorrect: Use `-a` to autocorrect
    app/services/billing/create_stripe_billing_customer.rb:3: Insert   extend T::Sig

     3 |  def call(user:, stripe_token: nil) 
```

在严格模式下，Sorbet 要求所有方法都必须有签名。再加一个:

```
sig do
  params({
    user: User,
    stripe_token: T.nilable(String)
  }).returns(T.nilable(BillingCustomer))
end
def call(user:, stripe_token: nil) 
```

现在当我们重新运行 srb tc:

```
➜  bundle exec srb tc
app/services/billing/create_stripe_billing_customer.rb:12: Method email does not exist on User https://srb.help/7003
    12 |    return if user.email.match?(/demo\+\d*@monolist.co/)
                      ^^^^^^^^^^
  Autocorrect: Use `-a` to autocorrect
    app/services/billing/create_stripe_billing_customer.rb:12: Replace with eval 12 |    return if user.email.match?(/demo\+\d*@monolist.co/)
                           ^^^^^

app/services/billing/create_stripe_billing_customer.rb:14: Method email does not exist on User https://srb.help/7003
    14 |    stripe_customer = Stripe::Customer.create({ email: user.email, source: stripe_token })
                                                               ^^^^^^^^^^
  Autocorrect: Use `-a` to autocorrect
    app/services/billing/create_stripe_billing_customer.rb:14: Replace with eval 14 |    stripe_customer = Stripe::Customer.create({ email: user.email, source: stripe_token })
                                                                    ^^^^^

Errors: 2 
```

`user.email`方法由 ActiveRecord 从数据库模式中动态生成。不幸的是，Sorbet 不知道动态生成的方法。为我们 100 多个模型中的每一列进行 rbi 定义将是极其乏味的。
幸运的是，开源项目[“冰糕轨道”](https://github.com/chanzuckerberg/sorbet-rails)允许我们自动生成。我们模型的 rbi 文件。

一旦我们用`gem "sorbet-rails"`安装了它，我们就可以运行`bundle exec rake rails_rbi:models`来生成。我们项目中每个模型的 rbi 文件。让我们重新运行`srb tc` :

```
➜  bundle exec srb tc
app/services/billing/create_stripe_billing_customer.rb:12: Method match? does not exist on NilClass component of T.nilable(String) https://srb.help/7003
    12 |    return if user.email.match?(/demo\+\d*@monolist.co/)
                      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  Autocorrect: Use `-a` to autocorrect
    app/services/billing/create_stripe_billing_customer.rb:12: Replace with T.must(user.email)
    12 |    return if user.email.match?(/demo\+\d*@monolist.co/)
                      ^^^^^^^^^^
Errors: 1 
```

哇哦。我们的第一个真正的类型错误！让我们来看看“sorbet/rails-RBI/models/user . RBI”
中生成的签名

```
sig { returns(T.nilable(String)) }
def email; end 
```

看起来像是将`T.nilable`分配给`User#email`的返回类型的冰糕轨道。这是因为电子邮件列在我们的数据库中是可空的。这是一个遗留下来的问题，那时注册 Monolist 不需要电子邮件地址。对于这些传统用户，我们应该简单地忽略他们，不要试图在 Stripe 上创建客户。

让我们来实现这个:

```
def call(user:, stripe_token: nil)
  return unless (email = user.email)
  return if email.match?(/demo\+\d*@monolist.co/)

  stripe_customer = Stripe::Customer.create({ email: email, source: stripe_token })

  BillingCustomer.create!({ user: user, stripeid: stripe_customer.id })
end 
```

现在，当我们重新运行`srb tc` :

```
➜  bundle exec srb tc
No errors! Great job. 
```

在这里，我们可以看到冰糕的“流量敏感性”的价值。这意味着 Sorbet 通过程序控制流跟踪类型。虽然`user.email`以`T.nilable(String)`开始，但是第 1 行的条件检查将电子邮件展开到`String`，修复了之前的类型错误。

## 结论

在这篇博文中，我们用 Sorbet 设置了我们的 Rails API 项目，为我们的模型和一些外部依赖项添加了类型定义，并完全键入了我们的第一个文件。虽然 Sorbet 仍然有一些粗糙的边缘(不灵活的初始化过程，小类型库)，我们仍然能够相对容易地启用它的静态类型检查，并发现我们的第一个真正的类型错误。

在 Monolist，我们相信添加类型是确保我们的客户所依赖的 API 集成的稳定性的好方法。在本系列的下一篇文章中，我们将把 Sorbet 集成到 CI 系统中，启用运行时检查，并开始向更复杂的代码路径添加类型。

* * *

### 想看看 Monolist 如何让你这样的工程师更有生产力？免费报名[这里](https://monolist.co)。

### 喜欢这个帖子？点击这里，加入我们的邮件列表了解更多内容[。](http://eepurl.com/gFxG3n)