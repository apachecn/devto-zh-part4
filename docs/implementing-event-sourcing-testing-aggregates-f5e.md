# ★实施事件源:测试集合

> 原文：<https://dev.to/freekmurze/implementing-event-sourcing-testing-aggregates-f5e>

今年早些时候[我们发布了 laravel-event-sourcing](https://freek.dev/1313-laravel-event-projector-v2-has-been-released) 的 v2。这个包可能是在 Laravel 中开始事件采购的最简单的方法。v2 的一个显著特征是增加了[集合](https://docs.spatie.be/laravel-event-projector/v2/using-aggregates/writing-your-first-aggregate/)。

今天我们发布了这个包的另一个新版本，增加了测试方法。[这些方法](https://docs.spatie.be/laravel-event-projector/v2/using-aggregates/testing-aggregates/)允许您验证聚合行为是否正确。在这篇文章中，我将展示一个例子，并解释测试方法是如何实现的。

这些测试方法的灵感来自于他的 Eventsauce 包中令人敬畏的测试方法 [Frank De Jonge](https://twitter.com/frankdejonge/) 。

## 测试一个骨料

聚合是根据过去的事件决定记录事件的类。要了解更多关于它们的一般目的和背后的想法，请阅读本节关于[使用聚合基于过去](https://docs.spatie.be/laravel-event-projector/v2/getting-familiar-with-event-sourcing/using-aggregates-to-make-decisions-based-on-the-past)做出决策的内容。在本文的剩余部分，我们将假设您知道如何使用聚合。

假设您有一个`AccountAggregateRoot`来处理银行账户的加减金额。这个账户的限额是-5000 美元。

```
use Spatie\EventProjector\AggregateRoot;

class AccountAggregateRoot extends AggregateRoot
{
    /** @var int */
    private $balance = 0;

    /** @var int */
    private $accountLimit = -5000;

    public function createAccount(string $name, string $userId)
    {
        $this->recordThat(new AccountCreated($name, $userId));

        return $this;
    }

    public function addMoney(int $amount)
    {
        $this->recordThat(new MoneyAdded($amount));

        return $this;
    }

    protected function applyMoneyAdded(MoneyAdded $event)
    {
        $this->balance += $event->amount;
    }

    public function subtractMoney(int $amount)
    {
        $this->hasSufficientFundsToSubtractAmount($amount)
            ? $this->recordThat(new AccountLimitHit($amount))
            : $this->recordThat(new MoneySubtracted($amount));
    }

    protected function applyMoneySubtracted(MoneySubtracted $event)
    {
        $this->balance -= $event->amount;
    }

    private function hasSufficientFundsToSubtractAmount(int $amount): bool
    {
        return $this->balance - $amount >= $this->accountLimit;
    }
} 
```

现在让我们来测试一个帐户不能超出其限额的规则。

```
// in a PHPUnit test

/** @test */
public function it_can_subtract_money()
{
    AccountAggregateRoot::fake()
        ->given(new SubtractMoney(4999))
        ->when(function (AccountAggregate $accountAggregate) {
            $accountAggregate->subtractMoney(1);
        })
        ->assertRecorded(new MoneySubtracted(1))
        ->assertNotRecorded(AccountLimitHit::class);
}

/** @test */
public function it_will_not_make_subtractions_that_would_go_below_the_account_limit()
{
    AccountAggregateRoot::fake()
        ->given(new SubtractMoney(4999))
        ->when(function (AccountAggregate $accountAggregate) {
            $accountAggregate->subtractMoney(2);
        })
        ->assertRecorded(new AccountLimitHit(2))
        ->assertNotRecorded(MoneySubtracted::class);
} 
```

你可以把上面的测试写得稍微短一点。给定的事件可以传递给`fake`方法。您也不需要使用`when`功能。

```
/** @test */
public function it_will_not_make_subtractions_that_would_go_below_the_account_limit()
{
    AccountAggregateRoot::fake(new SubtractMoney(4999))
        ->subtractMoney(2)
        ->assertRecorded(new AccountLimitHit(2))
        ->assertNotRecorded(MoneySubtracted::class);
} 
```

## 实施集料试验方法

在开始实现这些测试方法之前，我认为这会有点令人生畏。事实证明，这并不难。

要创建一个集合，你需要让一个类扩展我们的`Spatie\EventProjector\AggregateRoot`类。

```
class AccountAggregateRoot extends AggregateRoot {} 
```

为了保持`AggregateRoot`的实现简洁，我想避免在该类本身上添加断言。我唯一加的方法是`fake`。调用这个类将返回一个新的`FakeAggregateRoot`实例，其中封装了测试中的聚合(在我们的例子中是`AccountAggregateRoot`)。

这里的实现取自 [`AggregateRoot`](https://github.com/spatie/laravel-event-projector/blob/bfa2b4c9e15a5b2ba7f016584f07a81074856e37/src/AggregateRoot.php#L87-L97) 。

```
/**
 * @param \Spatie\EventProjector\ShouldBeStored|\Spatie\EventProjector\ShouldBeStored[] $events
 *
 * @return $this
 */
public static function fake($events = []): FakeAggregateRoot
{
    $events = Arr::wrap($events);

    return (new FakeAggregateRoot(app(static::class)))->given($events);
} 
```

在`FakeAggregateRoot`中所有的测试方法像`given`、`when`、`assertRecorded`等等都是活的。

我们来看一个最简单的:`assertNothingRecorded`。

```
public function assertNothingRecorded()
{
    PHPUnit\Framework\Assert::assertCount(0, $this->aggregateRoot->getRecordedEvents());

    return $this;
} 
```

在上面的方法中，我们获取封装的聚合根的记录事件。PHPUnit 断言用于确定该数组是否为空。否则，PHPUnit 测试将会失败。

如果你有兴趣，可以看看[的其他测试方法](https://github.com/spatie/laravel-event-projector/blob/master/src/FakeAggregateRoot.php)的实现。

## 在关闭

我希望你喜欢这个新测试方法的小旅行。之前我已经写了两篇关于这个包的实现细节的文章:这里是如何实现聚合的，这里是关于如何改进开发者体验的[的文章](https://freek.dev/1329-implementing-event-sourcing-improving-the-developer-experience)。

要了解更多关于这个软件包的信息，请阅读简介文章或文档。

一定要看看我们团队之前制作的 PHP & Laravel 包 [的大列表。](https://spatie.be)