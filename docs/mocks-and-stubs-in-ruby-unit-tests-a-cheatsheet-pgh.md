# ruby 单元测试中的模仿和存根，备忘单

> 原文：<https://dev.to/oinak/mocks-and-stubs-in-ruby-unit-tests-a-cheatsheet-pgh>

基于 Sandi Metz 非常精确的解释，我将试着给你一个终极记忆术(如果你学会了的话)或者备忘单(如果你用书签标记/复制了的话)来回答这个问题*考什么？*。

首先，让我们建立我将用来描述四个选项的术语:

*查询:*是返回数据但不改变接收方对象状态的方法，即:

```
>> a = [1,2,3]
=> [1, 2, 3] 
a.size
=> 3 
>> a
=> [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

*命令:*是一个方法，可以返回值，也可以不返回值，但它总是改变接收者对象的状态。

```
>> a = [1,2,3]
=> [1, 2, 3] 
>> a.pop
=> 3 
>> a
=> [1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

*传入*:是在类中定义的*方法，你想在它的单元测试中测试哪个行为。*

*Outgoing* :是你正在测试的类的 *out* 中定义的方法，你需要模拟哪些行为，但是不想在你的单元测试中测试。

好了，这些是定义，现在是规则:

## 1。传入查询:

*   断言方法的返回值

## 2。输入命令

*   断言方法的返回值(如果有)
*   断言状态的变化

你改变的状态可能无法通过你的对象的公共接口访问，但是正如 Xavier Noria 所说，如果它重要到需要一个文本，那么使用`.send()`或`instance_variable_get()`来检查它是值得的。利用 ruby 的高级自省能力。

## 3。传出查询

*   存根其他类的响应

这里的要点是，你需要一些外部类提供的值，但是那个类不介意你是否调用它，因为它声明保持不变。

因此，对于您的测试，您需要的是那个值，而不是提供它的类，特别是如果它不是您的，为回答一个特定的值而设置起来很复杂，不在您的控制之下，等等。

这就是如何用 Minitest 包含的存根系统来存根化方法调用:

```
 OtherKass.stub :query, "response" do
    assert_equal "expected_value", obj_under_test.tested_method
  end 
```

Enter fullscreen mode Exit fullscreen mode

## 4。传出命令

*   存根其他类的响应(如果需要的话)
*   模拟接收方对象来检查是否进行了调用

有时候，你对其他类的调用会有副作用，特别是*预期的*副作用，比如持久性、通知、资金转移、资产创造...

如果这是一个单元测试，那么另一个类如何管理它的状态与你无关，你需要测试的是*调用使用了正确的参数*。

其他类测试的工作是将您的调用作为“传入命令”进行检查

toll Minitest 为我们提供了针对这种困境的`Minitest::Mock`类。我有两个感兴趣的方法:

我们必须验证什么方法和参数(在测试方法实施之前调用)

预期的电话是否按规定发生了？(在调用对象的方法后调用，以查看它是否触发了预期的外部调用)

我们来看一个例子:

```
# setup
@mock = Minitest::Mock.new
@mock.expect(:outgoing_command, "return_value", [String])

# exercise within your class
@mock.outgoing_command("foo") # => true

# verify at the end
@mock.verify  # => true 
```

Enter fullscreen mode Exit fullscreen mode

文档上有更多关于如何使用`.expect` [的详细说明](https://www.rubydoc.info/gems/minitest/Minitest%2FMock:expect)

复杂的事情通常是让您的类使用 mock，这可以通过以前的存根或依赖注入来完成:

### 用以前的存根

这是很常见的，如果你只有一个非常稳定的接口的外部依赖，这并不坏，但是如果你看到自己每个测试都有几个这样的外部依赖，试着解耦你的类并注入你的依赖，就像后面的例子一样。

例子取自[在 Ruby 中用 Minitest](https://semaphoreci.com/community/tutorials/mocking-in-ruby-with-minitest)
嘲讽

```
require 'test_helper'

class UserTest < ActiveSupport::TestCase
  test '#apply_subscription' do
    mock = Minitest::Mock.new
    mock.expect :apply, true

    SubscriptionService.stub :new, mock do
      user = users(:one)
      assert user.apply_subscription
    end

    assert_mock mock # New in Minitest 5.9.0
    assert mock.verify # Old way of verifying mocks
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 同依赖注入

这种模式不仅更容易测试，而且在需要更改 collaborator 对象时也更容易重构，可能不是同时针对所有用途，想想*适配器模式*或特性标志，或者可能是 A/B-测试选择的策略。

```
class UserGreeter
  def initialize(name:, notifier: Slack)
    @name = name
    @notifier = notifier # store replaceable collaborator object
  end

  def run
    msg = whatever(@name)
    @notifier.notify(msg) # use injected dependency by internal name
  end
end

# on the test
require 'test_helper'

class UserGreeterTest < ActiveSupport::TestCase
  test '#run' do
    mock = Minitest::Mock.new
    mock.expect :notify, true, [String]

    user = User.new(
      user_attrs.merge(subscription_service: mock) 
    )
    assert user.run

    assert_mock mock
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## TL；速度三角形定位法(dead reckoning)

正如我所承诺的，我给你留了一张方便的助记表:

|  | 询问 | 命令 |
| --- | --- | --- |
| *来电* | 结果 | 状态(+结果) |
| *传出* | 烟蒂 | 模拟的 |