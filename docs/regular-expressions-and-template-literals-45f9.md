# 正则表达式和模板文字

> 原文：<https://dev.to/rfornal/regular-expressions-and-template-literals-45f9>

## 设置

沿着这条线的某个地方，我听到了一个关于模板文字是使正则表达式更容易阅读的一个伟大工具的评论。我开始这篇文章的想法是，我想看看这是否是真的，并提出了这种类型的使用的一些例子。

有了一个想法，我开始了一个新项目。**这是一个练习**...简单明了。这种模式“可以”用在生产环境中，但是我现在在**建议**。

可能有一些经过审查的工具可以在前端完成这项工作。如果你知道这些，请在评论中列出一些；如果只是为了我的读者。

## 以前用正则表达式工作

我曾经为一个客户做过一个项目，在这个项目中，我必须为一种 30 年前的大型机驱动的客户端语言重新创建一个脚本解析器和引擎，我非常尊重正则表达式。我学到了很多(翻译成...许多糟糕的代码被编写和重构)。经过两次主要的重构，我有了一组工作代码...和数百个正则表达式来实现。

我使用了我所知道的每一个技巧来使解析器正则表达式服务更具可读性。我抽象并组合了各种有趣的模式，知道有一天这些代码会被其他人管理。

经过一番努力，以这种方式使用模板文字听起来非常有效和干净。当然，一些值得研究的东西。

## 我想做什么...

首先，我找到了一个正则表达式；类似这样的。我想拿走这个...

匹配文本，避免额外空格

```
// ^[\s]*(.*?)[\s]*$ 
```

Enter fullscreen mode Exit fullscreen mode

然后，从更清晰的地方生成它，就像这样...

```
const code0001 = `
  /* Matches text avoiding additional spaces
  */
  ^       // Beginning of line
  [\\s]*  // Zero or more whitespace
  (.*?)   // Any characters, zero to unlimited,
          //   lazy (as few times as possible, expanding as needed)
  [\\s]*  // Zero or more whitespace
  $       // End of line
`; 
```

Enter fullscreen mode Exit fullscreen mode

**注意**这里的\s 仍然需要转义...看起来很奇怪，但事实就是如此。

## 开始

首先，我需要去掉评论...

```
// Borrowed Function (stripComment uses the regex
// ... https://stackoverflow.com/a/47312708)
function stripComments(stringLiteral) {
  return stringLiteral
    .replace(/\/\*[\s\S]*?\*\/|([^:]|^)\/\/.*$/gm, '');
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码把代码翻译成了...

```
" ^    
  [\s]*
  (.*?)
  [\s]*
  $ " 
```

Enter fullscreen mode Exit fullscreen mode

基本上，现在我需要去掉换行符、换行符和空格(是的，我知道 regex 模式中可以有空格，但是为了简单起见，我在这个练习中选择忽略它)。删除不需要的字符...

```
// Starting Demo Code Here
function createRegex(stringLiteral) {
  return stripComments(stringLiteral)
    .replace(/(\r\n|r\|\n|\s)/gm, '');
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我这样做的能力...

```
const code0001regex = new RegExp(createRegex(code0001));

//          ORIGINAL FROM ABOVE: /^[\s]*(.*?)[\s]*$/
// GENERATED code001regex value: /^[\s]*(.*?)[\s]*$/ 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们来看看...

我在上面定义的 ***code0001*** 为了易读性已经被重写了(现在更容易琢磨这个正则表达式模式要做什么)...

```
// /^[\s]*(.*?)[\s]*$/
const code0001 = `
  ^       // Beginning of line
  [\\s]*  // Zero or more whitespace

  (.*?)   // Any characters, zero to unlimited,
          //  lazy (as few times as possible, expanding as needed)

  [\\s]*  // Zero or more whitespace
  $       // End of line
`; 
```

Enter fullscreen mode Exit fullscreen mode

***code0002***
匹配 ***任何有效的 HTML 标签和相应的结束标签*** ...在这里，我试图展示更高级的缩进(在代码和支持注释中)。

```
// <([a-z]+)([^<]+)*(?:>(.*)<\/\1>|\s+\/>)
const code0002 = `
  <               // Literal
  ([a-z]+)        // Group: First Tag (one or more)
  (               // Group
    [^<]+           // Match (one or more) NOT <
  )*              // Group-END: Zero or more times
  (?:             // Group-NON-CAPTURE
    >               // Literal
    (.*)<\\/\\1>    // Up to and including SLASH and First Tag group above
    |\\s+\\/>       // OR spaces and close tag
  )               // Group-END
`; 
```

Enter fullscreen mode Exit fullscreen mode

***code0003***
匹配 ***文本*** 内任何有效的十六进制颜色。

```
// \B#(?:[a-fA-F0–9]{6}|[a-fA-F0–9]{3})\b
const code0003 = `
  \\B#              // Non-word boundary, Literal #
  (?:               // Group-NON-CAPTURE
    [a-fA-F0–9]{6}    // 1st alternative
    |[a-fA-F0–9]{3}   // 2nd alternative
  )                 // Group-END
  \\b               // Word boundary
`; 
```

Enter fullscreen mode Exit fullscreen mode

***code0004***
匹配 ***任何有效邮件内的文字*** 。

```
// \b[\w.!#$%&’*+\/=?^`{|}~-]+@[\w-]+(?:\.[\w-]+)*\b
const code0004 = `
  \\b                           // Word boundary
  [\\w.!#$%&’*+\\/=?^\`{|}~-]+  // Character in this list (and word), one to unlimited
  @                             // Literal
  [\\w-]+                       // One to unlimited word and character "-"
  (?:                           // Group-NON-CAPTURE
    \\.[\\w-]+                    // Literal ".", one to unlimited word and character "-"
  )*                            // Group-END (zero or more)
  \\b                           // Word boundary
`; 
```

Enter fullscreen mode Exit fullscreen mode

***code0005***
强密码:最小长度为 6，至少一个大写字母，至少一个小写字母，至少一个数字，至少一个特殊字符。

```
// (?=^.{6,}$)((?=.*\w)(?=.*[A-Z])(?=.*[a-z])
// ... (?=.*[0-9])(?=.*[|!"$%&\/\(\)\?\^\'\\\+\-\*]))^.*
const code0005 = `
  (?=           // Group-POSITIVE-LOOKAHEAD
    ^             // BOL
    .{6,}         // Any six characters except line terminators
    $             // EOL
  )             // Group-POSITIVE-LOOKAHEAD-END
  (             // Group
    (?=.*\\w)     // Group-POSITIVE-LOOKAHEAD
                  // Any Characters, zero to unlimited
                  // Any Word

    (?=.*[A-Z])   // Group-POSITIVE-LOOKAHEAD
                  // Any Characters, zero to unlimited
                  // Any Character (A-Z)

    (?=.*[a-z])   // Group-POSITIVE-LOOKAHEAD
                  // Any Characters, zero to unlimited
                  // Any Character (a-z)

    (?=.*[0-9])   // Group-POSITIVE-LOOKAHEAD
                  // Any Characters, zero to unlimited
                  // Any Character (0-9)

    (?=.*[|!"$%&\\/\\(\\)\\?\\^\\'\\\\\\+\\-\\*])
                  // Group-POSITIVE-LOOKAHEAD
                  // Any Characters, zero to unlimited
                  // Any Character in the list
  )             // Group-END
  ^             // BOL
  .*            // Match Any Characters, zero to unlimited
`; 
```

Enter fullscreen mode Exit fullscreen mode

***code 0006***
—SSN—社会安全号码(简单)

```
// ^((?<area>[\d]{3})[-][\d]{2}[-][\d]{4})$
const code0006 = `
  ^                   // BOL
  (                   // Group
    (?<area>            // Group-NAMED area
      [\\d]{3}            // 3-Digits
    )                   // Group-NAMED-END
    [-]                 // Literal, Dash
    [\\d]{2}            //  2-Digits
    [-]                 // Literal, Dash
    [\\d]{4}            // 4-Digits
  )                   // Group-END
  $                   // EOL
`; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

整篇文章是对使用 JavaScript 的一些模板文字生成正则表达式的不同理解。这是一个实验。我相信是成功的。

这个练习还指出，随着模式变得更容易理解，针对正则表达式编写测试会变得容易得多。

这里生成的正则表达式更容易阅读和推理，这是我们的目标。如果在一个项目中需要大量的正则表达式模板，我可以使用这种模式。