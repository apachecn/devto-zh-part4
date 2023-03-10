# 用 OCaml 编写解释器

> 原文：<https://dev.to/nt591/writing-an-interpreter-in-ocaml-45hm>

* * *

代码在我的 [Github](https://github.com/nt591/monkey-ocaml/tree/46882b03ca7d911b5a12b0e47738778fde5ee7fc) 上

* * *

## 背景

我决定再试试这个翻译。我将使用 Thorsten Ball 的书[编写 Go 中的解释器](https://interpreterbook.com/)作为我的主要来源，额外的阅读材料来自 Bob Nystrom 的[制作解释器](https://craftinginterpreters.com)。我发现，Thorsten 的书让我更快地提高效率，而 Bob 的材料更深入，有助于澄清和阐明我不理解的内容。

Thorsten 的书定义了一种叫做 Monkey 的语言，它在语法上类似于 Javascript。花括号，整数数学，不区分空白，递归，全部九码。

对于这个项目，我将使用 OCaml 语言。OCaml 位于学术界和大型生产规模软件的交汇处。它被用于简街，码头，彭博，脸书和其他公司。我计划最终在 Haskell 中重做这个项目——在写这一章时，我错过了 Haskell 的一些语法和约定。我发现使用 Haskell 的点符号和`$`应用程序的内联函数组合和应用程序比管道更具可读性。

```
-- Haskell
next_char . read_char $ lexer 
```

```
(* OCaml *)
lexer |> read_char |> next_char 
```

## 要求

*   沙丘 1.11.x
*   opam 2.0.3
*   OCaml 4.07.1
*   alcostest 0 . 8 . 5(测试框架)
*   Fmt 0.8.8(测试用漂亮打印机)

使用`opam`来设置 OCaml 并安装那些依赖项。我选择了一个新版本的 Dune，我选择了 Alcotest，因为它在 OCaml discord 上似乎相对流行。它的可读性很强，配置起来也很愉快。

## 项目结构

看一下这个[提交](https://github.com/nt591/monkey-ocaml/commit/7dba667d0c20c69aaddaa4640479f26f5673ee20)你可以复制一些文件

```
ROOT
  - dune-project
  - monkey.intall
  - monkey.opam
  - src/
    - dune
  - test/
    - dune 
```

dune 文件将允许您链接和构建您的代码并运行测试。我们将随着我们的进展更新这些，但现在这将让你开始。

## 定义我们的第一个令牌

Thorsten 解释器的第一步是定义我们的标记子集。在词法分析中，令牌是一个简单的数据结构，它代表了我们编程语言中的一小段语法。令牌可以是分号、加号、标识符(“x”)、整数(5)或其他表示形式。

我们将在`src/token.ml`中定义我们的令牌。创建该文件，让我们添加一些代码。我们将打开一个模块并添加基本令牌。

```
module Token = struct
  type token_type =
    | ILLEGAL
    | EOF
    (* Identifiers and literals *)
    | IDENT of string
    | INT of int
    (* Operators *)
    | ASSIGN
    | PLUS

    (* -- Delimiters *)
    | COMMA
    | SEMICOLON
    | LPAREN
    | RPAREN
    | LBRACE
    | RBRACE
    (* -- Keywords *)
    | FUNCTION
    | LET
end 
```

我们还将添加一个`token_to_string`函数，这样我们就可以为我们的测试打印出一些令牌，并且当编译器不可避免地对我们咆哮未使用的代码时，让我们的编译器高兴。

```
let token_to_string = function
  | ILLEGAL -> "ILLEGAL"
  | EOF -> "EOF"
  | IDENT a -> "IDENT " ^ a
  | INT a -> "INT " ^ string_of_int a
  | ASSIGN -> "ASSIGN"
  | PLUS -> "PLUS"
  | COMMA -> "COMMA"
  | SEMICOLON -> "SEMICOLON"
  | LPAREN -> "LPAREN"
  | RPAREN -> "RPAREN"
  | LBRACE -> "LBRACE"
  | RBRACE -> "RBRACE"
  | FUNCTION -> "FUNCTION"
  | LET -> "LET" 
```

与 Thorsten 的 Go 实现不同，我们不需要保存常数列表。相反，我们可以使用 OCaml 的代数数据类型来表示令牌。事实上，正是因为这个原因，OCaml 通常被用于编译器和类似的开发工具中。

然后，Thorsten 继续创建一些测试，以构建他的`NextChar`函数。我们将进行类似的 add 测试。

我们需要建立一个小型模块出口。创建`src/monkey.ml`并添加以下代码。

```
module Token = Token
module Lexer = Lexer 
```

创建一个文件`test/test.ml`，让我们添加一些样板文件。

```
 open Monkey
include Lexer
include Token

let token_testable = Alcotest.testable Token.pretty_print (=)

let test_lexer_delimiters () =
  Alcotest.(check (list token_testable))
    "same token types" [
        Token.ASSIGN
      ; Token.PLUS
      ; Token.LPAREN
      ; Token.RPAREN
      ; Token.LBRACE
      ; Token.RBRACE
      ; Token.COMMA
      ; Token.SEMICOLON
      ; Token.EOF
      ]
      (Lexer.generate_tokens "=+(){},;")

let () =
  Alcotest.run "Lexer"
    [
      ( "list-delimiters",
        [ Alcotest.test_case "first case" `Slow test_lexer_delimiters ] );
    ] 
```

看到那个`Token.pretty_print`功能了吗？我们需要定义一种方法来返回表示我们的令牌的格式化字符串。让我们回到`src/token.ml`

```
let pretty_print ppf tok = Fmt.pf ppf "Token %s" (token_to_string tok) 
```

我们可以查看 Alcotest 的[源代码](https://github.com/mirage/alcotest/blob/24a77e6f8b025d8fd79a6bfcf5f77a26ba8b8a19/src/alcotest.ml#L773-L781)来深入了解这段代码的作用。实际上，对于任何推断的类型`a`，它需要一个格式化类型`a`的函数和一个接受两个`a`并返回一个布尔值的等式检查函数。您可以查看`int32`或`string`的实现，以获得其工作原理的提示。我们的`token_testable`是一个在`Token.token_type`上工作的模块，T6 是我们定义的一组令牌标签。如果我们运行`dune runtest`，我们会得到一个错误，因为我们还没有实现我们的 lexer。让我们开始吧。

## 设置 Lexer

让我们来创造`src/lexer.ml`。

```
(* lexer *)

module Lexer = struct
  include Token

  type lexer = {
    input : string;
    position : int;
    read_position : int;
    ch : char;
  }

  let null_byte = '\x00'

  let new_lexer input_string =
    {
      input = input_string;
      position = 0;
      read_position = 0;
      ch = null_byte;
    }
end 
```

我选择使用 null_byte 而不是 OCaml [选项类型](http://ocaml-lib.sourceforge.net/doc/Option.html),因为在我看来，一个选项代表一个潜在的缺口，而我们知道我们的源代码中不会有缺口。我们将简单地读取字符串，直到用完所有的字符串。我们的源代码的结尾不是未定义的，而是一个已知的值。

我们需要定义我们的`read_char`函数。在 lexer 中，读取一个字符只是简单地增加 lexer 中的指针，并查看输入字符串中的下一个字符。

```
let read_char lexer =
  let read_to_end = lexer.read_position >= String.length(lexer.input) in
  let new_ch = if read_to_end then null_byte else String.get lexer.input lexer.read_position
  in {lexer with position = lexer.read_position; read_position = lexer.read_position + 1; ch = new_ch} 
```

OCaml 默认是不可变的，所以我们不能只调整我们的 lexer。或者说，我们可以，但当我们可以避免这种可变状态时，为什么要这样做呢？我们看看我们是否在绳子的末端。如果是，那么这个字符是空的，否则我们得到字符串中的下一个字符并更新我们的 lexer。为了初始化我们的 lexer，我们可以使用我们的`read_char`。我们将更新`new_lexer`看起来像

```
 let new_lexer input_string =
  let lexer = {
    input = input_string;
    position = 0;
    read_position = 0;
    ch = null_byte;
  } in
  read_char lexer 
```

厉害！我们的测试代码仍然在调用`Lexer.generate_tokens`，所以我们需要继续努力。

```
let next_char lexer = match lexer.ch with
  | '=' -> (read_char lexer, Token.ASSIGN)
  | ';' -> (read_char lexer, Token.SEMICOLON)
  | '(' -> (read_char lexer, Token.LPAREN)
  | ')' -> (read_char lexer, Token.RPAREN)
  | ',' -> (read_char lexer, Token.COMMA)
  | '+' -> (read_char lexer, Token.PLUS)
  | '{' -> (read_char lexer, Token.LBRACE)
  | '}' -> (read_char lexer, Token.RBRACE)
  | '\x00' -> (lexer, Token.EOF)
  | _ -> failwith "unmatched character"

let generate_tokens input_string =
  let lexer = new_lexer input_string in
  let rec gen lxr tokens =
    match next_char lxr with
    | (_, Token.EOF) -> List.rev_append tokens [Token.EOF]
    | (l, tok) -> gen l (tok :: tokens)
  in gen lexer [] 
```

这段代码匹配我们已知的输入，并返回一个更新的 lexer 和最后读取的令牌的元组。这个元组允许我们收集令牌并继续调用`next_char`，直到它在我们的`generate_tokens`函数中结束。

`generate_tokens`正在定义一个在词法分析器上调用`next_char`的递归函数。对于任何令牌，它会将该令牌添加到一个可见令牌列表中，并在新的 lexer 上再次调用`gen`。一旦它找到一个`EOF`令牌，它会将收集到的令牌`rev_append`到`EOF`。

附注:`rev_append`反转第一个列表并将其连接到第二个列表。因为我们在非 EOF 的情况下使用 OCaml `cons`操作符，所以我们实际上是以相反的顺序构造一个列表，所以我们需要在最后翻转它。原因是`cons`是常数时间，其中`append`是与第一个列表成比例的线性时间(在添加第二个列表的元素之前，操作必须遍历整个列表)。所以更容易在末尾反转(线性时间一次)而不是追加(每个令牌线性时间)。

如果我们运行`dune runtest`，我们将通过测试！

## 接下来

下一章将关注为真正的源代码添加标记。