# ActiveRecord 回调的简约性

> 原文：<https://dev.to/sap1enza/parsimony-with-activerecord-callbacks-4bnj>

看到有人将 activerecord 回调与不好或错误的事情联系起来，不惜一切代价避免使用它们，并以其他方式思考以达到预期的目标，这并不罕见，尽管我相信没有什么是绝对的，也有需要它们的情况。

以下面的场景为例

```
class Purchase < ApplicationRecord
  belongs_to :user

  def confirm!(user)
    self.user = user
    self.confirmed = true
    PurchaseProcessor.create!(user: user)
    Save!
  end
end 
```

我们有一个类`Purchase`，它有一个方法`confirm!`负责改变`Purchase`的状态，然而这可能会给我们带来一个令人头疼的一致性问题，因为 ActiveRecord 会自动为属性生成方法，并且它们可以在其他流中被改变

```
purchase.update_attributes!(confirmed: true)
Purchase.new(confirmed: true).save
Purchase.create!(confirmed: true) 
```

这是一个很大的问题，因为对象的状态是不一致的，它被确认了，但是没有用户，处理器也没有被执行

如果有一个工人在`PurchaseProcessor`设定的条件下生成任何重要的采购基础报告，那么离开基础不一致性的机会是巨大的

一种选择是使用回调

```
class Purchase < ApplicationRecord
  belongs_to :user

  validates_presence_of :user_id, if: :confirmed?

  after_save :execute_purchase_processor_on_confirm

  private

  def execute_purchase_processor_on_confirm
    If confirmed? && confirmed_changed?
      PurchaseProcessor.create!(user: user)
    end
  end
end 
```

这样，不管任何属性在任何流中是如何设置的，我们都可以保证完整性

但是也有回调本身成为问题的情况，例如:

```
class User < ApplicationRecord
  after_create :send_confirmation_email

  def send_confirmation_email
    UserMailer.registration_confirmation(self).deliver
  end
end 
```

这种方式可能需要在不调用`send_confirmation_email`的情况下在任何地方创建一个用户，使用这种设计，你只需使用私有方法`create_without_callbacks`就可以了，它也会忽略验证(这不是一个好的选择)

我认为这个问题是值得的，你需要多久才能创建一个不发送确认邮件的用户？也许它应该在其他类中分开，例如:

```
class UserRegistration
  def initialize(user)
    @user = user
  end

  def register
    @user.save
    UserMailer.registration_confirmation(self).deliver
  end
end 
```

也许最好将默认情况下不期望的行为分开，目的是简化使用和维护(你期望创建一个`User`只是创建它而不执行其他动作或流程)

回调并不都是邪恶的，但我相信在使用它之前应该有一些反思，考虑一下上下文和场景...但是作为一个规则，我特别喜欢在不使用回调的情况下开始思考设计，并分析它是否会导致一些不一致或不期望的场景