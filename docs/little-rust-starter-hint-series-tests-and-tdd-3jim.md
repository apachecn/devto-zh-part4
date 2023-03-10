# 小锈启动提示系列:测试和 TDD

> 原文：<https://dev.to/5422m4n/little-rust-starter-hint-series-tests-and-tdd-3jim>

今天，我们将通过应用测试驱动开发(TDD)方法来解决 rust 中的[日常挑战#47 -字母表](https://dev.to/thepracticaldev/daily-challenge-47-alphabets-4cbn)。不要担心，我们会做很少的步骤，完整的代码可作为回购(链接在最后)。

对于那些熟悉 Javascript 中已知的测试框架(如 karma 或 mocha)的人来说，我将提供一个很好的奖励，并展示我们如何使用 rspec 语法来使我们的测试代码更具表现力和可读性。

## 问题陈述

首先，挑战中的问题陈述是

> 在今天的挑战中，您需要用字母表中的位置替换给定字符串中的每个字母，其中“a”= 1，“b”= 2，等等。
> 比如:
> alphabet_position("日落十二点。”)应以字符串形式返回 20 8 5 19 21 14 19 5 20 19 5 20 19 1 20 20 20 23 5 12 22 5 15 3 12 15 3 15 3 11。

## 初试第一

让我们从`cargo new`开始一个新项目，并准备好源文件

```
cargo new dev_challenge_47
cd dev_challenge_47 && mkdir -f tests
touch tests/alphabet_position_test.rs 
```

Enter fullscreen mode Exit fullscreen mode

所以现在让我们从把需求翻译成代码开始，这是暂时的

> 对于给定的字符串，其中“a”= 1

```
// test/alphabet_position_test.rs
use dev_challenge_47::alphabet_position;    // <-- we don't have this function yet

#[test]                                     // <-- how to define a test in rust
fn it_should_replace_the_a_with_1() {        // <-- we express the requirement as name
    let replaced = alphabet_position("a");
    assert_eq!(replaced, "1");              // <-- we assert that the both are equal
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，代码甚至无法编译，所以让我们编写最小的实现来获得失败的测试，而不是一个编译项目。

```
// src/lib.rs
pub fn alphabet_position(s: &str) -> String {
    String::from("Hello")
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们只需要返回一个预定义的“Hello”字符串就可以了。

当我们现在运行`cargo test`时，我们会得到很多输出，但也会得到

```
test it_should_ignore_non_characters ... FAILED 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们第一次测试失败了。

## 让测试通过

让我们通过修改代码来修复我们的第一个测试。我们现在可以通过非常简单的实现来做到这一点，用“1”替换“a ”,但我会跳过这一步，直接进行重构。在这里我们会用到类似 ascii 的数学:`x - 'a' + 1`

```
// src/lib.rs
pub fn alphabet_position(s: &str) -> String {
    s.chars()                             // <-- get an iterator over all chars
        .map(|x| -> u8 { x as u8 - 'a' as u8 + 1 })   // <-- substract the ascii value of 'a'
        .map(|x| -> String { x.to_string() })         // <-- convert the char to a String
        .collect::<Vec<String>>()                     // <-- collect a Vector of String
        .join(" ")                                    // <-- join the Strings by whitespace
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有很多东西要学..

*   返回字符的迭代器
*   应用一个执行 ascii 数学运算的闭包。
*   `.map`应用一个将字符转换成字符串的闭包
*   将所有条目收集到一个向量中
*   `.join`将所有的字符串连接成一个

重新运行`cargo test`后，我们看到我们的小测试现在是绿色的。

## 下一次迭代

你可能已经猜到了，现在我们回到我们测试，写另一个东西。这基本上就是 TDD 的循环，我们只需编写尽可能多的测试和代码，这样我们就能一点一点地满足所有的需求。这次我们在这里增加了这个测试:

```
// test/alphabet_position_test.rs
#[test]
fn it_should_replace_the_capital_a_with_1() {
    let replaced = alphabet_position("A");
    assert_eq!(replaced, "1");
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:我太懒了，不想重新运行`cargo test`,这就是为什么我使用`cargo watch -x check -x test`来监视任何文件的变化，并自动重新运行测试。结帐[的文件](https://docs.rs/crate/cargo-watch/3.2.0/source/README.md)安装。

现在让我们再次修复失败的测试

```
// src/lib.rs
pub fn alphabet_position(s: &str) -> String {
    s.to_lowercase()      // <-- adding this line
        .chars()
        .map(|x| -> u8 { x as u8 - 'a' as u8 + 1 })
        .map(|x| -> String { x.to_string() })
        .collect::<Vec<String>>()
        .join(" ")
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们全部小写，这样我们的数学就不会不及格。

## 又一次迭代

好了，在我们进入完整的句子之前，让我们先来看看一些案例:

```
// test/alphabet_position_test.rs
#[test]
fn it_should_ignore_non_characters() {
    let replaced = alphabet_position("'a a. 2");
    assert_eq!(replaced, "1 1");
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们跳过所有的非字母来修正这个测试:

```
pub fn alphabet_position(s: &str) -> String {
    s.to_lowercase()
        .chars()
        .filter(|x| x.is_alphabetic())        // <-- adding this line here
        .map(|x| -> u8 { x as u8 - 'a' as u8 + 1 })
        .map(|x| -> String { x.to_string() })
        .collect::<Vec<String>>()
        .join(" ")
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我想不出更多的极限情况，所以如果你有一个完整的测试用例，验证它总是好的。幸运的是，我们在挑战描述中免费得到了一个:

```
#[test]
fn it_should_replace_the_sentence() {
    let replaced = alphabet_position("The sunset sets at twelve o' clock.");
    assert_eq!(
        replaced,
        "20 8 5 19 21 14 19 5 20 19 5 20 19 1 20 20 23 5 12 22 5 15 3 12 15 3 11"
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，测试也是绿色的，没有任何代码更改。干得好:)

## 规格 ftw

如上所述，我想展示我们现在如何用类似 rspec 的语法重写我们的测试，使它更具表现力。为此，我将使用[一个名为 prospect](https://crates.io/crates/speculate)的板条箱，它与方便的糖果一起提供。让我们添加依赖性。

```
$ cat >> Cargo.toml
[dev-dependencies]
speculate = "0.1"
^D 
```

Enter fullscreen mode Exit fullscreen mode

为了便于演示，我将创建一个新的测试文件(`touch test/alphabet_position_spec.rs`)并保留旧的文件。但是您也可以重构现有的。

```
// test/alphabet_position_spec.rs
use dev_challenge_47::alphabet_position;
use speculate::speculate;

speculate! {                  // <-- it's a macro but who cares
    describe "alphabet_position" {
        it "should replace 'a' with 1" {
            assert_eq!(alphabet_position("a"), "1");
        }
        it "should replace 'A' with 1" {
            assert_eq!(alphabet_position("A"), "1");
        }
        it "should ignore non characters" {
            assert_eq!(alphabet_position("'a a. 2"), "1 1");
        }
        it "should replace the full test sentence" {
            assert_eq!(
                alphabet_position("The sunset sets at twelve o' clock."),
                "20 8 5 19 21 14 19 5 20 19 5 20 19 1 20 20 23 5 12 22 5 15 3 12 15 3 11"
            );
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我觉得很好看，你的呢？

我唯一要抱怨的是运行`cargo test`时的输出，它并不像你从 karma 或其他人那里习惯的那样好。

```
running 4 tests
test speculate_0::alphabet_position::test_should_ignore_non_characters ... ok
test speculate_0::alphabet_position::test_should_replace_A_with_1 ... ok
test speculate_0::alphabet_position::test_should_replace_a_with_1 ... ok
test speculate_0::alphabet_position::test_should_replace_the_full_test_sentence ... ok 
```

Enter fullscreen mode Exit fullscreen mode

你仍然可以得到它，代码变得更有表现力和可读性，这对我来说足够重要，以使用这个漂亮的板条箱。

使用的版本:

```
$ cargo --version && rustc --version
cargo 1.37.0 (9edd08916 2019-08-02)
rustc 1.37.0 (eae3437df 2019-08-13) 
```

Enter fullscreen mode Exit fullscreen mode

请不要忘记分享你的反馈，如果有的话，请让我知道你学到了什么。

[完整的代码可以在 github 上找到](https://github.com/sassman/edu-tdd-rust)