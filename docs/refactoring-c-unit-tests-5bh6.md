# 重构 C#单元测试

> 原文：<https://dev.to/techelevator/refactoring-c-unit-tests-5bh6>

单元测试经常被当作二等公民对待，不像我们的产品代码那样被给予同等水平的修饰和重构。结果，它们可能变得脆弱、不清晰、难以维护。

在这篇文章中，我将向您展示一些技巧来保持您的单元测试有用、可维护和相关。

在本文中，我们将测试一个虚构的简历处理应用程序。我们将从一个单一的测试开始，扩展它，然后重构它以保持可用性。

## 样本单元测试

```
public class SpecialCaseTests
{
  [Fact]
  public void MattElandShouldScoreMaxValue()
  {
    // Arrange
    var resume = new ResumeInfo("Matt Eland");
    var job = new JobInfo("Software Engineering Manager", "Some Company", 42);
    resume.Jobs.Add(job);
    var provider = new KeywordScoringProvider();
    var analyzer = new ResumeAnalyzer(provider);

    // Act
    var result = analyzer.Analyze(resume);

    // Assert
    Assert.Equal(int.MaxValue, result.Score);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试中，我们使用 [XUnit 测试框架](https://xunit.net/)对被测系统运行一个单独的动作，然后对它进行断言。注意，我们遵循一个*安排*、*动作*、*断言*模式来区分设置、执行和验证。

即使是这样一个简单的测试，也有一些问题困扰着我。

## 用 Shouldly 作断言

首先，我讨厌断言的语法。它读起来不太好，我经常混淆哪个参数是期望值，哪个是实际值。

而不是:

```
Assert.Equal(int.MaxValue, result.Score); 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢安装 [Shouldly](https://github.com/shouldly/shouldly) NuGet 包，它可以让我编写更清晰的断言。这让我们将代码改为如下:

```
using Shouldly;

public class SpecialCaseTests
{
  [Fact]
  public void MattElandShouldScoreMaxValue()
  {
    // Arrange
    var resume = new ResumeInfo("Matt Eland");
    var job = new JobInfo("Software Engineering Manager", "Some Company", 42);
    resume.Jobs.Add(job);
    var provider = new KeywordScoringProvider();
    var analyzer = new ResumeAnalyzer(provider);

    // Act
    var result = analyzer.Analyze(resume);

    // Assert
    result.Score.ShouldBe(int.MaxValue);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

更容易阅读，不是吗？通过 Shouldy 有各种各样的方法可用于相等、引用相等和集合测试。

*注意:许多人对更流行的[fluent assessments](https://fluentassertions.com/)库深信不疑，但我一般更喜欢 Shouldly 更简洁的语法。*

## 用伪造品隐藏无意义的数值

查看之前的测试，不清楚排列步骤中的哪些值实际上与测试相关。在这个特定的测试中，测试中的行为实际上是这样的规则，如果简历是给“Matt Eland”的，系统应该返回一个最高分(嘿，这是我的示例应用程序，我必须在这里找点乐子)。

通过为测试中不相关的方面提供随机值，[伪](https://github.com/bchavez/Bogus)库可以帮助解决这个问题。

Bogus 有各种各样的随机数据生成器，从随机数到名字到邮政编码和地址到公司名称、商业术语和黑客短语。

下面是我们的测试用例，使用伪造来隐藏不相关的内容:

```
using Bogus;
using Shouldly;

public class SpecialCaseTests
{
  [Fact]
  public void MattElandShouldScoreMaxValue()
  {
    // Arrange
    var resume = new ResumeInfo("Matt Eland");
    var faker = new Faker();
    string title = faker.Hacker.Phrase;
    string company = faker.Company.CompanyName;
    int monthsInJob = faker.Random.Int(1, 4200);
    var job = new JobInfo(title, company, monthsInJob);
    resume.Jobs.Add(job);
    var provider = new KeywordScoringProvider();
    var analyzer = new ResumeAnalyzer(provider);

    // Act
    var result = analyzer.Analyze(resume);

    // Assert
    result.Score.ShouldBe(int.MaxValue);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 提取方法隐藏设置细节

现在我们的测试中无意义的值已经被隐藏了，实际的测试更加清晰，但是设置代码变得难以控制。让我们提取一个添加随机作业的方法。

```
using Bogus;
using Shouldly;

public class SpecialCaseTests
{
  private JobInfo CreateRandomJob() {
    var faker = new Faker();

    string title = faker.Hacker.Phrase;
    string company = faker.Company.CompanyName;
    int monthsInJob = faker.Random.Int(1, 4200);

    return new JobInfo(title, company, monthsInJob);
  }

  [Fact]
  public void MattElandShouldScoreMaxValue()
  {
    // Arrange
    var resume = new ResumeInfo("Matt Eland");
    resume.Jobs.Add(CreateRandomJob());
    var provider = new KeywordScoringProvider();
    var analyzer = new ResumeAnalyzer(provider);

    // Act
    var result = analyzer.Analyze(resume);

    // Assert
    result.Score.ShouldBe(int.MaxValue);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那就清楚多了！在这个过程中，我们可以提取一个方法来创建一个简历分析器并分析简历。

```
using Bogus;
using Shouldly;

public class SpecialCaseTests
{
  private JobInfo CreateRandomJob() {
    var faker = new Faker();

    string title = faker.Name.JobTitle;
    string company = faker.Company.CompanyName;
    int monthsInJob = faker.Random.Int(1, 4200);

    return new JobInfo(title, company, monthsInJob);
  }

  private AnalyzerResult Analyze(ResumeInfo resume) {
    var provider = new KeywordScoringProvider();
    var analyzer = new ResumeAnalyzer(provider);

    return analyzer.Analyze(resume);
  }

  [Fact]
  public void MattElandShouldScoreMaxValue()
  {
    // Arrange
    var resume = new ResumeInfo("Matt Eland");
    resume.Jobs.Add(CreateRandomJob());

    // Act
    var result = Analyze(resume);

    // Assert
    result.Score.ShouldBe(int.MaxValue);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看一个非常简洁易读的测试方法。作为一个额外的好处，如果我们改变 Analyzer 的签名或者想要使用不同的提供者进行测试，我们可以在一个方法中替换它，而不是必须在每个单独的测试用例中维护它。

是时候扩展我们的测试了，但是在此之前，让我们引入一个其他测试类可以继承的基类。

## 提取抽象类以改进测试

我们可以增加两个私有方法的可见性，并将它们放入一个名为 ResumeTestsBase 的新抽象类中，然后让 SpecialCaseTests 继承它。

这里是我们的基类:

```
using Bogus;

public abstract class ResumeTestsBase
{

  private Faker _faker;
  protected Faker Faker => _faker ?? _faker = new Faker();

  protected JobInfo CreateRandomJob(int monthsInJob = -1) {
    string title = Faker.Name.JobTitle;
    string company = Faker.Company.CompanyName;

    // Ensure we have a valid months in job if not specified
    if (monthsInJob <= 0) {
      monthsInJob = Faker.Random.Int(1, 4200);
    }

    return new JobInfo(title, company, monthsInJob);
  }

  protected AnalyzerResult Analyze(ResumeInfo resume) {
    var provider = new KeywordScoringProvider();
    var analyzer = new ResumeAnalyzer(provider);

    return analyzer.Analyze(resume);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们将 Faker 移动到了一个延迟实例化的属性中，这样我们就可以在将来的其他方法中重用它。我们还将`CreateMonthsInJob`方法参数化，以帮助将来的测试。

这个基类允许我们拥有一个非常简单和集中的测试类:

```
using Shouldly;

public class SpecialCaseTests : ResumeTestsBase
{
  [Fact]
  public void MattElandShouldScoreMaxValue()
  {
    // Arrange
    var resume = new ResumeInfo("Matt Eland");
    resume.Jobs.Add(CreateRandomJob());

    // Act
    var result = Analyze(resume);

    // Assert
    result.Score.ShouldBe(int.MaxValue);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以添加一个新的类来测试被测系统的新方面。

```
using Shouldly;

public class MonthsInJobTests : ResumeTestsBase
{
  [Fact]
  public void FiveMonthsInJobShouldScoreAFive()
  {
    // Arrange
    var resume = new ResumeInfo(Faker.Name.FullName);
    resume.Jobs.Add(CreateRandomJob(5));

    // Act
    var result = Analyze(resume);

    // Assert
    result.Score.ShouldBe(5);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那很好，但是我们应该测试不止一个值。扩大规模开始出现问题:

```
using Shouldly;

public class MonthsInJobTests : ResumeTestsBase
{
  [Fact]
  public void FiveMonthsInJobShouldScoreAFive()
  {
    // Arrange
    var resume = new ResumeInfo(Faker.Name.FullName);
    resume.Jobs.Add(CreateRandomJob(5));

    // Act
    var result = Analyze(resume);

    // Assert
    result.Score.ShouldBe(5);
  }

  [Fact]
  public void OneMonthInJobShouldScoreAOne()
  {
    // Arrange
    var resume = new ResumeInfo(Faker.Name.FullName);
    resume.Jobs.Add(CreateRandomJob(1));

    // Act
    var result = Analyze(resume);

    // Assert
    result.Score.ShouldBe(1);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

复制因素又开始出现了。谢天谢地都是现代的。NET 测试框架支持参数化测试。在 XUnit 中，这被称为理论，它看起来像这样:

```
using Shouldly;

public class MonthsInJobTests : ResumeTestsBase
{
  [Theory]
  [InlineData(1)]
  [InlineData(5)]
  [InlineData(10)]
  [InlineData(900)]
  public void ResumesShouldScoreForTotalMonthsInJob(int monthsInJob)
  {
    // Arrange
    var resume = new ResumeInfo(Faker.Name.FullName);
    resume.Jobs.Add(CreateRandomJob(monthsInJob));

    // Act
    var result = Analyze(resume);

    // Assert
    result.Score.ShouldBe(monthsInJob);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`Theory`测试，与使用`Fact`属性的两个测试相比，使用一个方法的四个测试代码行更少。

测试运行者将把这个测试用例视为四个独立的测试，并单独运行每个测试，将`InlineData`传递给方法的参数。

* * *

这些只是创建清晰、简洁和可维护的单元测试的一些基础。还有许多其他的库和技术，但是这些基本技术将帮助您构建一个可靠的测试套件，在简单性、实用性和可维护性方面大放异彩。