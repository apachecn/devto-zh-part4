# 测试双打的搭便车指南

> 原文：<https://dev.to/xoubaman/the-hitchhiker-guide-to-test-doubles-53m3>

绝对必要，但经常被滥用，它们有不同的味道，服务于不同的目的。他们是测试替身。在这本内容丰富的指南中了解它们。

这篇文章中的代码样本是用 PHP 编写的，引用了该语言及其生态系统的一些细节，尽管这些概念应该适用于大多数 OOP 编程语言。

## 什么是测试替身？

马丁·福勒直截了当地定义了测试替身:

> “双重测试”是一个通用术语，适用于出于测试目的替换生产对象的任何情况。

注意 *any case* 是很重要的:它不仅适用于单元测试中的类。当我们在测试环境中使用不同的数据库系统时，或者当我们使用模拟服务器而不是访问外部 API 时，我们使用的是测试替身。

## 我们什么时候用测试替身？

被测试的主题(SUT)是我们在某个时刻测试的系统的一组元素。这组元素定义了 SUT 的边界。

我们使用测试替身来替换与这些界限之外的世界交互的元素。

在每种情况下，正确识别 SUT 非常重要，这样我们就可以知道应该用双精度值替换什么。

在这篇文章中，我们将看到不同场景和测试类型的例子。

## 为什么我们要使用测试替身？

当我们编写测试时，我们应该只关心 SUT 的行为。当替换这些与外界交互的元素时，测试 double 的行为类似于 doubled 元素，但去除了它们的大部分复杂性，在执行逻辑时保持测试对象被封装，并优化我们通过测试获得的反馈循环。

[![Suspicious](img/441c12ab812db0d26a8899306133ab51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SakW6SKn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/st99s1ybhy1bhrgvmv0c.png)

假设我们正在测试一个负责在餐馆预订桌子的服务。它的行为包括检查是否有可用的桌子，如果有，创建预订，并将其保存在数据库中。

这个预订服务是我们的 SUT，在单元测试环境中，我们想要测试的行为在*保存它*时停止。我们感兴趣的是预订被保存的事实，而不关心数据库的类型、涉及的表的数量等。与服务逻辑无关的方面。

在这个场景中使用 test double 将允许我们断言预订被保存，而没有真实数据库所带来的复杂性。

不用说，数据库仍然很重要，应该在另一个环境中进行测试，在这个环境中，SUT 覆盖了其边界内的数据库。

## 使用测试替身

通常，在单元测试中，我们将测试加倍与类的替换联系起来。然而，如前所述，测试 double 可能会替换生产代码中的任何内容。

我们正在处理的 SUT 和测试类型是理解何时需要使用测试替身以及所需的测试替身类型的关键因素。

### 在功能测试中使用测试替身

功能测试是高层次的测试，我们对应用程序的很大一部分进行测试，从一个大的整体到一系列的服务——这里的服务指的是高层次的服务，比如独立的应用程序。

在高层次的功能测试中，SUT 通常是我们系统的一个重要部分，而不是整个系统。

在这种情况下，我们通常希望使用尽可能接近生产的配置，可能是真实的数据库、文件系统、队列...在使用测试双精度测试的更细粒度测试中，我们通常想要删除的元素。

然而，我们不希望我们的测试依赖于完全不受我们控制的元素，比如外部系统。当外部系统由于某种原因关闭时，这将导致测试失败，即使我们的应用程序如预期的那样运行，加上一些其他讨厌的影响，比如允许从我们的测试环境内部连接到外部世界。

为了防止这种情况，我们可以使用存根服务器将整个外部系统加倍，在存根服务器中，我们为具体的请求数据配置固定的响应。显然，来自我们的存根服务器的响应必须与真实的完全相同。

以同样的方式，我们可以替换电子邮件系统，以避免在运行测试套件时向客户发送电子邮件。

在一个高层次的上下文中，测试替身给了我们在生产环境中无法控制的元素的控制权。

### 在集成测试中使用测试替身

集成测试涵盖了系统不同组件之间的交互。这个组件可以是一个数据库、通过 HTTP 对某个 API 的调用、应用程序中的某个其他模块，...

当进行集成测试时，SUT 是由组件之间的通信定义的。这里的关键是沟通，而不一定是技术、系统或线路另一端的任何东西。

最有可能的是，我们将通过一些供应商库与数据库进行交互，比如说 Doctrine，PHP 生态系统中一个众所周知的 ORM。原则是集成测试中的另一个组件，我们的组件与之通信。

在这种情况下，我们不太关心正在使用的数据库系统——最终，这就是为什么我们在其上使用一个抽象层的原因——所以用另一个性能更好的来替换它是完全可以的，例如使用 SQLite 而不是 MySQL。

另一个例子:某个类执行对 API 的 HTTP 请求。在这种情况下，我们不能用另一种类似的、更方便的技术来代替 HTTP。但是我们不需要一个真正的服务来响应请求，拥有一个带有一些预定义响应的服务器就足够了。

考虑到这一点，我们应该一次测试一种通信方式。如果我们在一个类中测试与数据库的通信，该类也将消息放入队列并执行 HTTP 请求，那么我们必须对不使用数据库的元素使用 test doubles。当数据库通信加倍时，他们将有他们自己的测试。

### 在单元测试中使用测试替身

单元测试涵盖了我们应用程序的单个组件的行为。通常，这意味着从一个类到一堆负责执行具体动作的类。这就是这种情况下的 SUT。

对于充当我们的 SUT 之外的世界的通信点的类，我们希望使用 test doubles 来代替。

要做到这一点，我们需要一种有效的替代方法。最常见和最理想的方法是通过依赖注入。

通常，我们会发现构造函数依赖注入，所以我们注入测试 double:

```
final class MyClass
{
    public function __construct(Logger $logger) {...}
}

final class MyClassTest extends TestCase
{
    protected function setUp()
    {
        $this->logger = $this->buildLoggerDouble();
        $this->myClass = new MyClass($this->logger);
    }

    public function testSomeLogic(): void
    {
        //Configure the logger double to behave in a certain way
        //and exercise the logic of MyClass we want to test
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

其他类型的依赖注入将以类似的方式工作。

在任何情况下，能够进行单元测试和在类级别使用测试替身的第一步是提取它的依赖关系，所以当你不在这种情况下，花时间去做。

## 写作测试双打

这里我们主要指的是单元测试和集成测试环境中的测试加倍。

当涉及到编写测试双精度浮点数时，我们有一些选择。第一个决定是使用一个模仿库或者自己编写测试替身。

模仿库给了我们对测试替身的完全控制。他们总是创建一个模拟，根据我们添加的配置数量，它将表现为一种类型或另一种类型的 test double。

```
//Using PHPUnit built-in mock engine
$loggerMock = $this->createMock(Logger::class); 
```

Enter fullscreen mode Exit fullscreen mode

使用 mocking 库可以快速生成测试 doubles，当代码设计得不好时，它会非常方便。缺点是在测试和双重依赖之间以及与库本身之间存在紧密耦合。很容易犯测试实现而不是行为的错误，因为它们允许深层次的配置，使得重构更加困难，测试更加难以跟踪。

当我们选择自己编写测试 double 时，我们需要一种方法来编写与被替换的类相同类型的 double。为此，我们可以抽象一个接口中涉及的类方法，或者扩展覆盖构造函数和所需方法的原始类:

```
interface Logger
{
    public function log(string $someMessage): void;
}

class TheRealLogger implements Logger
{
    public function log(string $someMessage): void
    {
        //Stuff involving database, filesystem, vendors,...
    }
}

//Test double implementing the interface
final class MyDoubledLogger implements Logger
{
    public function log(string $someMessage): void
    {
        //Do nothing, it is a test double!
    }
}

//Test double extending the original class
final class MyDoubledLogger extends TheRealLogger
{
    public function log(string $someMessage): void
    {
        //Do nothing, it is a test double!
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要 double 的合作者已经实现了一个接口，那么它是一个不用动脑筋的问题，我们实现 double 作为它的一个新实例。如果还没有接口，我们应该评估哪种选择更可取:继续继承及其所有缺点，或者添加新的抽象来维护新的接口。

自制的测试替身通常比库生成的模拟更容易理解:它们是我们定期编写的代码的简单版本，并且它们坚持与真实类相同的类公共 API。他们对 IDE 重构也非常友好。主要的缺点是，与使用 mocking 库相比，它们需要额外的时间来编写，有时它们变得足够复杂，值得进行自己的测试。

我个人更喜欢尽可能多地编写自己的测试替身。它们并不耗费时间，而且它们附带了一些额外的语法糖，使得测试更容易阅读，比如命名构造函数和显式断言。然而，有时候库模拟更方便。我们一会儿将分析不同的情况。

知道了选项之后，是时候进行所有不同类型的双打测试了。

## 假人

假人是最简单的测试替身。doubled 类的所有方法都是无效的。这意味着它们什么也不做，或者只做最少的事情来编译代码，并且不会在运行时中断。当我们不关心当前测试中这种依赖性的使用时，我们使用虚拟对象。

[![Alt Text](img/75bc0d3f008b364d8ca296639e5d2c96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x76C1wBj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qod5e3gir6xkgsy3cv44.png)

日志记录器是一个常见的例子:我们编写测试来覆盖一些与日志记录无关的逻辑。

```
//Selfmade dummy
final class MyDoubledLogger implements Logger
{
    public function log(string $someMessage): void
    {
        //Do nothing, it is a test double!
    }
}

//A PHPUnit mock with no configuration acts as a dummy
$loggerMock = $this->createMock(Logger::class); 
```

Enter fullscreen mode Exit fullscreen mode

## 存根

当我们希望我们的测试 double 返回一个固定的结果时，我们使用一个存根。

[![Say "I am Krusty the Clown"](img/57317dd7719e4bdae780fef589f3ba55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rJdWcg2g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/br0cfnhibzxcc7zmi4da.png)

如果我们的测试 double 用一个 stub 替换了一个执行 HTTP 请求并返回某个值的类，我们设置这个值，double 将直接返回它:

```
final class HttpExchangeRateProvider implements ExchangeRateProvider
{
    public function getRateFor(string $currency): float
    {
        //Do a request
    }
}

//Writing our own stub
final class StubbedExchangeRateProvider implements ExchangeRateProvider
{
    private float $rate;

    public function willReturnRate(float $rate): void
    {
        $this->rate = $rate;
    }

    public function getRateFor(string $currency): float
    {
        return $this->rate;
    }
}

//Configuring a PHPUnit mock to act as a stub
$exchangeRate = $this->createMock(ExchangeRateProvider::class);
$exchangeRate
    ->method('getRateFor')
    ->willReturn($someRate); 
```

Enter fullscreen mode Exit fullscreen mode

## 假货

Fakes 是一些聪明的家伙，他们执行一些实际的逻辑，以类似于 doubled 类的方式运行，但是这种逻辑不能覆盖所有的情况，并且不能在产品代码中使用。

[![Endut! Hoch Hetch!](img/da498a4560818b65a6bdb36c3e8bd5ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k6c2OsT0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnnu692xskeypq4f929f.png)

一个经典的例子是内存中的存储库，它保存了添加元素的副本，甚至能够找到它们:

```
// A fake made by us
final class InMemoryUserRepository implements UserRepository
{
    public function add(User $user): void
    {
        $this->users[$user->id()->toString()] = $user;
    }

    public function ofId(UserId $id): User
    {
        return $this->users[$id->toString()];
    }
}

//Configuring a PHPUnit mock to act as a fake starts to become verbose
$repository = $this->createMock(UserRepository::class);

$id = new UserId('some id');
$user = new User($id);

$repository
    ->method('ofId')
    ->with([$userId])
    ->willReturn($user); 
```

Enter fullscreen mode Exit fullscreen mode

假货可以变得足够复杂，值得对它们进行检测。例如，我通常为我的内存存储库编写测试。

## 奸细

间谍是测试替身，允许我们控制如何称呼他们。他们跟踪他们的方法是如何被调用的，这样我们就可以根据它们的用法来做断言。

[![It takes a big man to fill a hat like that](img/b459fa0fabf7ca34765a79dd863efbdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KXFDPMXE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y6okplbur51c9ere1whn.png)

我们使用间谍，当它是重要的，以确保他们被使用一定的次数，一定的参数，或两者兼而有之。例如，如果某个操作只执行一次对我们来说至关重要，因为它耗费资源，或者运行两次会触发一些不良逻辑。

然而，我们必须小心:大量使用间谍可能是测试的一种味道，测试关注的是实现而不是行为。

```
final class EmailSenderSpy implements EmailSender
{
    private $emailsSent = 0;

    public function send(Email $email): void
    {
        $this->emailsSent++;
    }

    public function numberOfEmailsSent(): int
    {
        return $this->emailsSent;
    }
}

$emailSender = $this->createMock(EmailSender::class);
$emailSender
    ->expects($this->once()) //here we expect once, at least once, a concrete number...
    ->method('send'); 
```

Enter fullscreen mode Exit fullscreen mode

前面的例子控制了间谍的使用次数。间谍还可以控制使用时提供的参数:

```
final class EmailSenderSpy implements EmailSender
{
    private $emails = [];

    public function send(Email $email): void
    {
        $this->emails[] = $email;
    }

    public function usages(): int
    {
        return count($this->emails);
    }

    public function emailsSent(): array
    {
        return $this->emails;
    }
}

$emailSender = $this->createMock(EmailSender::class);
$emailSender
    ->expects($this->once()) //here we expect once, at least once, a concrete number...
    ->method('send')
    ->with($someEmail); 
```

Enter fullscreen mode Exit fullscreen mode

与使用模仿库生成的断言相比，使用自制的间谍，我们有可能添加一些语法糖来获得更明确和更有意义的断言——我们将在讨论模仿时回到这一点。对于库，我们在测试的安排部分设置期望，但是在断言部分不做任何事情，因为期望是隐式断言。考虑以下断言 spy 用法的方式，只是给我们的 spy 添加一些简单的方法:

```
$this->emailSender = new EmailSenderSpy();
self::assertEquals(3, $this->emailSender->numberOfEmailsSent());
self::assertTrue($this->emailSender->sentANumberOfEmail(3));
self::assertTrue($this->emailSender->wasNotUsed());
self::assertTrue($this->emailSender->sentAnEmailTo('thor@asgard.com')); 
```

Enter fullscreen mode Exit fullscreen mode

使用 mock 库的等价物将需要更复杂的 mock 设置。

像往常一样，我们必须考虑可读性的提高何时值得投资。我们的间谍可能会变得太复杂，并开始做一些容易出错的杂耍，与使用嘲讽库相比，这可能不值得实现。

### 模仿

模仿是模仿库提供的。我们不能自己编写它们，因为我们会为此编写一个嘲笑库。它们是可广泛配置的，基于我们如何设置它们，它们将充当一个假人、一个树桩、一个假货或一个间谍，正如我们所看到的。

[![Almost](img/77e44da147c3a37fe43dea77339fb7df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i26cIsvD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ubnfvf573804h43o16q1.png)

到目前为止展示的例子几乎没有配置，但是当配置更复杂的行为时，事情会变得非常冗长。总的来说，就可读性而言，自制的替身往往比模仿更直接。

Mocks 还允许进行一些奇特的配置，比如期望在每种情况下以一定的顺序用一定的参数调用，使用闭包计算参数，等等。虽然有些情况下这看起来很方便，但是把它们看作是与实现耦合的弱测试的可能气味。

## 提示和窍门

*不要重复自己*

很有可能我们会在不同的测试中使用不同配置的同一个测试替身。隐式编写我们自己的 doubles 允许重用，但是 mocking 库不允许。为了避免这种情况，考虑使用工厂来构建您的模拟。

这种模式意味着以某种方式将创建的模拟与测试用例联系起来，这样就可以评估模拟期望。下面是一个使用 PhpUnit 的例子:

```
//In a PhpUnit test case
$userRepositoryMockFactory = new UserRepositoryMockFactory($this);

$repositoryMock = $userRepositoryMockFactory->alwaysFindingUser($user); 
```

Enter fullscreen mode Exit fullscreen mode

*匿名类*

快速使用 test doubles 的一个廉价方法是使用匿名类实现一个接口:

```
//a dummy using an anonymous class
$this->logger = new class implements Logger
{
    public function log(string $someMessage): void
    {
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里的缺点是它不能在其他测试中重用，除非我们把它移到某个模拟工厂。

我认为这种方法可以更快地编写测试，但是通常我会在后面的步骤中将 doubles 移到一个单独的类中。

*自分流*

这种技术包括使用测试用例本身作为替身，让它实现接口:

```
class UserAuthenticatorTest extends TestCase implements UserCredentials
{
    public function getUsername(): string
    {
        return $this->username
    }

    public function getPassword(): string
    {
        return $this->password
    }

    public function testItDoesNotAllowWrongUserPasswordPairs(): void
    {
        $this->password = 'an invalid password';
        $authenticator = new UserAuthenticator();
        self::assertFalse($authenticator->authenticateWithCredentials($this))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

和匿名类一样，在开始时加快速度是一个很好的技巧，但是我更喜欢在测试完成并通过后再进行单独的双打。自分流在编写的时候可能很有意义，但是对于一个没有上下文的开发人员——就像你第二天一样——看到测试用例作为一个依赖项或参数被通过可能会令人兴奋不已。

*给我们自己的替身加糖*

正如我们所看到的，我们可以增加间谍来提高断言的明确性和可读性。一个具有类似好处的类似技术是将命名构造函数添加到我们的测试替身中。

比较以下验证程序的双测试:

```
class UserValidatorStub extends UserValidator
{
    private bool $validationResult;

    public function __construct(bool $validationResult)
    {
        $this->validationResult = $validationResult;
    }

    public function validate(UserData $userData): bool
    {
        return $this->validationResult;
    }

    public static function alwaysPassing(): self
    {
        return new self(true);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在测试中，我们将使用静态工厂方法来明确指出验证器的行为方式。只有一行。对于模拟来说，它会更加冗长，可读性更差。

```
$this->userValidator = UserValidatorStub::alwaysPassing();

//vs the way less explicit
$this->userValidator = $this->createMock(UserValidator::class);
$this->userValidator
    ->method('validate')
    ->willReturn(true); 
```

Enter fullscreen mode Exit fullscreen mode

## 测试加倍，代码没有设计好

在设计糟糕的代码中，即插即用测试双精度可能不容易。由于可疑的实施决策，SUT 的边界可能是模糊的。

例如，考虑一下广泛使用活动记录模式的应用程序的可悲现实。活动记录是一种在特定环境下工作良好的模式，但是由于其通常的实现——流畅的接口、混合的建模和对数据库的访问——对单元测试来说是一种痛苦。

设计糟糕的代码迫使我们去做*肮脏的*事情，作为通向更好设计的中间步骤。这个渐进重构过程的一个关键部分是提取依赖项，并在测试中加倍。在我们认识到什么是依赖关系的适当抽象之前，我们可以全力以赴使用模拟库，甚至是大型的、外来的配置，只要它们完成了工作，并且我们意识到我们正在进入一种中间状态，为更好、更可测试的设计奠定基础。

另一个值得注意的事实是，在一个混乱的代码库中，我们可能不确定我们的 SUT 的一些合作者最终是否会在其边界之外运行逻辑。一个可悲的现实是，显然无辜的值对象巧妙地访问了各种基础设施元素。再次强调，疯狂模仿是迈向更好设计的一步，这是完全正确的。

## 最后的话

测试替身是测试中的核心工具，和许多其他工具一样，误用它们会带来更多的痛苦而不是价值。希望在读完这篇文章后，你对他们是什么，为什么，什么时候有一个更好的了解。

如果你同意，不同意或者只是想补充一些东西，请不要犹豫，留下评论。非常欢迎反馈。