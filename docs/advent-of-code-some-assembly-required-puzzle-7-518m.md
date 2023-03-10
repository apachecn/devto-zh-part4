# 代码的出现——需要一些汇编——难题 7

> 原文：<https://dev.to/10xlearner/advent-of-code-some-assembly-required-puzzle-7-518m>

# 代码的出现——需要一些组装——难题 7

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第七部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 7 日开始解决这个问题，名为“需要一些装配”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/7)上找到，我在这里只描述问题的本质:

圣诞老人给了小鲍比一套电线和位逻辑门，他需要我们的帮助来组装电路，因为他太小了，无法独立完成。

每根电线由一些小写字母标识，可以传输 16 位信号，只能从一个源获得信号，但可以向多个目的地提供信号。一根导线的信号由一个门、另一根导线或某个特定值提供给每根导线。最后，直到所有的输入都有信号时，门才提供信号。

例如:

```
123 -> x // Attribution of the signal to the wire
456 -> y // Attribution of the signal to the wire
x AND y -> d // Bitwise AND operator
x OR y -> e // Bitwise OR operator
x LSHIFT 2 -> f // Left Shifting
y RSHIFT 2 -> g // Right Shifting
NOT x -> h // Bitwise complement
NOT y -> i // Bitwise complement 
```

Enter fullscreen mode Exit fullscreen mode

结果在

```
x: 123
y: 456
d: 72
e: 507
f: 492
g: 114
h: 65412
i: 65079 
```

Enter fullscreen mode Exit fullscreen mode

在这一部分，我们将不得不找到电线`a`中的信号值

### 解

首先，这个问题和上一个完全不在一个层面上。那么就从它开始吧。

#### 位操作

对于这个问题，我们需要一些比特上的操作符。希望 C++已经定义了我们需要的那个

他们在这里:

*   `&`是`AND`运算符
*   `|`是`OR`运算符
*   `<<`是`LSHIFT`运算符
*   `>>`是`RSHIFT`运算符
*   `~`是`NOT`运算符

我鼓励你检查一下关于这些操作员的 [std 文档](https://en.cppreference.com/w/cpp/language/operator_arithmetic)。

#### 类型

让我们从我们需要的类型/对象开始。我们有一个`Signal`，它将是一个`uint16_t`，因为信号是 16 位的。

```
using Signal = uint16_t; 
```

Enter fullscreen mode Exit fullscreen mode

我们有`Wire`，它是一个有名字的物体，可以有一个信号。这是标题可能的样子。

```
class Wire
{
public:
    explicit Wire (const std::string& name_);
    ~Wire ();

    bool hasSignal () const;

    void setSignal(const Signal value_);
    Signal getSignal() const;

    std::string getName() const;
private:
    std::string name;
    bool hasSignalValueBeenSet{false};
    Signal value{0};
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了信号和导线，我们需要的最后一个对象是允许我们计算每根导线的信号值的操作。为此，我们有 5 种操作:

```
enum class OperationType
{
    AND, // x AND y -> z
    OR, // x OR y -> z
    LSHIFT, // x LSHIFT 2 -> y
    RSHIFT, // x RSHIFT 2 -> y
    NOT, // NOT x -> y
    ATTRIBUTION, // 123 -> x
}; 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们必须为每种操作设置一个公共接口的行为:

```
class Operation
{
public:
    virtual Wire& getResultWire () = 0;
    virtual bool canBeExecuted () const = 0;
    virtual bool isExecuted () const = 0;
    virtual void execute () = 0;
}; 
```

Enter fullscreen mode Exit fullscreen mode

**编辑**:注意“输出线”可以直接集成在`Operation`类中，因为所有操作只能有一个输出。

每个特定的操作都将从该类继承，并重写该函数以指定其行为。例如:

```
class LShift : public Operation
{
public:
    LShift (Wire& input_, size_t number_, Wire& output_) : input(input_), number(number_), output(output_) {}
    ~LShift () = default;

    Wire& getResultWire () override
    {
        return output;
    }

    bool canBeExecuted () const override
    {
        return input.hasSignal();
    }

    bool isExecuted () const override
    {
        return hasBeenExecuted;
    }

    void execute () override
    {
        output.setSignal (input.getSignal() << number);
        hasBeenExecuted = true;
    }

private:
    Wire& input, &output;
    size_t number{0};
    bool hasBeenExecuted{false};
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看所有操作的实现，我邀请你去我的 [Github](https://github.com/Xav83/AdventOfCode/tree/2015.07/2015/Day7) 。

#### 程序

现在我们有了我们的主类，我们可以用一些代码从难题输入中提取数据，比如:

```
class Wires; // vector of Wire with some specific behavior
class Operations; // vector of Operation with a method "run" to run all the operations

foreachLineIn(fileContent, [&wires](const std::string& line)
{
    const auto operationType = getOperationFromInstruction (line);
    auto& wiresFromInstruction = getWiresNameInInstruction (line);
    for(auto& w : wiresFromInstruction.getWires())
    {
        wires.addNewWire(w); // Only adds wire if it doesn't already exist
    }
});

foreachLineIn(fileContent, [&wires, &operations](const std::string& line)
{
    const auto operationType = getOperationFromInstruction (line);
    auto& wiresFromInstruction = getWiresNameInInstruction (line);
    operations.addNewOperation(operationType, wires, wiresFromInstruction, line);
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了所有的数据，我们“只”需要运行它，…我的意思是`operations.run()`。

```
void Operations::run()
{
    while (std::any_of(std::begin(operations), std::end(operations), 
    {
        return !operation->isExecuted();
    }))
    {
        for(auto& operation : operations)
        {
            if(!operation->isExecuted() && operation->canBeExecuted())
            {
                operation->execute();
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧，每根电线都有信号。现在要做的就是用`std::find_if`得到名为`a`的线来求解这部分。

## 第二部分

### 问题

对于这一部分，我们必须获取 a 线的信号，将 b 线覆盖为该信号，并重置其他线(包括 a 线),再次找到 a 线的信号值。

### 解

嗯……现在我们有一个软件可以找到导线`a`的值，我们所要做的就是修改给导线 b 赋值的那条线，然后它再次运行程序，得到结果，对吗？

完全正确，它的工作/爱软件❤的可重用性

## 结论

你可以注意到，在这篇文章中写的解决方案，并没有包括所有使程序运行的资源，而只是解决这个问题的资源中有趣的部分。如果你想从头到尾看这些节目，你可以登录我的 [GitHub 账户](https://github.com/Xav83/AdventOfCode/tree/2015.07/2015/Day7)，探索完整的解决方案，添加评论或提问。

以下是我们使用的 std 方法列表，我非常鼓励您查看它们的定义:

*   [使用](https://en.cppreference.com/w/cpp/language/type_alias)
*   [std::any_of](https://en.cppreference.com/w/cpp/algorithm/all_any_none_of)

谢谢你的阅读，希望你喜欢😃。直到下一部分，享受学习和成长的乐趣。