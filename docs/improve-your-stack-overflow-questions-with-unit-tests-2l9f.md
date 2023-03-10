# 用单元测试改善你的堆栈溢出问题

> 原文：<https://dev.to/scotthannen/improve-your-stack-overflow-questions-with-unit-tests-2l9f>

假设您正试图编写代码来解决这个问题:

> 我有开始日期和结束日期。我想计算在这两个日期之间某一天(比如 10)出现的次数。

你试着写它，陷入困境，并决定尝试寻求关于[堆栈溢出](https://stackoverflow.com/)的帮助。你张贴上面的问题，并包括来自你的 Winforms 应用程序的代码，看起来像这样:

```
public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
    }

    private void btnCalculate_Click(object sender, EventArgs e)
    {
        try
        {
            var startDate = DateTime.Parse(txtStartDate.Text);
            var endDate = DateTime.Parse(txtEndDate.Text);
            var day = int.Parse(txtDay.Text);

            var numberOfTimesDayHappensInDateRange = 0;

             // Some code that tries to calculate this, but isn't getting
             // the results you want

            MessageBox.Show($"The answer is {numberOfTimesDayHappensInDateRange}!");
        }
        catch
        {
            MessageBox.Show("I think something is wrong with the values you entered!");
        }
    }
} 
```

可能会发生以下情况的一些变化:

*   你会被否决的
*   你的问题会结束
*   你不会得到任何回应
*   你会得到与你的问题无关的答案
*   有人会留言说，“我不知道你想做什么。你能举例说明你期望的结果吗？”

当这种情况发生时，我们很难过。我们如何改进我们的问题？一种方法是编写一个单元测试，显示我们的输入和我们期望的结果。

免责声明:我问过 Stack Overflow 上的其他人对此有何看法。答案相当于，“当然，但是没有人会这么做。”他们可能是对的。不过没关系。我是一个乐观主义者，我喜欢认为这将有助于某人，即使它与询问关于堆栈溢出的问题无关。

如果我们可以通过将包含问题的代码与其他代码隔离开来，使这一点更容易理解，会怎么样呢？

我们可以从把这部分代码放在一个单独的类和方法中开始:

```
public static class DateFunctions
{
    public static int NumberOfTimesDayOfMonthOccursInDateRange(
        DateTime startDate, DateTime endDate, int dayOfMonth)
    {
        // Some code that tries to calculate this, but isn't getting
        // the results you want
    }
} 
```

如果还不清楚的话，这篇文章不包括这个功能的工作版本。这是关于如何提出问题，而不是解决方案本身。

这是一个很长的函数名，不是吗？但是它应该返回什么也更清楚了。如果我们只是把它叫做`NumberOfTimesDayOccursInDateRange`，那么我们谈论的是一个日历日(1，15，27)而不是一周中的某一天(星期一，星期二)可能就不那么明显了。)

到目前为止，我们所做的只是将代码从一个地方移到另一个地方。这是一个开始。现在，想要理解这个方法并帮助我们的人不必查看所有的额外代码。他们所看到的正是我们想要弄清楚的部分。

对我们来说，不同的输入会得到什么样的结果似乎是显而易见的，但是我们如何才能把这一点清楚地传达给其他人呢？

实现这一点的一种方法是使用一个或多个单元测试。如果我们的解决方案中还没有单元测试项目，我们可以添加一个:

右键点击*解决方案*，选择添加- >新项目… - > Visual C# - >测试- >单元测试项目(。NET 框架)。

还有 NUnit、xUnit 等其他选项，但我们只看微软的默认版本。其他的基本上做同样的事情，但是有额外的能力。

我们的项目将包含一个具有单一单元测试的单一测试类，就像这样:

```
namespace UnitTestProject1
{
    [TestClass]
    public class UnitTest1
    {
        [TestMethod]
        public void TestMethod1()
        {
        }
    }
} 
```

`[TestClass]`属性表明该类包含测试。属性表明 method_is_ a test。这就是测试运行人员如何知道这个项目中代码的哪些部分是我们需要运行的测试。

为了简单起见，单元测试是这样一种方法:如果运行时没有抛出任何异常，则*通过*，如果抛出异常，则*失败*。我们编写这些方法，这样如果我们的代码做了我们期望它做的事情，它就不会抛出异常。会过去的。如果不按我们预期的那样做，它将抛出一个异常并失败。

这一点用一个例子会更清楚。首先，让我们将我们的`DateFunctions`类复制到与测试相同的文件中。我们没必要这么做。最终，这个类将会出现在我们“真正的”项目中——而不是测试项目中——并且我们的测试项目将会引用它。但是在这一步，将它们放在一个地方通常比较方便。这将有助于解决堆栈溢出问题。这也是我在回答别人的问题时的工作方式，我想确保我的回答是有效的。我只是创建一个测试项目，并编写代码和单元测试。

下面是包含了`DateFunctions`类和一个简单单元测试的文件的样子:

```
[TestClass]
public class DateFunctionTests
{
    [TestMethod]
    public void NumberOfTimesDayOfMonthOccursInDateRange_returns_correct_result()
    {
        var startDate = DateTime.Parse("1/1/2019");
        var endDate = DateTime.Parse("2/15/2019");
        var dayOfMonth = 14;
        var expectedResult = 2;
        var actualResult = DateFunctions.NumberOfTimesDayOfMonthOccursInDateRange(
            startDate, endDate, dayOfMonth);
        Assert.AreEqual(expectedResult, actualResult);
    }
}

public static class DateFunctions
{
    public static int NumberOfTimesDayOfMonthOccursInDateRange(
        DateTime startDate, DateTime endDate, int dayOfMonth)
    {
        return 0; 
        /*
         * I just put this here so it compiles. This would be your
         * work in progress code. You've written something. It just
         * doesn't return the right results.
         */
    }
} 
```

我让测试变得有点冗长，将所有东西都声明为变量，以便于理解。它表明我们将要用特定的输入来执行我们的函数，我们知道我们期望的结果。

最后一条语句- `Assert.AreEqual` -将检查我们的实际结果是否与我们的预期结果相同。如果不是，它将抛出一个异常，测试将失败。

为了进行测试，右键单击测试类并选择 **Run Test(s)** 。项目将编译，Visual Studio 将运行测试，一个“测试资源管理器”窗口将打开，向我们显示结果。

这个测试将会失败，因为我们的函数返回了 *0* ，而不是预期的结果。如果我们通过单击红色的“X”图标深入查看测试结果，我们最终会看到这个异常:

> 消息:断言。AreEqual 失败。预期:<2>。实际:<0>。

如果我们想出如何修复我们的方法，使它返回正确的结果，这个测试将不再失败。我们的测试结果会是绿色，而不是红色。

如果我们想单步执行代码，我们可以设置断点，然后右键单击并选择 **Debug Test(s)** 。这将使我们能够单步执行代码。

### 这多好，到此为止

假设你一遍又一遍地尝试修复你的功能。哪个更容易:

*   启动您的 Windows 窗体应用程序，键入一些值，按下按钮，然后查看结果
*   运行单元测试并查看它是通过还是失败

单元测试要容易得多。也许我们已经意识到创建表单是一种可怕的测试方式。也许我们倾向于使用控制台应用程序。尽管如此，运行单元测试难道不比这更容易吗？单元测试很像一个控制台应用程序，除了不是我们*看*结果看它是否正确，测试已经知道它正在寻找的正确结果。这是测试的一部分。

想象一下，如果我们将这段代码粘贴到堆栈溢出问题中。它说，实际上，

> 这是我需要帮助的代码。这里有一个单元测试，准确地描述了它应该做什么。

如果我试图帮助解决这个问题，我可能会从创建一个单元测试项目开始，将这段代码粘贴到其中，并编写一些单元测试。我会使用单元测试来确认我的解决方案是否有效。(我想给你一个好答案，也就是说我不打算猜了。)

但是现在，通过提供您的代码和单元测试，您甚至可以更容易地帮助您。我可以粘贴代码和单元测试，我所要做的就是使用你的代码使单元测试通过。

### 更精彩

我们真的相信我们的代码会像预期的那样工作，因为它只为一组输入返回正确的结果吗？难道我们不想用多个输入来测试它吗？也许我们有一些奇怪的边缘案例。我们如何用多个输入来测试呢？

我们可以复制并粘贴测试方法，这样我们就有了几个测试方法，每个方法都有不同的输入和预期的输出，然后我们就可以运行所有这些测试。那是可以接受的。这意味着我们可以同时用一系列输入测试我们的代码。想象一下用一个控制台应用程序来尝试。这会是一种痛苦。单元测试使它变得容易。

但是有一种比创建多个测试更简单的方法。我们可以创建一个接受多个输入的单一测试。这被称为*参数化测试*。它可能看起来像这样:

```
[TestClass]
public class DateFunctionTests
{
    [DataTestMethod]
    [DataRow("1/1/2019", "2/15/2019", 14, 2)]
    [DataRow("1/1/2019", "1/13/2019", 14, 0)]
    [DataRow("1/1/2019", "12/31/2019", 31, 7)]
    public void NumberOfTimesDayOfMonthOccursInDateRange_returns_correct_result(
        string startDate, string endDate, int dayOfMonth, int expectedResult)
    {
        var actualResult = DateFunctions.NumberOfTimesDayOfMonthOccursInDateRange(
            DateTime.Parse(startDate), DateTime.Parse(endDate), dayOfMonth);
        Assert.AreEqual(expectedResult, actualResult);
    }
} 
```

每个`DataRow`属性包含一组值——开始日期、结束日期、一个月中的某一天以及预期结果。我们可以只添加一个新的数据行，而不必编写更多的测试方法来测试一组不同的值。测试运行程序将对每组值运行一次测试。

想象一下，如果我们修改函数，使其对一组输入有效，但对不同的输入返回错误的结果，会有多沮丧。想象一下，必须对每组输入进行一遍又一遍的测试。现在很容易验证该函数同时为所有输入返回正确的结果。

### 我们的改进问题

现在，我们可以发布这样的代码，而不是发布一堆其他人必须阅读的不相关的代码:

* * *

我正在尝试编写一个函数，返回一个月中某一天在一个日期范围内出现的次数。这是我的代码，还没有运行，还有一些显示结果应该是什么的单元测试。

你能帮我找到代码中的问题吗？

```
public static class DateFunctions
{
    public static int NumberOfTimesDayOfMonthOccursInDateRange(
        DateTime startDate, DateTime endDate, int dayOfMonth)
    {
       // Whatever code you have that isn't working right
    }
}

[TestClass]
public class DateFunctionTests
{
    [DataTestMethod]
    [DataRow("1/1/2019", "2/15/2019", 14, 2)]
    [DataRow("1/1/2019", "1/13/2019", 14, 0)] 
    [DataRow("1/1/2019", "12/31/2019", 31, 8)]
    public void NumberOfTimesDayOfMonthOccursInDateRange_returns_correct_result(
        string startDate, string endDate, int dayOfMonth, int expectedResult
        )
    {
        var actualResult = DateFunctions.NumberOfTimesDayOfMonthOccursInDateRange(
            DateTime.Parse(startDate), DateTime.Parse(endDate), dayOfMonth);
        Assert.AreEqual(expectedResult, actualResult);
    }
} 
```

* * *

你可能见过 Stack Overflow 上的人要求一个代表“最小的、完整的、可验证的例子”的 [MCVE](https://stackoverflow.com/help/minimal-reproducible-example) 换句话说，他们要求你只提供你需要帮助的那部分代码，而不是各种无关的细节，确保你没有遗漏重要的部分，并展示你如何知道它是否工作。

这正是我们所取得的成就:

*   **最小** -我们已经剥离了所有与日期函数*不*相关的代码。
*   **完成** -整个日期计算都在该函数中
*   **可验证的** -单元测试显示了你所期望的以及你如何知道它是否工作。

假设您已经做了一些努力来编写代码——您正在展示您编写函数的尝试——您已经编写了一个完美的问题。

### 最好的部分

你问这个问题所付出的努力将使理解和调试你的代码变得更加容易，以至于你更有可能在提出这个问题之前就解决自己的问题。

这些是大多数开发人员为了使他们自己的问题更容易理解和解决而采取的大致相同的步骤。我们将执行特定功能的代码放在单独的类和方法中，这样我们就可以在与其他代码隔离的情况下测试它。然后我们编写单元测试来验证我们的代码做了我们认为它应该做的事情。

我们都会在代码中犯错误，即使是看起来微不足道的代码。这让我们能够更快地发现我们的错误。我们常常能在犯了错误后马上发现自己的错误。这也意味着，如果我们必须在调试器中逐句通过我们的代码，我们将不必逐句通过那么多代码。与其设置断点，启动应用程序，并提供输入来测试一些微小的代码，我们可以只隔离我们正在工作的部分。

从长远来看，这意味着我们工作得更快，更有信心。如果我们没有进行过单元测试，我们可能会认为编写额外的测试代码会导致我们工作得更慢，但实际上恰恰相反。当我们减去我们本来要花在搜索稍后出现的缺陷、重读几天或几周前编写的代码以及在调试器中单步调试大量代码上的时间时，最终结果是我们节省了大量时间。

不是立竿见影的。有一点点的学习曲线，一开始可能会让我们慢下来。但是，如果你曾经要做出一个信念的飞跃，这是一个要做的。一旦你到了另一边，你就再也不想回去了。你甚至会发现自己不知道如何在没有单元测试的情况下编写代码。有那么好。

你可能读过批评单元测试的文章，认为我们可能最终会写太多无用的测试，这在某种程度上损害了我们代码的设计。这种争论通常有一定的真实性。一开始我们可能会失去平衡，从不写测试转向写太多测试。我们的测试质量可能会很低。没关系。这是学习经历的一部分。如果我们意识到不编写任何单元测试，我们已经处于一个极端，这是有帮助的。在找到平衡之前，我们可能会走向相反的极端。

尽管这种实践对我很有帮助，但是我仍然有很多关于单元测试的东西要学。

单元测试也像是通向各种改进实践的入门药物。在这个例子中，我们必须将代码从一个 Windows 窗体移到它自己的类中，这样我们才能测试它。这是个好习惯。使测试代码成为可能改进了代码的设计。这种模式不断重复。随着时间的推移，我们很可能会发现，安排我们的代码，以便我们可以为它编写测试，在其他方面极大地改善了我们代码的设计。

* * *

这篇文章的前提仅仅是引入单元测试的一个技巧吗？是也不是。我经常阅读堆栈溢出问题，如果它们包含单元测试来描述预期的结果，就会容易理解得多。有时我会通过在答案中包含单元测试来证明这个想法，但是解释如何编写测试已经超出了最初问题的范围。

同时，我确信大多数不写单元测试的开发人员会从学习写单元测试中受益。正如我希望我已经展示的那样，它们改变了我们编写代码的方式，使得隔离和解决我们自己的问题变得更加容易。我总是在栈溢出上查找东西。但是这种改变增强了我们自己解决问题的能力，提高了我们的生产力和信心。