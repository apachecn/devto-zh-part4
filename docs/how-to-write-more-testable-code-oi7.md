# 如何编写可测试的代码

> 原文：<https://dev.to/ddarrko/how-to-write-more-testable-code-oi7>

这是我为 Howsy 的工程团队准备的东西。希望对其他人有所帮助。这些例子是用 PHP 编写的，但是可以抽象成任何 OOP 语言

## 什么是单元测试？

这只是一个针对软件的单个“单元”或组件的测试。我们正在测试一个类/方法的最小可能实现。单元测试不依赖于任何依赖项(其他类/库甚至数据库)

在这里阅读更多关于单元测试的内容。

## 为什么重要？

我的意思是我们可以坚持集成测试，对吗？然而，它们很容易写...

任何软件项目的目标都是产生满足业务需求的可维护、可扩展的代码库。编写有效的单元测试有助于我们确保编写可靠的代码。

点击阅读更多关于 solid [的信息。](https://scotch.io/bar-talk/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)

## 怎样才能写出更多可测试的代码？

我上过一个非常简单的课，这是我这些年来看到的一些典型的代码。

```
class ToActive
{
    public function transition(int $accountId): void
    {
        $account = Account::find($accountId);
        if ($this->canTransition($accountId)) {
            $account->setState('active')->save();
        }
    }

    private function canTransition(Account $account): bool
    {
        $validator = new ToActiveValidator();

        if ($validator->validate($account)) {
            return true;
        }
        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，这个类似乎是有效的。它从数据库中找到一个帐户，调用另一个类中的验证方法，并返回 true/false。根据结果，帐户可能会更新，也可能不会更新。

尽管这个类可以工作，但是不可能对进行单元测试，因为两个不同的依赖项被实例化而不是注入。这两段令人不快的代码是

```
$account = Account::find($accountId); 
```

Enter fullscreen mode Exit fullscreen mode

```
$validator = new ToActiveValidator(); 
```

Enter fullscreen mode Exit fullscreen mode

因为它们在我们方法的主体中，所以它们将在单元测试中被调用。这将导致我们依赖其他类的响应来运行测试。这使得我们为这个类编写的任何结果测试都不是真正的单元测试。

我们可以很容易地重构这个类，使它更容易测试，因此更具可扩展性和松散耦合性。

## 重构

```
class ToActive
{
   private $account;
   private $validator;

   public function __construct(AccountInterface $account, StateTransitionValidatorInterface $validator)
   {
       $this->account   = $account;
       $this->validator = $validator;
   }

   public function transition(): void
   {
       if ($this->validator->validate($this->account)) {
           $this->account->setState('active')->save();
       }
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你从上面看到的，我们现在正在注入依赖关系。

我们的类不关心帐户或验证器的具体实现——只关心它们采用了所需的接口。这再次使我们的代码更加松散耦合。将来我们可能会完全改变 StateTransitionValidator 的实现，甚至添加额外的验证器。我们的代码现在不那么紧密耦合，因此更具可测试性。

## 现在让我们开始测试

看看这个类，我们有一个公共方法。我们不应该试图测试私有方法，因为它们是实现细节。如果你发现你的类有太多的私有方法或者觉得它们需要测试，这是一个确定的信号，你的类可以分解成更小的组件。

在公共方法中有两条路径——验证通过，我们保存实体，或者验证没有通过，我们不保存它。

假设验证首先通过，我将编写测试。我们将嘲笑我们的依赖关系(这在最初的类架构中是不可能的)。

```
 public function testToActiveWithPassingValidationShouldTransition()
{
   /** @var Mockery\MockInterface|Account $account */
   $account = Mockery::Mock(Account::class);
   $account->shouldReceive('setState')
       ->with('active')
       ->andReturn($account)
       ->once();
   $account->shouldReceive('save')
       ->andReturn($account)
       ->once();

   /** @var Mockery\MockInterface|ToNotActiveValidator $validator */
   $validator = Mockery::mock(ToActiveValidator::class);
   $validator->shouldReceive('validate')
       ->with($account)
       ->andReturn(true)
       ->once();

   $service = new ToActive($account, $validator);
   $service->transition();
}

public function testToActiveWithFailingValidationShouldNotTransition()
{
   /** @var Mockery\MockInterface|Account $account */
   $account = Mockery::Mock(Account::class);
   $account->shouldReceive('setState')
       ->with('active')
       ->andReturn($account)
       ->never();
   $account->shouldReceive('save')
       ->andReturn($account)
       ->never();

   /** @var Mockery\MockInterface|ToNotActiveValidator $validator */
   $validator = Mockery::mock(ToActiveValidator::class);
   $validator->shouldReceive('validate')
       ->with($account)
       ->andReturn(false)
       ->once();

   $service = new ToActive($account, $validator);
   $service->transition();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 分解它

首先，我们使用[mocking](https://github.com/mockery/mockery)来模仿我们的 account 类。

我们告诉我们的 mock，我们将调用带有 active 参数的 setState 方法。我们还断言这个函数将被恰好调用一次(这是我们实际的测试断言)
我们还断言 save()方法将被恰好调用一次。

我们还模仿我们的具体验证器类。我们告诉我们的 mock，validate 方法将被调用一次，并且它将总是返回 true。

测试将会通过，你可以看到我们已经做到了，实际上没有依赖我们的依赖。这些被模仿并注入到我们的类中，它们的所有方法都返回我们在测试中定义的内容。

### 测试反向

我们还想测试第二条路径(如果验证返回 false，业务规则规定我们不应该转换对象，该怎么办)

看一下第二个测试，你可以看到我们的测试非常相似，只是现在我们强制验证器返回 false。因此，我们的断言已经改变，我们现在希望 setState 和 Save 永远不要在我们的 account 对象上调用。

## 我们取得了什么成就

原始代码很难测试，而且耦合紧密。重构让我们可以让代码更易测试，但是一个很大的副作用是代码现在也更灵活了。

如果你有任何问题，请在评论中告诉我！