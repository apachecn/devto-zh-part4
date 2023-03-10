# 合同测试的第一种方法

> 原文：<https://dev.to/chicio/a-first-approach-to-contract-test-5g01>

在这篇文章中，我将谈论合同测试:它们是什么以及你如何使用它们。

有时，您必须对同一接口的多个实现进行单元测试。基本上，您可以对同一接口的多个具体实现进行相同的测试。在这种情况下，**合同测试**可以帮你节省很多时间。通过使用契约测试，您将能够为不同的具体实现运行相同的测试集。

是怎么工作的？要点是有一个模板基础抽象“ContractTest”测试类，它使用抽象方法封装测试的逻辑，这些方法使用被测试对象的基础接口。这些抽象方法将在这个“ContractTest”类的子类中实现，它们将为测试提供抽象方法声明中使用的接口的具体实现。

让我们来看一个例子，让一切更清楚！！！这个例子是一个使用 JUnit 4 和 Mockito 2.8 的独立 Java 项目。显然，你可以将这个概念应用到其他语言/平台上(事实上，当我在一个 Android 应用中开发一个新特性时，我学习并实现了契约测试)😍).

例如假设我们有如下接口:

```
public interface Command {
    void execute();
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有两个实现该接口的对象:AccountCommand 和 SettingsCommand。

```
class AccountCommand implements Command {
    private MenuActionsListener menuActionsListener;

    AccountCommand(MenuActionsListener menuActionsListener) {
        this.menuActionsListener = menuActionsListener;
    }

    public void execute() {
        menuActionsListener.onAccountSelected();
    }
}

public class SettingsCommand implements Command {
    private MenuActionsListener menuActionsListener;

    SettingsCommand(MenuActionsListener menuActionsListener) {
        this.menuActionsListener = menuActionsListener;
    }

    public void execute() {
        menuActionsListener.onSettingsSelected();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这两个实现看起来非常相似。所以是时候看看契约测试如何帮助我们了🤘！！！！

我们可以编写一个`CommandContract`基本测试类，包含我们想要编写的测试的逻辑。在我们的具体例子中，我们希望通过调用`execute()`方法来确保当命令被执行时，在`Command`的每个具体实现上用正确的方法调用`menuActionsListener`。所以我们的`CommandContract`实现是:

```
abstract class CommandContract {
    private Command command;
    private MenuActionsListener menuActionsListener;

    @Test
    public void commandIsExecuted() throws Exception {
        givenAMenuActionListener();
        command = givenACommand(menuActionsListener);
        whenACommandIsExecuted();
        thenTheCorrectMenuActionIsInvoked(menuActionsListener);
    }

    private void givenAMenuActionListener() {
        menuActionsListener = mock(MenuActionsListener.class);
    }

    protected abstract Command givenACommand(MenuActionsListener menuActionsListener);

    private void whenACommandIsExecuted() {
        command.execute();
    }

    protected abstract void thenTheCorrectMenuActionIsInvoked(MenuActionsListener menuActionsListener);
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你在`commandIsExecuted()`测试中所看到的，我们使用所有的抽象方法来定义测试的通用命令实现。现在，在 test 子类中，我们将实现抽象方法，为测试提供具体命令的各种实现。

所以我们创建一个`AccountCommandTest`类，作为`CommandContract`的子类，来测试我们的`AccountCommand`类:

```
public class AccountCommandTest extends CommandContract {

    protected Command givenACommand(MenuActionsListener menuActionsListener) {
        return new AccountCommand(menuActionsListener);
    }

    protected void thenTheCorrectMenuActionIsInvoked(MenuActionsListener menuActionsListener) {
        Mockito.verify(menuActionsListener).onAccountSelected();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还创建了一个`SettingsCommandTest`类，它是`CommandContract`的子类，用来测试我们的`AccountCommand`类:

```
public class SettingsCommandTest extends CommandContract {

    protected Command givenACommand(MenuActionsListener menuActionsListener) {
        return new SettingsCommand(menuActionsListener);
    }

    protected void thenTheCorrectMenuActionIsInvoked(MenuActionsListener menuActionsListener) {
        Mockito.verify(menuActionsListener).onSettingsSelected();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们测试了所有具体的`Command`实现，没有复制单元测试逻辑。
精彩😮😍！！！

[在这里](https://github.com/chicio/Contract-Tests)你可以找到完整的例子(一个使用 IntelliJ、JUnit4、Mockito 开发的 Maven 项目)。

是时候对你的项目进行合同测试了😂😆！！！