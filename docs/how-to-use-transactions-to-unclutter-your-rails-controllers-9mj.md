# 如何使用事务来整理 Rails 控制器

> 原文：<https://dev.to/mercier_remi/how-to-use-transactions-to-unclutter-your-rails-controllers-9mj>

当你的 Rails 应用需要处理多个步骤时，你的控制器的方法会变得一团糟。

但是，不要绝望。你可以将连续的步骤委托给业务事务和玛丽-近藤那些混乱的控制器。我会告诉你怎么做。

让我们记住，业务事务不同于 ActiveRecord 事务。业务事务允许您创建一系列步骤，每个步骤都会产生一个`Success`或一个`Failure`对象。ActiveRecord 事务是关于确保几个数据库操作作为一个单元工作，并且如果出现任何错误，所有操作都将回滚。

在本教程中，我将使用文档简洁的[干交易 gem](https://github.com/dry-rb/dry-transaction/) 。

让我们从编写一个基本的控制器开始。

```
 class LeadsController < ApplicationController
    def new
      @lead = Lead.new
    end

    def create
      lead = Lead.create(lead_params)

      if lead.errors.any?
        lead = Lead.new(lead_params)
        render :new
      else
        redirect_to lead_path(lead)
      end
    end

    private

    def lead_params
      params.require(:lead).permit(
        :first_name, :last_name, :email
      )
    end
  end 
```

好的，这里有一个基本的`LeadsController`。您有两个动作:`new`和`create`。如您所见，`create`方法很简单:

*   我创建了一个新的销售线索，其中包含由我们的销售线索填写的信息。
*   我处理任何错误的方法是呈现预先填充了我们当前领导已经给我们的信息的`leads/new`表单。
*   如果一切顺利，我会将我们的线索重定向到她的个人资料页面(或者我选择的任何页面)。

但是如果我想做更多呢？假设我想:

*   通过 API 连接到远程 CRM [,并将销售线索的信息与我的销售团队同步](https://remimercier.com/what-is-an-api/)
*   向销售线索发送欢迎文本和电子邮件
*   通知我团队中的某人

```
 class LeadsController < ApplicationController
    def new
      @lead = Lead.new
    end

    def create
      lead = Lead.create(lead_params)

      if lead.errors.any?
        lead = Lead.new(lead_params)
        render :new
      else
        # synchronize new lead to distant CRM
        MyDistantCrm.create_lead(lead)

        # send welcome sms and email to new lead
        MySmsProvider.welcome_sms(lead).deliver_now
        LeadMailer.welcome(lead).deliver_now

        # notify business developer
        BusinessDevelopperMailer.new_lead(lead).deliver_now

        # redirect new leads to their profile page
        redirect_to lead_path(lead)

      # rescue any error to avoid a 500 error
      rescue StandardError => exception
        flash[:error] = exception
      end
    end

    private

    def lead_params
      params.require(:lead).permit(
        :first_name, :last_name, :email
      )
    end
  end 
```

如您所见，有几个新步骤在做非常不同的事情。在现实生活中的应用程序中，我们可以做更多的事情:创建关联，生成 SKU 号码，等等。另一件事是，所有这些步骤都依赖于被创建的`lead`没有任何错误。这将很多东西放入一个`else`分支。这就是商业交易可以派上用场的地方。

我们来看看怎么做。

## 交易营救

让我们建立一个干交易，看看如何清理我们的控制器。

### 第一步:安装干交易

将这一行添加到应用程序的 Gemfile:

```
 gem 'dry-transaction' 
```

然后执行:

```
 bundle install 
```

### 第二步:一般原则

在我们开始将部分`LeadsController#create`转移到一个事务之前，让我们看一下事务文件，看看什么是什么。

> 业务事务是一系列操作，任何操作都可能失败并停止处理。

每个步骤一次处理一个，并且必须返回一个`Success`或一个`Failure`对象。

```
 class Leads::Create < BaseTransaction
    # Here, I define the operation sequence
    tee :params
    step :create_lead

    # Here, I define each operation
    def params(input)
      @params = input.fetch(:params)
    end

    def create_lead(input)
      @lead = Lead.create(@params)

      if @lead.errors.any?
        Failure(error: @lead.errors.full_messages.join(' | '))
      else
        Success(input)
      end
    end
  end 
```

以下是需要记住的几点:

*   `tee`是一种特定的步骤。它总是会返回一个`Success`。这在这里很好，因为我只是获取一个输入。
*   `step`是基本操作。它必须返回一个`Success`或者一个`Failure`对象。
*   `@params`将可用于我的交易的所有其他步骤或方法，而`lead`则不可用。

### 第三步:将控制器的逻辑移入事务中

现在，我们可以将部分`LeadsController#create`转移到一个事务中。

我的应用程序的架构是:

```
 - app
    - controllers
      - application_controller.rb
      - leads_controller.rb 
```

在本教程结束时，它将:

```
 - app
    - controllers
      - application_controller.rb
      - leads_controller.rb
    - transactions
      - base_transaction.rb
      - leads
        - create.rb 
```

先建`base_transaction.rb`吧。

```
 class BaseTransaction
    include Dry::Transaction

    def self.call(*args, &block)
      new.call(*args, &block)
    end
  end 
```

`def self.call(*args, &block)`将允许我们**用一组参数从我们的控制器调用**事务。

我将从我们之前开始的事务开始，并将我们的部分`LeadsController#create`移入其中。

```
 class Leads::Create < BaseTransaction
    tee :params
    step :create_lead
    step :create_distant_lead
    step :send_welcome_sms
    step :send_welcome_email
    step :notify_business_developper

    def params(input)
      @params = input.fetch(:params)
    end

    def create_lead(input)
      @lead = Lead.create(@params)

      if @lead.errors.any?
        Failure(error: @lead.errors.full_messages.join(' | '))
      else
        Success(input)
      end
    end

    def create_distant_lead(input)
      MyDistantCrm.create_lead(@lead)

      Success(input)
    rescue StandardError => exception
      Failure(error: exception)
    end

    def send_welcome_sms(input)
      MySmsProvider.welcome_sms(@lead).deliver_now

      Success(input)
    rescue StandardError => exception
      Failure(error: exception)
    end

    def send_welcome_email(input)
      LeadMailer.welcome(@lead).deliver_now

      Success(input)
    rescue StandardError => exception
      Failure(error: exception)
    end

    def notify_business_developper(input)
      BusinessDevelopperMailer.new_lead(@lead).deliver_now

      Success(input)
    rescue StandardError => exception
      Failure(error: exception)
    end
  end 
```

我所有的`LeadsController#create`步骤现在都在我的事务中。

每个操作处理它自己的错误并返回一个`Success`或一个`Failure`对象。

例如，如果我的`MySmsProvider.welcome_sms(@lead).deliver_now`返回一个错误，我的事务将不执行接下来的步骤，并将返回一个`Failure`,所以我知道这里出了问题。

### 第四步:调用事务并处理其结果

现在我所有的步骤都在我的事务中了，我该怎么处理我的控制器呢？我们将从调用事务开始。

```
 class LeadsController < ApplicationController
    def new
      @lead = Lead.new
    end

    def create
      Leads::Create.call(params: lead_params)
    end

    private

    def lead_params
      params.require(:lead).permit(
        :first_name, :last_name, :email
      )
    end
  end 
```

整洁对吗？

> 调用事务将按照指定的顺序运行其操作，每个操作的输出将成为下一个操作的输入。

如前所述，一个事务要么返回一个`Success`对象，要么返回一个`Failure`对象。我可以在控制器中处理这些结果。

在我们最初的控制器中，如果线索创建失败，我将呈现`new`表单。另一方面，如果创建成功，我会将我的新线索重定向到它的概要文件。我们现在就开始吧！

```
 class LeadsController < ApplicationController
    def new
      @lead = Lead.new
    end

    def create
      Leads::Create.call(lead_params) do |m|
        m.success do
          redirect_to lead_path(lead)
        end
        m.failure do |failure|
          lead = Lead.new(lead_params)
          render :new
        end
      end
    end

    private

    def lead_params
      params.require(:lead).permit(
        :first_name, :last_name, :email
      )
    end
  end 
```

现在，我的控制器只处理对一组业务操作的调用。不再有数据库操作与发送电子邮件或重定向规则混杂在一起。抽象中有一些内聚性。

就是这个！

你们都去检查[干交易的文件](https://dry-rb.org/gems/dry-transaction/)，不要犹豫[阅读源代码](https://github.com/dry-rb/dry-transaction/)获得更多的魔法！

如果你有任何问题或者有什么不清楚的地方，请在 Twitter 上给我发消息，或者在 GitHub 上给我发消息，这样我们就可以把这个教程做得更好。

下一次，我将向您展示如何测试您的事务。

干杯，

雷米

* * *

[如何使用事务清理你的 Rails 控制器](https://remimercier.com/transactions-in-rails/)最初发表在[我的博客](https://remimercier.com)上。