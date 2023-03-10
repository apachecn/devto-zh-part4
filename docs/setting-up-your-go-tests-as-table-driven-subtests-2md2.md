# 将 Go 测试设置为表格驱动的子测试

> 原文：<https://dev.to/jaysonesmith/setting-up-your-go-tests-as-table-driven-subtests-2md2>

Go 对测试有很好的支持，有些人可能会用更有效、更易读的方式来编写测试。我见过一些不同的编写测试的方法，但是到目前为止我最喜欢的是表驱动子测试。在我们深入了解这些内容之前，让我们先来看看一个简单的项目，我们将一步一步地使用表驱动的子测试。注意:你可能会在野外看到以不同风格编写的表格驱动子测试。这里介绍的风格就是我的做法。

## 表驱动前子测试(TDSTs)

让我们使用一个简单的加法函数，它接收两个字符串，对它们进行转换，如果转换成功，就返回它们的和。

```
// add.go
package add

import (
    "strconv"

    "github.com/pkg/errors"
)

func add(x, y string) (int64, error) {
    xInt, err := strconv.ParseInt(x, 10, 64)
    if err != nil {
        return 0, errors.Wrap(err, "converting x failed")
    }

    yInt, err := strconv.ParseInt(y, 10, 64)
    if err != nil {
        return 0, errors.Wrap(err, "converting y failed")
    }

    return xInt + yInt, nil
} 
```

下面是我们可以用来提供 add 函数的一些基本内容的六个案例:

*   零
*   正数
*   有肯定答案的负数
*   带有否定答案的负数
*   x 参数无法解析
*   y 参数无法解析

而下面的例子是以一种清晰易读的方式建立起来的，其步骤分为安排/行动/断言或给出/何时/然后，这取决于你如何思考。即使有一个测试失败，这种设置也能让我们得到所有的测试输出。

...它也是超级重复和不必要的冗长。😅

```
// ./add_test.go
package add

import (
    "errors"
    "testing"

    "github.com/stretchr/testify/require"
)

func TestAdd_Zeros(t *testing.T) {
    expected := int64(0)

    actual, err := add("0", "0")

    require.NoError(t, err)
    require.Equal(t, expected, actual)
}

func TestAdd_PositiveNumbers(t *testing.T) {
    expected := int64(4)

    actual, err := add("2", "2")

    require.NoError(t, err)
    require.Equal(t, expected, actual)
}

func TestAdd_NegativeNumbers_PositiveAnswer(t *testing.T) {
    expected := int64(5)

    actual, err := add("-5", "10")

    require.NoError(t, err)
    require.Equal(t, expected, actual)
}

func TestAdd_NegativeNumbers_NegativeAnswer(t *testing.T) {
    expected := int64(-8)

    actual, err := add("-5", "-3")

    require.NoError(t, err)
    require.Equal(t, expected, actual)
}

func TestAdd_XFailsParse(t *testing.T) {
    expected := int64(0)
    expectedErr := errors.New(`converting x failed: strconv.ParseInt: parsing "a": invalid syntax`)

    actual, err := add("a", "0")

    require.EqualError(t, expectedErr, err.Error())
    require.Equal(t, expected, actual)
}

func TestAdd_YFailsParse(t *testing.T) {
    expected := int64(0)
    expectedErr := errors.New(`converting y failed: strconv.ParseInt: parsing "z": invalid syntax`)

    actual, err := add("0", "z")

    require.EqualError(t, expectedErr, err.Error())
    require.Equal(t, expected, actual)
} 
```

不是有六个不同的测试函数，有些函数的名字越来越长，比如`TestAdd_NegativeNumbers_NegativeAnswer`来表达测试的目的，如果我告诉你我们可以把它重构为一个测试函数，会怎么样？那真的会让 T2 把事情收拾干净，不是吗？表格测试由三个基本部分组成:表格、循环和测试。

## 设置表格

表测试中的表可以通过两种方式设置:一个 slice of struct([]struct)或一个 map of string struct(map[string]struct)。除了风格之外，这两者之间没有太大的区别，当使用[]struct 时，您的测试将总是按照它们在表中存在的顺序运行。这种始终一致的顺序可能是一件坏事，因为如果您不注意测试之间的状态，它可能会导致测试以一种顺序通过，而不是以另一种顺序通过。我最近遇到了一个问题，我们的一组测试使用了 map[string]string 方法，因为地图不会以一致的顺序返回。它们不是随机的，但是它们足够不一致，以至于对于这个例子来说它们也是*。对于本指南，我们将创建的表将使用[]struct 方法。*

我们的表将是我们想要运行的所有测试用例的集合，包含我们需要的关于测试名称、输入和预期数据的所有信息。在更高级的情况下，我们的表也可以包括像测试装置或辅助方法这样的东西——让我们在学习时保持简单。

在最基本的情况下，我们的空表看起来会像这样(我喜欢将我们赋予它的变量命名为 testCases):

```
testCases := []struct{}{} 
```

从这里开始，我们需要填充我们的测试用例。我们将首先通过定义我们的字段和它们的类型来做到这一点，就像我们将放入标准结构中一样。

```
testCases := []struct {
    name     string
    x        string
    y        string
    expected int64
    err      error
}{} 
```

现在让我们添加我们的第一个测试用例，并收紧一点！

```
testCases := []struct {
    name     string
    x        string
    y        string
    expected int64
    err      error
}{
    {
        name:     "Zeros",
        x:        "0",
        y:        "0",
        expected: int64(0),
    },
} 
```

东西还是好看的，干净的，易读的！您也可以编写没有 struct 键的单独案例，但是为了可读性，我倾向于保留它们。您可能会注意到，这里没有 err 条目。这种情况下没有声明的错误，因为我们不希望出现错误，所以没有必要包含错误！

## 循环时间

表驱动测试的一个优点是它们很简洁，但是我们需要一种方法来完成它们。这就是我们的循环出现的地方。这一步*真的*简单:

```
for _, tc := range testCases {} 
```

我们在`testCases`上建立了一个标准循环，并将每个测试用例分配给`tc`。这是这里，让我们收紧东西，看看如何完整的设置现在看起来。

```
func TestAdd(t *testing.T) {
    testCases := []struct {
        name, x, y string
        expected   int64
        err        error
    }{
        {name: "Zeros", x: "0", y: "0", expected: int64(0)},
    }

    for _, tc := range testCases {

    }
} 
```

我将结构中的声明压缩了一些，因为我们的测试值是基本的，所以我将它们都放在一行中。

## 测试

我们已经建立了三分之二的表驱动测试，让我们给它一些东西来运行。(我们稍后会做子测试)这一步看起来基本上和我们的初始测试一样；我们只需要适当地命名一些东西，这样测试就知道什么去哪里了。

```
func TestAdd(t *testing.T) {
    testCases := []struct {
        name, x, y string
        expected   int64
        err        error
    }{
        {name: "Zeros", x: "0", y: "0", expected: int64(0)},
    }

    for _, tc := range testCases {
        actual, err := add(tc.x, tc.y)

        require.NoError(t, err)
        require.Equal(t, tc.expected, actual)
    }
} 
```

现在，你可能会想，与我们开始时的情况相比，这有点复杂，如果这是我们唯一的测试案例，我在某种程度上同意你的观点。一旦你开始使用它们，默认设置它们是有意义的，因为它使得添加新的案例变得非常容易。让我们通过添加所有的测试用例来展示这一点。

```
func TestAdd(t *testing.T) {
    testCases := []struct {
        name, x, y string
        expected   int64
        err        error
    }{
        {name: "Zeros", x: "0", y: "0", expected: int64(0)},
        {name: "Positive numbers", x: "2", y: "2", expected: int64(4)},
        {name: "Negative numbers with positive result", x: "-5", y: "10", expected: int64(5)},
        {name: "Negative numbers with negative result", x: "-5", y: "-3", expected: int64(-8)},
        {name: "X fails to parse", x: "a", y: "0", expected: int64(0),
            err: errors.New(`converting x failed: strconv.ParseInt: parsing "a": invalid syntax`)},
        {name: "Y fails to parse", x: "0", y: "z", expected: int64(0),
            err: errors.New(`converting y failed: strconv.ParseInt: parsing "z": invalid syntax`)},
    }

    for _, tc := range testCases {
        actual, err := add(tc.x, tc.y)

        require.NoError(t, err)
        require.Equal(t, tc.expected, actual)
    }
} 
```

如此简单！“但是等等，那些不通过！”你是正确的读者；我们需要在我们的测试部分中添加更多的东西来让事情进行下去。这可能是这样的。

```
for _, tc := range testCases {
    actual, err := add(tc.x, tc.y)

    if tc.err == nil {
        require.NoError(t, err)
    } else {
        require.EqualError(t, tc.err, err.Error())
    }
    require.Equal(t, tc.expected, actual)
} 
```

## 子测试时间

标准表测试的一个问题是，如果一个测试用例失败了，那么之后的所有测试用例都不会运行，这让我们处于一个非常糟糕的境地。当这种情况发生时，我们对代码的状态有一个不完整的了解，这可能是危险的！这就是子测试的用武之地。

怎么会？每个子测试都是独立运行的，当一个测试失败时，测试执行会继续到下一个测试用例，直到所有的测试都完成——假设你编写的测试是原子的。你*正在用这种方式编写你的测试，对吗？对吗？！*

设置运行子测试非常简单:

```
func TestAdd(t *testing.T) {
    testCases := []struct {
        name, x, y string
        expected   int64
        err        error
    }{
        {name: "Zeros", x: "0", y: "0", expected: int64(0)},
        {name: "Positive numbers", x: "2", y: "2", expected: int64(4)},
        {name: "Negative numbers with positive result", x: "-5", y: "10", expected: int64(5)},
        {name: "Negative numbers with negative result", x: "-5", y: "-3", expected: int64(-8)},
        {name: "X fails to parse", x: "a", y: "0", expected: int64(0),
            err: errors.New(`converting x failed: strconv.ParseInt: parsing "a": invalid syntax`)},
        {name: "Y fails to parse", x: "0", y: "z", expected: int64(0),
            err: errors.New(`converting y failed: strconv.ParseInt: parsing "z": invalid syntax`)},
    }

    for _, tc := range testCases {
        t.Run(tc.name, func(tt *testing.T) {
            actual, err := add(tc.x, tc.y)

            if tc.err == nil {
                require.NoError(tt, err)
            } else {
                require.EqualError(tt, tc.err, err.Error())
            }
            require.Equal(tt, tc.expected, actual)
        })
    }
} 
```

超级简单！只需包装现有的测试设置，更改一些变量名，就可以得到子测试了！这里有几点需要指出:

*   t.Run 接受测试名(我们现在正在使用它！)以及一个包装我们的测试代码的函数。
*   包装函数接受一个测试实例。T struct 并将其声明为`tt`。虽然没有必要，但我会做事情可能会出错的事情，所以我们不妨这样做，并在未来的道路上为自己节省一些可能的痛苦。

让我们运行一下新设置的子测试，看看情况如何:

```
$ go test
PASS
ok      github.com/jaysonesmith/tdst    0.024s 
```

### 一切关于交流

行...重要的是，该输出看起来就像任何其他测试运行通过时的情况一样！是啊！正确！虽然当他们通过测试时看起来是一样的，但是当测试失败时才是真正的精彩！我将修改一半子测试中的内容，这样我们就可以看到失败时的输出是什么样子:

```
$ go test
--- FAIL: TestAdd (0.00s)
    --- FAIL: TestAdd/Positive_numbers (0.00s)
        add_test.go:35:
                Error Trace:    add_test.go:35
                Error:          Not equal:
                                expected: 5
                                actual  : 4
                Test:           TestAdd/Positive_numbers
    --- FAIL: TestAdd/Negative_numbers_with_negative_result (0.00s)
        add_test.go:35:
                Error Trace:    add_test.go:35
                Error:          Not equal:
                                expected: -3
                                actual  : -8
                Test:           TestAdd/Negative_numbers_with_negative_result
    --- FAIL: TestAdd/Y_fails_to_parse (0.00s)
        add_test.go:33:
                Error Trace:    add_test.go:33
                Error:          Error message not equal:
                                expected: "converting y failed: strconv.ParseInt: parsing \"z\": invalid syntax"
                                actual  : "converting y failed: strconv.ParseInt: foo"
                Test:           TestAdd/Y_fails_to_parse
FAIL
exit status 1
FAIL    github.com/jaysonesmith/tdst    0.024s 
```

呜！我们得到了一个非常容易阅读的所有失败的测试用例的分类，包括父测试、测试用例、失败的断言行，以及一些有用的预期/实际输出，以帮助我们诊断问题。如果没有子测试，输出会是这样的:

```
$ go test
--- FAIL: TestAdd (0.00s)
    add_test.go:35:
                Error Trace:    add_test.go:35
                Error:          Not equal:
                                expected: 5
                                actual  : 4
                Test:           TestAdd
FAIL
exit status 1
FAIL    github.com/jaysonesmith/tdst    0.025s 
```

...是啊。一点也没用，是吧？

如果我们使用 Go 的非致命工具或 evidence 的`assert`而不是`require`，我们可以获得与子测试设置类似的行为，因为它们不会完全停止当前运行。我通常使用`assert`，但是，作为一个警告，如果测试的一部分失败，并且随后的断言依赖于该数据，这也会导致一些空指针异常。

下面是我如何设置的，以及我们最终的测试将会是什么样子:

```
func TestAdd(t *testing.T) {
    testCases := []struct {
        name, x, y string
        expected   int64
        err        error
    }{
        {name: "Zeros", x: "0", y: "0", expected: int64(0)},
        {name: "Positive numbers", x: "2", y: "2", expected: int64(5)},
        {name: "Negative numbers with positive result", x: "-5", y: "10", expected: int64(5)},
        {name: "Negative numbers with negative result", x: "-5", y: "-3", expected: int64(-3)},
        {name: "X fails to parse", x: "a", y: "0", expected: int64(0),
            err: errors.New(`converting x failed: strconv.ParseInt: parsing "a": invalid syntax`)},
        {name: "Y fails to parse", x: "0", y: "z", expected: int64(0),
            err: errors.New(`converting y failed: strconv.ParseInt: foo`)},
    }

    for _, tc := range testCases {
        t.Run(tc.name, func(tt *testing.T) {
            actual, err := add(tc.x, tc.y)

            if tc.err == nil {
                assert.NoError(tt, err)
            } else {
                assert.EqualError(tt, tc.err, err.Error())
            }
            assert.Equal(tt, tc.expected, actual)
        })
    }
} 
```

你觉得怎么样？我个人真的很喜欢这些，并且在测试的时候经常会写 TDST 的。我希望你学到了一些东西，并喜欢这本书。

非常感谢您的宝贵时间！

射流研究…

注:本指南仅涵盖设置表格驱动子测试，但不包括所有关于它们的精彩内容！也许我也会写这些！