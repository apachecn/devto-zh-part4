# 用 Rust 和 POM 解析世界

> 原文：<https://dev.to/pragmalang/parsing-the-world-with-rust-and-pom-eb6>

作为程序员，我们花很多时间处理文本字符串。通常，我们从用户那里接收文本作为输入，或者我们阅读文本文件并试图理解它们的内容。

在很多情况下，我们使用正则表达式来查看文本是否匹配某种模式，或者从字符串中提取一些信息。例如，如果您收到一个用户名作为表单输入，并且您想确保它不包含任何空格，那么您可以使用类似于`"^\S+$"`的正则表达式将一个字符串与一个或多个非空白字符进行匹配(`^`表示字符串的开头，`\S`表示非空白字符，`+`表示重复一次或多次，`$`表示字符串的结尾)。你甚至可以写一个类似于
的函数

```
fn has_whitespaces(s: String) -> bool {
  s.contains(' ')
} 
```

Enter fullscreen mode Exit fullscreen mode

> 你能找出代码中的错误吗？

但是，如果您试图解析更复杂的字符串的内容呢？比如说，一个 JSON 字符串，一个 CSV(逗号分隔值)文件，甚至一个程序？如果你不使用现有的库函数，你将很难使用正则表达式和字符串方法。

在本文中，我们将探索 [POM](https://github.com/J-F-Liu/pom) 库，它为直观地定义和组合解析器提供了一个非常酷的界面。使用这个库，你可以方便地定义一门正式语言的全部语法(也许是你自己的新编程语言？)

POM 是一个 [PEG](https://en.wikipedia.org/wiki/Parsing_expression_grammar) (解析表达式语法)的实现，它是一种形式语言在符号、字符串表达式和规则方面的定义。

例如，如果我们要定义一个解析变量定义的规则，比如:`let x = 1;`，我们会说`let`是一个符号序列，`=`，`;`，空格是符号，`x`和`1`是字符串表达式。变量定义包括`let`序列，后面是一个空格，后面是**有效标识符**(例如`x`，后面是零个或多个空格，后面是`=`符号，后面是零个或多个空格，后面是一个有效表达式(本例中为`1`，后面是零个或多个空格和`;`符号。有点啰嗦，不是吗？这就是形式语言的定义。

幸运的是，POM 提供了定义这些规则的非常明确的方式。它允许我们定义不同的规则来解析每一小段文本，然后使用算术和逻辑运算符将它们组合起来，形成更复杂的规则。这些操作符被称为*解析器组合子*。

POM 定义了一个叫做`Parser`的类型，用来编码解析规则。可以使用该库提供的许多预定义解析器来构建解析器，比如`sym`和`seq`函数。上面这个变量定义的例子可以用 POM 表示为:

```
use pom::char_class::*;
use pom::parser::*;

fn variable_def<'a>() -> Parser<'a, u8, (String, u32)> {
  let valid_id = (is_a(alpha) + is_a(alphanum).repeat(0..))
    .map(|(first, rest)| format!("{}{}", first as char, String::from_utf8(rest).unwrap()));
  let valid_expr = one_of(b"0123456789")
    .repeat(1..10)
    .convert(String::from_utf8)
    .convert(|s| s.parse::<u32>());
  seq(b"let") * sym(b' ') * valid_id - sym(b' ') - sym(b'=') 
    - sym(b' ').repeat(0..) + valid_expr - sym(b';')
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们分解代码:
1——我们导入`pom::char_class`的所有内容，它导出
谓词，如`alpha`和`alphanum`。我们使用这些谓词来测试输入字符的类型。
`pom::parser`导出`seq`，`sym`以及其他返回
`Parser`
的函数

```
use pom::char_class::*;
use pom::parser::*; 
```

Enter fullscreen mode Exit fullscreen mode

2-`variable_def`函数不带参数，返回一个生存期为`'a`的`Parser`
，接受`u8` s(字符字节)作为输入，
输出一个`(String, u32)`的元组。元组编码变量的
标识符及其值(为了简单起见，我们将有效值限制为 9 位正整数
。)

```
fn variable_def<'a>() -> Parser<'a, u8, (String, u32)> 
```

Enter fullscreen mode Exit fullscreen mode

3 -有效的变量标识符至少由一个字母字符组成，后跟零个或多个字母数字字符。我们使用`is_a`解析器来测试变量 id 的第一个字符`is_a(alpha)`，以及 id 的其余部分`is_a(alphanum)`。
注意`+`操作符是如何用于组合变量标识符的两个部分的。它返回一个包含两个操作数结果的元组，然后我们在`map`方法调用中析构它。`map`用于转换一个解析器的结果，并返回一个新的解析器。在本例中:`(is_a(alpha) + is_a(alphanum).repeat(0..))`返回一个`Parser<'a, u8, (u8, Vec<u8>)>`(第一个字符和其余的字符)，然后我们通过使用`format!`连接第一个字符和其余的字符，将它转换成一个`Parser<'a, u8, String>`(整个标识符)。

```
let valid_id = (is_a(alpha) + is_a(alphanum).repeat(0..))
    .map(|(first, rest)| format!("{}{}", first as char, String::from_utf8(rest).unwrap())); 
```

Enter fullscreen mode Exit fullscreen mode

4 -一个有效的表达式被定义为 1 到 9 个数字被转换成一个`String`，然后被解析为一个`u32`。这里使用`convert`而不是`map`，因为`String::from_utf8`返回一个`Result`，所以如果`String::from_utf8`的结果是`Err`，解析器不会匹配表达式。

```
let valid_expr = one_of(b"0123456789")
    .repeat(1..10)
    .convert(String::from_utf8)
    .convert(|s| s.parse::<u32>()); 
```

Enter fullscreen mode Exit fullscreen mode

5 -返回的解析器是上面提到的符号与一些空格的组合，以及一个有效的标识符和表达式。`*`操作符组合两个解析器并返回右边解析器的结果。所以`Parser<'a, u8, T> * Parser<'a, u8, U> = Parser<'a, u8, U>`。我们还看到了正在使用的`-`操作符，它组合了两个解析器，并返回一个包含左侧解析器值的解析器。这些操作符遵循与 Rust 中普通算术操作符相同的优先规则。

```
seq(b"let") * sym(b' ') * valid_id - sym(b' ') - sym(b'=') 
    - sym(b' ').repeat(0..) + valid_expr - sym(b';') 
```

Enter fullscreen mode Exit fullscreen mode

> 看看[这个方便的解析器组合子表](https://github.com/J-F-Liu/pom#list-of-predefined-parsers-and-combinators)。

现在来测试我们的解析器:

```
#[test]
fn test_variable_parser() {
  let valid_variable_byte_string = b"let v1 = 42;";
  let parsed_variable = variable_def().parse(valid_variable_byte_string);
  assert_eq!(parsed_variable, Ok(("v1".to_string(), 42)));

  let invalid_variable_byte_string = b"let nosemicolon = 42";
  let parsed_variable = variable_def().parse(invalid_variable_byte_string);
  assert_eq!(parsed_variable, Err(pom::Error::Incomplete));

  let invalid_variable_byte_string = b"let morethan9digits = 1234567890;";
  let parsed_variable = variable_def().parse(invalid_variable_byte_string);
  assert_eq!(
    parsed_variable,
    Err(pom::Error::Mismatch {
      message: "expect: 59, found: 48".to_string(),
      position: 31
    })
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 POM 来解析正整数值的变量定义可能看起来有些过火，但是请想象一下用它来解析整个编程语言。

我希望这篇文章对你有所帮助。现在享受用你的方式解析每一个字符串。

特别感谢[刘俊峰](https://github.com/J-F-Liu)和所有贡献者创造了一个惊人的图书馆。你应该得到一块饼干🍪