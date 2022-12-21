# Ruby 中的注入 Ruby 依赖注入🌈

> 原文：<https://dev.to/eligoh/dependency-injection-in-ruby-55ja>

我曾经在 Medium 上写过一篇关于依赖注入的文章- [依赖注入并不可怕！](https://medium.com/@choonggg/dependency-injection-is-not-scary-refactoring-rails-the-clean-way-5ce5b305fd22)。最近加入了 dev.to，我想我也应该把我的想法带到这里。

> 好的，Eli，由于这篇文章，我现在知道如何依赖注入了。但是什么时候使用它呢？

请耐心听我说，我很快就会谈到这一点。目前正在构建收件箱功能，我需要一个表单对象来创建对话和回复/编辑对话。

我可以做的是创建一个表单对象，把我的手擦干净，然后完成它。或者我也可以让这个表单在其他控制器中重用。🤓

```
module Inbox
  class ConversationForm < BaseForm
    attr_reader :conversation, :message, :sender

    def initialize(conversation, params = {})
      @conversation = conversation
      @sender = build_sender
      @message = conversation.messages.build(user: @sender)
    end

    ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

但是利用依赖注入，我能够为另一个控制器重用表单。

```
module Inbox
  class ConversationForm < BaseForm
    attr_reader :conversation, :message, :sender

    def self.build(params={})
      conversation = Conversation.new
      sender = build_sender
      new(conversation, sender, params)
    end

    def initialize(conversation, sender, params = {})
      @conversation = conversation
      @sender = sender
      @message = converation.messages.build(sender: sender)
    end

    ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当我想建立一个新的对话时，我会使用`Inbox::ConversationForm.build`。在另一个控制器/动作中，我会预定义对话并将其注入表单。

```
# ConversationsController
# Creating a Conversation
def new
  @form = Inbox::ConversationForm.build
end

# My::ConversationsController
def new
  @form = Inbox::ConversationForm.new(fetch_conversation, Current.user)
end

# BOTH
def create/update
  if @form.save
    render :new
  else
    redirect_to some_path
  end
end

private

def fetch_conversation
  Inbox::Conversation.find(params[:conversation_id])
end 
```

Enter fullscreen mode Exit fullscreen mode

再一次前进，依赖注入！