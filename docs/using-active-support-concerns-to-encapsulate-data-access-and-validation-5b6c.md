# 使用主动支持关注封装数据访问和验证

> 原文：<https://dev.to/diegocasmo/using-active-support-concerns-to-encapsulate-data-access-and-validation-5b6c>

最近，我必须实现一个特性，将货币的概念引入到一个 API 中。API 的用户可以创建不同类型的资源，每个资源都需要知道所使用的货币类型。例如，用户可以用美元创建一个`Payment`,或者用澳元请求一个`Withdrawal`。

在这篇简短的博文中，我的目标是解释我是如何使用`ActiveSupport::Concern`将所有数据访问和`currency`属性的验证封装和分组到一个地方的。

### 大意

我希望货币是一个`enum`，其中每个值是一个[货币代码](https://en.wikipedia.org/wiki/ISO_4217)。使用`enum`允许按名称编写查询，比如`Payment.first.currency.GBP?`或`Payment.where(currency: :AUD)`。如果这样的逻辑只在单一模型中需要，我可能会这样做:

```
class Payment < ApplicationRecord
  enum currency: { USD: 0, AUD: 1, GBP: 2 }
  validates :currency, inclusion: { in: currencies.keys }
end 
```

Enter fullscreen mode Exit fullscreen mode

尽管如此，同样的货币概念需要存在于多种模型中。为了解决这个问题，我选择使用`ActiveSupport::Concern`来干燥模型，并将逻辑的相关关注点组合在一起。正如 DHH 在[2012 年](https://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns)、`ActiveSupport::Concern`的这篇博客文章中所描述的，“封装了关于某一部分职责的数据访问和领域逻辑。”

### 解

为了实现期望的解决方案，我在`app/models/concerns/currency.rb`中创建了一个`Currency`模块，如下所示:

```
module Currency
  extend ActiveSupport::Concern
  include ActiveModel::Validations

  included do
    enum currency: { USD: 0, AUD: 1, GBP: 2 }
    validates :currency, inclusion: { in: currencies.keys }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`included`方法使用一个块，当一个模块包含在另一个模块或类中时，它就会被执行。在这种情况下，`currency`模块定义了一个`enum`属性，它为每种货币指定了一个命名值。它还验证了`currency`属性被设置为在`enum`中定义的值之一。要使用关注点，只需包含它:

```
class Payment < ApplicationRecord
  include Currency
end 
```

Enter fullscreen mode Exit fullscreen mode

```
class Withdrawal < ApplicationRecord
  include Currency
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！`Payment`和`Withdrawal`模型现在定义了如何访问和验证`currency`属性(假设为`currency`列创建了一个迁移，并在每个表中适当地运行)。

### 测试

通过使用`rspec`的共享示例，测试类或模块的共享行为可以大大简化。在`spec/support/shared_examples/currency_spec.rb`中，我定义了一个共享示例(注意，我使用的是`shoulda-matchers` gem):

```
shared_examples_for 'currency' do
  let(:model) { described_class }

  it { should define_enum_for(:currency).with_values(USD: 0, AUD: 1, GBP: 2) }

  describe 'validations' do
    it { should allow_value(model.currencies[:USD]).for(:currency) }
    it { should allow_value(model.currencies[:AUD]).for(:currency) }
    it { should allow_value(model.currencies[:GBP]).for(:currency) }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

要使用它，只需在模型上下文中包含共享示例:

```
RSpec.describe Payment, type: :model do
  it_behaves_like 'currency'
end 
```

Enter fullscreen mode Exit fullscreen mode

```
RSpec.describe Withdrawal, type: :model do
  it_behaves_like 'currency'
end 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。通过使用如上所述的共享示例，包含问题的模型可以很容易地测试其功能。

### 结论

在这篇简短的博文中，我展示了一个简单的解决方案来封装和分组属性的数据访问和验证。注意我选择使用`ActiveSupport::Concern`，因为许多模型都使用相同的属性。如果`currency`属性只被一个模型使用，我就不会为它定义一个关注点。最后，我展示了如何使用`rspec`的共享例子来简化测试类或模块的共享行为。

你实现过类似的东西吗？你是怎么做到的？请在下面的评论中告诉我:)。