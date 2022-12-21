# 每日挑战#23 -莫尔斯电码解码器

> 原文：<https://dev.to/thepracticaldev/daily-challenge-23-morse-code-decoder-1b8m>

利用编程的力量，我们翻译了猪的拉丁文，并告诉人类时间。现在为了这个挑战，你必须写一个简单的莫尔斯电码解码器。今天的挑战来自 CodeWars 上的用户 jolaf

> 你的任务是实现一个功能，将莫尔斯电码作为输入，并返回一个解码的人类可读的字符串。
> 
> 例如:
> `decodeMorse('.... . -.-- .--- ..- -.. .')`
> 应该返回`"HEY JUDE"`
> 
> * * *
> 
> c++/Go/JavaScript/PHP/Python/Ruby/TypeScript:`MORSE_CODE['.--']`
> c#:`MorseCode.Get(".--") (returns string)`
> Elixir:`morse_codes variable`
> Elm:`MorseCodes.get : Dict String String`
> Haskell:`morseCodes ! ".--" (Codes are in a Map String String)`
> Java:`MorseCode.get(".--")`
> Kotlin:`MorseCode[".--"] ?: "" or MorseCode.getOrDefault(".--", "")`
> Rust:`self.morse_code`
> Scala:`morseCodes(".--")`

**注意**:出于编码目的，你应该使用 ASCII 字符`.`和`-`，而不是 Unicode 字符。

我一直觉得做一个全尺寸的莫尔斯电码解码器，以音频为输入，大声读出输出，真的会很酷。也许我可以开始把它作为一个附带项目来建造——它会给我一个借口去买另一个树莓派。

祝你好运，编码快乐！

* * *

*感谢 [CodeWars](https://codewars.com/) ，他已经在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想要对未来的帖子提出挑战吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！