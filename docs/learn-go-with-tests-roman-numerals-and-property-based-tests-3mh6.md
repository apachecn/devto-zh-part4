# 通过测试学习 Go-罗马数字和基于属性的测试

> 原文：<https://dev.to/quii/learn-go-with-tests-roman-numerals-and-property-based-tests-3mh6>

这是从一个名为 [Learn Go with tests](https://github.com/quii/learn-go-with-tests) 的项目中截取的帖子，该项目的目的是熟悉 Go 并学习 TDD 的相关技术

**[你可以在这里找到本章的所有代码](https://github.com/quii/learn-go-with-tests/tree/master/roman-numerals)**

一些公司会要求你做罗马数字形作为面试过程的一部分。这一章将向你展示如何用 TDD 解决这个问题。

我们将编写一个函数，将一个阿拉伯数字转换成一个罗马数字。

如果你没听说过罗马数字，那就是罗马人写数字的方式。

你通过把符号粘在一起来建造它们，这些符号代表数字

所以`I`是“一”。`III`是三。

看起来很简单，但是有一些有趣的规则。`V`表示五，但`IV`是四(不是`IIII`)。

`MCMLXXXIV`是 1984 年。这看起来很复杂，很难想象我们如何从一开始就编写代码来解决这个问题。

正如这本书所强调的，软件开发人员的一个关键技能是尝试识别*有用的*功能的“垂直薄片”，然后**迭代**。TDD 工作流有助于促进迭代开发。

所以与其说是 1984 年，不如说是从 1 开始。

## 先写测试

```
func TestRomanNumerals(t *testing.T) {
    got := ConvertToRoman(1)
    want := "I"

    if got != want {
        t.Errorf("got '%s', want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经读到这里了，希望你会觉得很无聊和例行公事。这是件好事。

## 试运行测试

`./numeral_test.go:6:9: undefined: ConvertToRoman`

让编译器来指引方向

## 编写运行测试所需的最少代码，并检查失败的测试输出

创建我们的函数，但是不要让测试通过，总是要确保测试没有通过

```
func ConvertToRoman(arabic int) string {
    return ""
} 
```

Enter fullscreen mode Exit fullscreen mode

它现在应该运行了

```
=== RUN   TestRomanNumerals
-------- FAIL: TestRomanNumerals (0.00s)
    numeral_test.go:10: got '', want 'I'
FAIL 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func ConvertToRoman(arabic int) string {
    return "I"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

还没什么可重构的。

*我知道*光是硬编码结果就感觉怪怪的，但是有了 TDD，我们希望尽可能久地远离“红色”。可能*觉得*我们没有完成太多，但是我们已经定义了我们的 API，并且得到了一个捕获我们的规则之一的测试；即使“真正的”代码非常愚蠢。

现在用这种不安的感觉来写一个新的测试，来迫使我们写一些不那么愚蠢的代码。

## 先写测试

我们可以使用子测试来很好地对我们的测试进行分组

```
func TestRomanNumerals(t *testing.T) {
    t.Run("1 gets converted to I", func(t *testing.T) {
        got := ConvertToRoman(1)
        want := "I"

        if got != want {
            t.Errorf("got '%s', want '%s'", got, want)
        }   
    })

    t.Run("2 gets converted to II", func(t *testing.T) {
        got := ConvertToRoman(2)
        want := "II"

        if got != want {
            t.Errorf("got '%s', want '%s'", got, want)
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestRomanNumerals/2_gets_converted_to_II
    --- FAIL: TestRomanNumerals/2_gets_converted_to_II (0.00s)
        numeral_test.go:20: got 'I', want 'II' 
```

Enter fullscreen mode Exit fullscreen mode

没什么好惊讶的

## 写足够的代码让它通过

```
func ConvertToRoman(arabic int) string {
    if arabic == 2 {
        return "II"
    }
    return "I"
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，感觉我们还是没有真正解决问题。所以我们需要编写更多的测试来推动我们前进。

## 重构

我们在测试中有一些重复。当你在测试一些感觉像是“给定输入 X，我们期望 Y”的事情时，你可能应该使用基于表格的测试。

```
func TestRomanNumerals(t *testing.T) {
    cases := []struct {
        Description string
        Arabic      int
        Want        string
    }{
        {"1 gets converted to I", 1, "I"},
        {"2 gets converted to II", 2, "II"},
    }

    for _, test := range cases {
        t.Run(test.Description, func(t *testing.T) {
            got := ConvertToRoman(test.Arabic)
            if got != test.Want {
                t.Errorf("got '%s', want '%s'", got, test.Want)
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以轻松地添加更多的案例，而不必编写更多的测试样板文件。

让我们继续努力，争取 3 分

## 先写测试

将以下内容添加到我们的案例中

```
{"3 gets converted to III", 3, "III"}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestRomanNumerals/3_gets_converted_to_III
    --- FAIL: TestRomanNumerals/3_gets_converted_to_III (0.00s)
        numeral_test.go:20: got 'I', want 'III' 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func ConvertToRoman(arabic int) string {
    if arabic == 3 {
        return "III"
    }
    if arabic == 2 {
        return "II"
    }
    return "I"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

好了，我开始不喜欢这些 if 语句了，如果你仔细看代码，你会发现我们正在根据`arabic`的大小构建一串`I`。

我们“知道”对于更复杂的数字，我们会做一些算术和字符串连接。

让我们带着这些想法尝试一次重构，它*可能不*适合最终的解决方案，但是没关系。我们总是可以扔掉我们的代码，用我们必须指导我们的测试重新开始。

```
func ConvertToRoman(arabic int) string {

    var result strings.Builder

    for i:=0; i<arabic; i++ {
        result.WriteString("I")
    }

    return result.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能没用过 [`strings.Builder`](https://golang.org/pkg/strings/#Builder)

> 构建器用于使用 Write 方法高效地构建字符串。它最大限度地减少了内存复制。

通常我不会为这样的优化费心，直到我遇到实际的性能问题，但是代码量并不比“手动”添加字符串大多少，所以我们也可以使用更快的方法。

对我来说代码看起来更好，描述了我们现在所知道的领域。

### 罗马人也被干成了...

事情现在变得越来越复杂。罗马人聪明地认为重复的字符会变得难以阅读和计数。所以罗马数字的一个规则是，你不能让同一个字符连续出现三次以上。

取而代之的是，你取下一个最高的符号，然后通过在它的左边放一个符号来“减去”。不是所有的符号都可以用作减法器；只有(1)，X (10)，C (100)和 M(1000)。

例如罗马数字中的`5`就是`V`。为了创造 4，你不做`IIII`，而是做`IV`。

## 先写测试

```
{"4 gets converted to IV (cant repeat more than 3 times)", 4, "IV"}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestRomanNumerals/4_gets_converted_to_IV_(cant_repeat_more_than_3_times)
    --- FAIL: TestRomanNumerals/4_gets_converted_to_IV_(cant_repeat_more_than_3_times) (0.00s)
        numeral_test.go:24: got 'IIII', want 'IV' 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func ConvertToRoman(arabic int) string {

    if arabic == 4 {
        return "IV"
    }

    var result strings.Builder

    for i:=0; i<arabic; i++ {
        result.WriteString("I")
    }

    return result.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

我不“喜欢”我们打破了我们的字符串构建模式，我想继续下去。

```
func ConvertToRoman(arabic int) string {

    var result strings.Builder

    for i := arabic; i > 0; i-- {
        if i == 4 {
            result.WriteString("IV")
            break
        }
        result.WriteString("I")
    }

    return result.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让 4“符合”我现在的想法，我现在从阿拉伯数字开始倒数，随着我们的进展，向我们的字符串添加符号。不确定这是否会长期有效，但是让我们看看！

让我们让 5 工作

## 先写测试

```
{"5 gets converted to V", 5, "V"}, 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestRomanNumerals/5_gets_converted_to_V
    --- FAIL: TestRomanNumerals/5_gets_converted_to_V (0.00s)
        numeral_test.go:25: got 'IIV', want 'V' 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

复制我们为 4
所做的方法

```
func ConvertToRoman(arabic int) string {

    var result strings.Builder

    for i := arabic; i > 0; i-- {
        if i == 5 {
            result.WriteString("V")
            break
        }
        if i == 4 {
            result.WriteString("IV")
            break
        }
        result.WriteString("I")
    }

    return result.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

像这样的循环中的重复通常是等待被调用的抽象的标志。短路循环可能是提高可靠性的有效工具，但它也可能告诉你其他事情。

我们循环我们的阿拉伯数字，如果我们碰到某些符号，我们就调用`break`，但是我们*真正做的*是笨拙地减去`i`。

```
func ConvertToRoman(arabic int) string {

    var result strings.Builder

    for arabic > 0 {
        switch {
        case arabic > 4:
            result.WriteString("V")
            arabic -= 5
        case arabic > 3:
            result.WriteString("IV")
            arabic -= 4
        default:
            result.WriteString("I")
            arabic--
        }
    }

    return result.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

*   鉴于我从我们的代码中读取的信号，从我们对一些非常基本的场景的测试中，我可以看到，要构建一个罗马数字，我需要在应用符号时从`arabic`中减去
*   `for`循环不再依赖于`i`，取而代之的是，我们将继续构建我们的字符串，直到我们从`arabic`中减去足够多的符号。

我很肯定这种方法对第 6(六)、7(七)和 8(八)条也是有效的。尽管如此，还是要将案例添加到我们的测试套件中并进行检查(为了简洁起见，我不会包含代码，如果您不确定，可以查看 github 中的示例)。

9 遵循与 4 相同的规则，即我们应该从后面的数的表示中减去`I`。10 用带`X`的罗马数字表示；所以因此 9 应该是`IX`。

## 先写测试

```
{"9 gets converted to IX", 9, "IX"} 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestRomanNumerals/9_gets_converted_to_IX
    --- FAIL: TestRomanNumerals/9_gets_converted_to_IX (0.00s)
        numeral_test.go:29: got 'VIV', want 'IX' 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

我们应该能够采用与之前相同的方法

```
case arabic > 8:
    result.WriteString("IX")
    arabic -= 9 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

感觉*好像代码仍然告诉我们在某处有重构，但对我来说并不完全明显，所以让我们继续。*

我也将跳过这方面的代码，但是在您的测试用例中添加一个对`10`的测试，它应该是`X`，并在继续阅读之前让它通过。

这里是我添加的几个测试，因为我相信我们的代码在 39 以下应该可以正常工作

```
{"10 gets converted to X", 10, "X"},
{"14 gets converted to XIV", 14, "XIV"},
{"18 gets converted to XVIII", 18, "XVIII"},
{"20 gets converted to XX", 20, "XX"},
{"39 gets converted to XXXIX", 39, "XXXIX"}, 
```

Enter fullscreen mode Exit fullscreen mode

如果你曾经做过面向对象编程，你会知道你应该带着一点怀疑来看待`switch`语句。通常，您在一些命令性代码中捕获概念或数据，而实际上它可以在类结构中捕获。

严格来说，Go 并不是面向对象，但这并不意味着我们完全忽略了面向对象提供的经验教训(就像有些人想告诉你的那样)。

我们的 switch 语句描述了一些关于罗马数字的事实和行为。

我们可以通过将数据与行为分离来重构这一点。

```
type RomanNumeral struct {
    Value  int
    Symbol string
}

var RomanNumerals = []RomanNumeral {
    {10, "X"},
    {9, "IX"},
    {5, "V"},
    {4, "IV"},
    {1, "I"},
}

func ConvertToRoman(arabic int) string {

    var result strings.Builder

    for _, numeral := range RomanNumerals {
        for arabic >= numeral.Value {
            result.WriteString(numeral.Symbol)
            arabic -= numeral.Value
        }
    }

    return result.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

这感觉好多了。我们已经将数字周围的一些规则声明为数据，而不是隐藏在算法中，我们可以看到我们如何处理阿拉伯数字，尝试将符号添加到我们的结果中，如果它们合适的话。

这种抽象适用于更大的数字吗？扩展测试套件，使其适用于罗马数字 50，即`L`。

这里有一些测试案例，试着让它们通过。

```
{"40 gets converted to XL", 40, "XL"},
{"47 gets converted to XLVII", 47, "XLVII"},
{"49 gets converted to XLIX", 49, "XLIX"},
{"50 gets converted to L", 50, "L"}, 
```

Enter fullscreen mode Exit fullscreen mode

如果你是一个骗子，你需要添加到`RomanNumerals`数组的只是

```
{50, "L"},
{40, "XL"}, 
```

Enter fullscreen mode Exit fullscreen mode

## 还有剩下的！

以下是剩余的符号

| 阿拉伯语 | 古罗马的 |
| --- | --- |
| One hundred | C |
| Five hundred | D |
| One thousand | M |

对剩余的符号采取同样的方法，只需要向测试和符号数组中添加数据。

你的代码适用于`1984` : `MCMLXXXIV`吗？

这是我最后的测试套件

```
func TestRomanNumerals(t *testing.T) {
    cases := []struct {
        Arabic int
        Roman  string
    }{
        {Arabic: 1, Roman: "I"},
        {Arabic: 2, Roman: "II"},
        {Arabic: 3, Roman: "III"},
        {Arabic: 4, Roman: "IV"},
        {Arabic: 5, Roman: "V"},
        {Arabic: 6, Roman: "VI"},
        {Arabic: 7, Roman: "VII"},
        {Arabic: 8, Roman: "VIII"},
        {Arabic: 9, Roman: "IX"},
        {Arabic: 10, Roman: "X"},
        {Arabic: 14, Roman: "XIV"},
        {Arabic: 18, Roman: "XVIII"},
        {Arabic: 20, Roman: "XX"},
        {Arabic: 39, Roman: "XXXIX"},
        {Arabic: 40, Roman: "XL"},
        {Arabic: 47, Roman: "XLVII"},
        {Arabic: 49, Roman: "XLIX"},
        {Arabic: 50, Roman: "L"},
        {Arabic: 100, Roman: "C"},
        {Arabic: 90, Roman: "XC"},
        {Arabic: 400, Roman: "CD"},
        {Arabic: 500, Roman: "D"},
        {Arabic: 900, Roman: "CM"},
        {Arabic: 1000, Roman: "M"},
        {Arabic: 1984, Roman: "MCMLXXXIV"},
        {Arabic: 3999, Roman: "MMMCMXCIX"},
        {Arabic: 2014, Roman: "MMXIV"},
        {Arabic: 1006, Roman: "MVI"},
        {Arabic: 798, Roman: "DCCXCVIII"},
    }
    for _, test := range cases {
        t.Run(fmt.Sprintf("%d gets converted to '%s", test.Arabic, test.Roman), func(t *testing.T) {
            got := ConvertToRoman(test.Arabic)
            if got != test.Roman {
                t.Errorf("got '%s', want '%s'", got, test.Roman)
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我删除了`description`，因为我觉得*数据*描述了足够多的信息。
*   我添加了一些我发现的其他边缘案例，只是为了给我更多一点信心。对于基于表格的测试，这是非常便宜的。

我没有改变算法，我要做的只是更新`RomanNumerals`数组。

```
var RomanNumerals = []RomanNumeral{
    {1000, "M"},
    {900, "CM"},
    {500, "D"},
    {400, "CD"},
    {100, "C"},
    {90, "XC"},
    {50, "L"},
    {40, "XL"},
    {10, "X"},
    {9, "IX"},
    {5, "V"},
    {4, "IV"},
    {1, "I"},
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解析罗马数字

我们还没完呢。接下来我们要写一个函数，将罗马数字转换成`int`

## 先写测试

我们可以通过一点点重构来重用我们的测试用例

将`cases`变量移出测试，作为`var`块中的包变量。

```
func TestConvertingToArabic(t *testing.T) {
    for _, test := range cases[:1] {
        t.Run(fmt.Sprintf("'%s' gets converted to %d", test.Roman, test.Arabic), func(t *testing.T) {
            got := ConvertToArabic(test.Roman)
            if got != test.Arabic {
                t.Errorf("got %d, want %d", got, test.Arabic)
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我现在使用切片功能只是运行其中一个测试(`cases[:1]`)，因为试图让所有这些测试一次通过是一个很大的飞跃

## 试运行测试

```
./numeral_test.go:60:11: undefined: ConvertToArabic 
```

Enter fullscreen mode Exit fullscreen mode

## 编写运行测试所需的最少代码，并检查失败的测试输出

添加我们的新函数定义

```
func ConvertToArabic(roman string) int {
    return 0
} 
```

Enter fullscreen mode Exit fullscreen mode

测试现在应该运行并失败

```
-------- FAIL: TestConvertingToArabic (0.00s)
    --- FAIL: TestConvertingToArabic/'I'_gets_converted_to_1 (0.00s)
        numeral_test.go:62: got 0, want 1 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

你知道该怎么做

```
func ConvertToArabic(roman string) int {
    return 1
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在我们的测试中改变切片索引，以移动到下一个测试用例(例如`cases[:2]`)。用你能想到的最愚蠢的代码让它通过你自己，继续写愚蠢的代码(有史以来最好的书对吗？)对于第三种情况也是如此。这是我的愚蠢代码。

```
func ConvertToArabic(roman string) int {
    if roman == "III" {
        return 3
    }
    if roman == "II" {
        return 2
    }
    return 1
} 
```

Enter fullscreen mode Exit fullscreen mode

通过*真实代码的沉默工作*我们可以开始看到像以前一样的模式。我们需要迭代输入并构建*某物*，在这个例子中是一个总数。

```
func ConvertToArabic(roman string) int {
    total := 0
    for range roman {
        total++
    }
    return total
} 
```

Enter fullscreen mode Exit fullscreen mode

## 先写测试

接下来我们移动到`cases[:4]` ( `IV`)，它现在失败了，因为它返回了 2，因为这是字符串的长度。

## 写足够的代码让它通过

```
// earlier..
type RomanNumerals []RomanNumeral

func (r RomanNumerals) ValueOf(symbol string) int {
    for _, s := range r {
        if s.Symbol == symbol {
            return s.Value
        }
    }

    return 0
}

// later..
func ConvertToArabic(roman string) int {
    total := 0

    for i := 0; i < len(roman); i++ {
        symbol := roman[i]

        // look ahead to next symbol if we can and, the current symbol is base 10 (only valid subtractors)
        if i+1 < len(roman) && symbol == 'I' {
            nextSymbol := roman[i+1]

            // build the two character string
            potentialNumber := string([]byte{symbol, nextSymbol})

            // get the value of the two character string
            value := romanNumerals.ValueOf(potentialNumber)

            if value != 0 {
                total += value
                i++ // move past this character too for the next loop
            } else {
                total++
            }
        } else {
            total++
        }
    }
    return total
} 
```

Enter fullscreen mode Exit fullscreen mode

这很可怕，但确实有效。这太糟糕了，我觉得有必要添加评论。

*   我希望能够查找给定罗马数字的整数值，所以我从我们的数组`RomanNumeral`中创建了一个类型，然后向它添加了一个方法`ValueOf`
*   接下来在我们的循环中，我们需要向前看*如果*字符串足够大*并且当前符号是有效的减法器*。目前它只是`I` (1)，但也可以是`X` (10)或`C` (100)。
    *   如果它满足这两个条件，我们需要查找该值并将其加到总数*中，如果*是一个特殊的减法器，否则忽略它
    *   然后我们需要进一步增加`i`，这样我们就不会对这个符号计数两次

## 重构

我不完全相信这将是长期的方法，而且我们可能会做一些有趣的重构，但我会抵制这种方法，以防我们的方法完全错误。我宁愿先多做几次测试再看。与此同时，我发表了第一个不那么可怕的声明。

```
func ConvertToArabic(roman string) int {
    total := 0

    for i := 0; i < len(roman); i++ {
        symbol := roman[i]

        if couldBeSubtractive(i, symbol, roman) {
            nextSymbol := roman[i+1]

            // build the two character string
            potentialNumber := string([]byte{symbol, nextSymbol})

            // get the value of the two character string
            value := romanNumerals.ValueOf(potentialNumber)

            if value != 0 {
                total += value
                i++ // move past this character too for the next loop
            } else {
                total++
            }
        } else {
            total++
        }
    }
    return total
}

func couldBeSubtractive(index int, currentSymbol uint8, roman string) bool {
    return index+1 < len(roman) && currentSymbol == 'I'
} 
```

Enter fullscreen mode Exit fullscreen mode

## 先写测试

让我们继续看`cases[:5]`

```
=== RUN   TestConvertingToArabic/'V'_gets_converted_to_5
    --- FAIL: TestConvertingToArabic/'V'_gets_converted_to_5 (0.00s)
        numeral_test.go:62: got 1, want 5 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

除了当它是减法时，我们的代码假设每个字符都是一个`I`,这就是值为 1 的原因。我们应该能够重用我们的`ValueOf`方法来修复这个问题。

```
func ConvertToArabic(roman string) int {
    total := 0

    for i := 0; i < len(roman); i++ {
        symbol := roman[i]

        // look ahead to next symbol if we can and, the current symbol is base 10 (only valid subtractors)
        if couldBeSubtractive(i, symbol, roman) {
            nextSymbol := roman[i+1]

            // build the two character string
            potentialNumber := string([]byte{symbol, nextSymbol})

            if value := romanNumerals.ValueOf(potentialNumber); value != 0 {
                total += value
                i++ // move past this character too for the next loop
            } else {
                total++ // this is fishy...
            }
        } else {
            total+=romanNumerals.ValueOf(string([]byte{symbol}))
        }
    }
    return total
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

当你在 Go 中索引字符串时，你会得到一个`byte`。这就是为什么当我们再次构建字符串时，我们必须做类似于`string([]byte{symbol})`的事情。它重复了几次，让我们只是移动那个功能，这样`ValueOf`就取而代之了。

```
func (r RomanNumerals) ValueOf(symbols ...byte) int {
    symbol := string(symbols)
    for _, s := range r {
        if s.Symbol == symbol {
            return s.Value
        }
    }

    return 0
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以直接把字节传递给我们的函数

```
func ConvertToArabic(roman string) int {
    total := 0

    for i := 0; i < len(roman); i++ {
        symbol := roman[i]

        if couldBeSubtractive(i, symbol, roman) {
            if value := romanNumerals.ValueOf(symbol, roman[i+1]); value != 0 {
                total += value
                i++ // move past this character too for the next loop
            } else {
                total++ // this is fishy...
            }
        } else {
            total+=romanNumerals.ValueOf(symbol)
        }
    }
    return total
} 
```

Enter fullscreen mode Exit fullscreen mode

仍然很糟糕，但也快好了。

如果你开始移动我们的`cases[:xx]`号，你会看到现在有相当多的正在通过。完全移除切片操作符，看看哪些失败了，这是我的套件中的一些例子

```
=== RUN   TestConvertingToArabic/'XL'_gets_converted_to_40
    --- FAIL: TestConvertingToArabic/'XL'_gets_converted_to_40 (0.00s)
        numeral_test.go:62: got 60, want 40
=== RUN   TestConvertingToArabic/'XLVII'_gets_converted_to_47
    --- FAIL: TestConvertingToArabic/'XLVII'_gets_converted_to_47 (0.00s)
        numeral_test.go:62: got 67, want 47
=== RUN   TestConvertingToArabic/'XLIX'_gets_converted_to_49
    --- FAIL: TestConvertingToArabic/'XLIX'_gets_converted_to_49 (0.00s)
        numeral_test.go:62: got 69, want 49 
```

Enter fullscreen mode Exit fullscreen mode

我认为我们所缺少的是对`couldBeSubtractive`的更新，这样它就能解释其他种类的减法符号

```
func couldBeSubtractive(index int, currentSymbol uint8, roman string) bool {
    isSubtractiveSymbol := currentSymbol == 'I' || currentSymbol == 'X' || currentSymbol =='C'
    return index+1 < len(roman) && isSubtractiveSymbol
} 
```

Enter fullscreen mode Exit fullscreen mode

再试一次，他们还是失败了。然而，我们之前留下了评论...

```
total++ // this is fishy... 
```

Enter fullscreen mode Exit fullscreen mode

我们永远不应该只是增加总数，因为这意味着每个符号都是一个`I`。替换为

```
total += romanNumerals.ValueOf(symbol) 
```

Enter fullscreen mode Exit fullscreen mode

所有的测试都通过了！现在我们已经有了完全正常工作的软件，我们可以放心地放纵自己进行一些重构了

## 重构

这是我完成的所有代码。我有过几次失败的尝试，但正如我一直强调的，这很好，测试帮助我自由地摆弄代码。

```
import "strings"

func ConvertToArabic(roman string) (total int) {
    for _, symbols := range windowedRoman(roman).Symbols() {
        total += allRomanNumerals.ValueOf(symbols...)
    }
    return
}

func ConvertToRoman(arabic int) string {
    var result strings.Builder

    for _, numeral := range allRomanNumerals {
        for arabic >= numeral.Value {
            result.WriteString(numeral.Symbol)
            arabic -= numeral.Value
        }
    }

    return result.String()
}

type romanNumeral struct {
    Value  int
    Symbol string
}

type romanNumerals []romanNumeral

func (r romanNumerals) ValueOf(symbols ...byte) int {
    symbol := string(symbols)
    for _, s := range r {
        if s.Symbol == symbol {
            return s.Value
        }
    }

    return 0
}

func (r romanNumerals) Exists(symbols ...byte) bool {
    symbol := string(symbols)
    for _, s := range r {
        if s.Symbol == symbol {
            return true
        }
    }
    return false
}

var allRomanNumerals = romanNumerals{
    {1000, "M"},
    {900, "CM"},
    {500, "D"},
    {400, "CD"},
    {100, "C"},
    {90, "XC"},
    {50, "L"},
    {40, "XL"},
    {10, "X"},
    {9, "IX"},
    {5, "V"},
    {4, "IV"},
    {1, "I"},
}

type windowedRoman string

func (w windowedRoman) Symbols() (symbols [][]byte) {
    for i := 0; i < len(w); i++ {
        symbol := w[i]
        notAtEnd := i+1 < len(w)

        if notAtEnd && isSubtractive(symbol) && allRomanNumerals.Exists(symbol, w[i+1]) {
            symbols = append(symbols, []byte{byte(symbol), byte(w[i+1])})
            i++
        } else {
            symbols = append(symbols, []byte{byte(symbol)})
        }
    }
    return
}

func isSubtractive(symbol uint8) bool {
    return symbol == 'I' || symbol == 'X' || symbol == 'C'
} 
```

Enter fullscreen mode Exit fullscreen mode

我对前面代码的主要问题类似于我们之前的重构。我们有太多的顾虑交织在一起。我们编写了一个算法，试图从字符串*和*中提取罗马数字，然后找到它们的值。

所以我创建了一个新的类型`windowedRoman`，它负责提取数字，并提供了一个`Symbols`方法来将它们作为一个切片进行检索。这意味着`ConvertToArabic`函数可以简单地迭代这些符号并将它们相加。

我通过提取一些函数对代码进行了分解，特别是在不可靠的 if 语句周围，以确定我们当前处理的符号是否是两个字符的减法符号。

可能有一种更优雅的方式，但我不会为此而烦恼。代码就在那里，它可以工作，并且经过了测试。如果我(或其他任何人)找到了更好的方法，他们可以安全地改变它——困难的工作就完成了。

## 基于属性的测试介绍

在本章中，我们学习了一些罗马数字的规则

*   不能有 3 个以上的连续符号
*   只有(1)、X (10)、C (100)和 M(1000)可以是“减法器”
*   获取`ConvertToRoman(N)`的结果并将其传递给`ConvertToArabic`应该会返回给我们`N`

到目前为止，我们已经编写的测试可以被描述为基于“示例”的测试，其中我们在我们的代码周围提供一些示例来验证工具。

如果我们能够利用这些我们所知道的关于我们领域的规则，以某种方式在我们的代码中运用它们，会怎么样呢？

基于属性的测试可以帮助你做到这一点，它向你的代码抛出随机数据，并验证你所描述的规则总是正确的。很多人认为基于属性的测试主要是关于随机数据的，但是他们错了。基于属性的测试的真正挑战是对你的领域有一个很好的理解，这样你才能编写这些属性。

话够了，来看看一些代码

```
func TestPropertiesOfConversion(t *testing.T) {
    assertion := func(arabic int) bool {
        roman := ConvertToRoman(arabic)
        fromRoman := ConvertToArabic(roman)
        return fromRoman == arabic
    }

    if err := quick.Check(assertion, nil); err != nil {
        t.Error("failed checks", err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 理性

我们的第一个测试将检查，如果我们将一个数字转换成罗马数字，当我们使用另一个函数将它转换回一个数字时，我们得到的是原来的数字。

*   给定的随机数(例如`4`)。
*   用随机数调用`ConvertToRoman`(如果`4`应该返回`IV`)。
*   取上面的结果，传给`ConvertToArabic`。
*   上面应该给了我们最初的输入(`4`)。

这感觉像是一个建立我们信心的很好的测试，因为如果其中任何一个有错误，它就会崩溃。唯一可以通过的方法是如果他们有相同类型的错误；这并非不可能，但感觉不太可能。

### 技术解释

我们使用标准库中的[测试/快速](https://golang.org/pkg/testing/quick/)包

从底部开始读，我们为`quick.Check`提供了一个函数，它将针对一些随机输入运行，如果函数返回`false`，它将被视为检查失败。

我们上面的`assertion`函数接受一个随机数，并运行我们的函数来测试属性。

###运行我们的测试

尝试运行它；你的电脑可能会挂起一段时间，无聊的时候就杀了它:)

这是怎么回事？尝试将以下内容添加到断言代码中。

```
assertion := func(arabic int) bool {
    if arabic <0 || arabic > 3999 {
        log.Println(arabic)
        return true
    }
    roman := ConvertToRoman(arabic)
    fromRoman := ConvertToArabic(roman)
    return fromRoman == arabic
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似这样的内容:

```
=== RUN   TestPropertiesOfConversion
2019/07/09 14:41:27 6849766357708982977
2019/07/09 14:41:27 -7028152357875163913
2019/07/09 14:41:27 -6752532134903680693
2019/07/09 14:41:27 4051793897228170080
2019/07/09 14:41:27 -1111868396280600429
2019/07/09 14:41:27 8851967058300421387
2019/07/09 14:41:27 562755830018219185 
```

Enter fullscreen mode Exit fullscreen mode

仅仅运行这个非常简单的属性就暴露了我们实现中的一个缺陷。我们使用`int`作为输入，但是:

*   你不能用罗马数字做负数
*   给定我们最多 3 个连续符号的规则，我们不能表示大于 3999 的值([嗯，有点像](https://www.quora.com/Which-is-the-maximum-number-in-Roman-numerals)),并且`int`具有比 3999 大得多的最大值。

这太棒了！我们被迫更深入地思考我们的领域，这是基于属性的测试的真正优势。

显然`int`不是很棒的类型。如果我们尝试更合适的方法呢？

### [`uint16`T4】](https://golang.org/pkg/builtin/#uint16)

Go 有用于*无符号整数*的类型，这意味着它们不能是负数；所以这立即排除了我们代码中的一类错误。通过添加 16，这意味着它是一个 16 位整数，可以存储最大值`65535`，这仍然太大，但让我们更接近我们需要的。

尝试更新代码以使用`uint16`而不是`int`。我在测试中更新了`assertion`,增加了一点可见性。

```
assertion := func(arabic uint16) bool {
    if arabic > 3999 {
        return true
    }
    t.Log("testing", arabic)
    roman := ConvertToRoman(arabic)
    fromRoman := ConvertToArabic(roman)
    return fromRoman == arabic
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行测试，他们现在实际运行，您可以看到正在测试什么。您可以多次运行来查看我们的代码是否经得起各种值的考验！这给了我很大的信心，我们的代码正在按照我们想要的方式工作。

默认的运行次数`quick.Check`是 100，但是你可以通过配置来改变。

```
if err := quick.Check(assertion, &quick.Config{
    MaxCount:1000,
}); err != nil {
    t.Error("failed checks", err)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步工作

*   您能编写检查我们描述的其他属性的属性测试吗？
*   你能想出一个办法让别人不可能用大于 3999 的号码来调用我们的代码吗？
    *   您可能会返回一个错误
    *   或者创建一个不能表示大于 3999 的新类型
        *   你认为什么是最好的？

## 包装完毕

### 更多的 TDD 实践与迭代开发

一开始，想到要编写将 1984 年转换成 MCMLXXXIV 的代码，您会感到害怕吗？对我来说确实如此，我已经写了很长时间的软件了。

和往常一样，诀窍是从简单的事情开始，然后从 T2 的小步骤开始。

在这个过程中，我们没有任何大的飞跃，没有任何大的重构，也没有陷入混乱。

我能听到有人嘲讽地说“这只是一个形”。我不能否认这一点，但我仍然对我参与的每个项目采取同样的方法。我从不在第一步中发布大型分布式系统，我发现团队可以发布的最简单的东西(通常是“Hello world”网站)，然后在可管理的块中迭代小的功能，就像我们在这里所做的一样。

技能是知道如何分配工作，这需要实践和一些可爱的 TDD 来帮助你。

### 基于属性的测试

*   内置于标准库中
*   如果你能想到用代码描述你的领域规则的方法，它们是给你更多自信的极好工具
*   迫使你深入思考你的领域
*   可能是对您的测试套件的一个很好的补充