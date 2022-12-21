# 没有宝石的 Ruby 服务对象

> 原文：<https://dev.to/oinak/ruby-service-objects-without-gems-4kd3>

有时我们使用像[开拓者](https://github.com/trailblazer/trailblazer)或[交互者](https://github.com/collectiveidea/interactor)这样的宝石只是为了将业务逻辑从控制器和模型中分离出来，有时，几个 PORO 和一个简单的约定就足够了:

下面是我最喜欢的一组服务对象约定的概要:

*   从持有共享行为的基础服务继承
*   有一个单一的类级命令方法*`run`接受关键字参数，即*总是*返回服务类的一个实例*
**   有一个实例级的`status` *查询方法*来检查动作是否成功(通常是`:ok`和`:error`)*   有一个实例级的`result` *查询方法*，它保存任何需要的外部对象作为结果*

 *> 我使用*查询*和*命令*，就像 Sandi Metz 所做的那样:
> 
> *   *查询*是一种获取信息但不改变接收者状态的方法，而
> *   *命令*可能返回也可能不返回，但总是改变接收器的状态

我总是使用`run`并让类的名称来描述动作，其他人，比如非常聪明的 Xavier Noria，更喜欢让服务的 main 方法成为一个有意义的动词，但是我发现这只是另一件需要记住的事情。

让我们通过一个示例来看看它的实际应用，我将使用身份验证，这不是因为您应该编写自己的程序(您不应该)，而是因为这是一个通用的业务逻辑，它将使 ServiceObject 的 fir 更好:

要继承的基础服务:

```
class Service
  attr_reader :result, :status

  def self.run(**args)
    new(**args).tap(&:run)
  end

  def initialize(*)
    raise NotImplemented, "must be defined by subclasses"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

具有熟悉逻辑的示例服务:

```
module Services
  class UserAuthenticate < ::Service
    def initialize(username:, password:, user_model: User)
      @username = username
      @password = pasword
    end

    def run
      if user&.authenticate(@password)
        @result = user
        @status = :ok
      else
        @result = nil
        @status = :error
      end
    end

    private

    def user
      user_model.find_by(username: @username)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在一个相当不新颖的 rails 应用中使用的例子:

```
class SessionsController < ApplicationController
  def create
    if user_authentication.status == :ok
      session[:user_id] = user_authentication.result.id
      redirect_to root_path
    else
      render :new, flash: { error: t('.wrong_email_or_password') }
    end
  end

  def destroy
    session[:user_id] = nil
    redirect_to root_path
  end

  private

  def user_authentication  
    @user_authentication ||= UserAuthentication.run(session_params)
  end

  def session_params
    params.require(:user).permit(:username, :password)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

什么，啊，你对那部分感兴趣？

好的，让我们看看如何测试这个(在 minispec 中):

```
describe UserAuthentication do
  let(:user_class) { Object.new }
  let(:user) { Minitest::Mock.new }
  let(:good_pass) { 'good_pass' }
  subject do
    user_class.stub(:find_by, user) do
      UserAuthenticate.run('username', user_class: user_class)
    end
  end

  describe "valid password" do
    before do
      user.expect(:authenticate, true, [good_pass])
    end

    it "returns status ok" do
      value(subject.status).must_equal(:ok)
    end

    it "has a result of user object" do
      value(subject.result).must_equal(user)
    end

    it "calls authenticate on the user model" do
      subject
      user.verify
    end
  end

  describe "invalid password" do
    # I am sure you can deduce this part :-)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode*