# 使用 Match–再次拜访 Bob

> 原文：<https://dev.to/jculverhouse/using-match-bob-revisited-2jc4>

<figure>[![TRY AGAIN in Scrabble letter - as I try again with an Exercism task, this time using match.](img/687e2674988ce72e1dcbc78eedeb0abf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LmCGtYzO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/board-games-encouragement-games-scrabble-spell-spelling-1520271-pxhere.com_.jpg%3Ffit%3D840%252C461%26ssl%3D1) 

<figcaption>等等，“TG”？那不是一个词！挑战！</figcaption>

</figure>

还记得之前在[的帖子](https://rust.graystorm.com/2019/07/01/return-to-string-basics/)中的鲍勃吗——我们最小的人工智能反应者？我从[exercisem](https://exercism.io/)导师那里得到反馈，我已经避开了`Rust`最全能的操作符之一，我应该考虑使用 match。所以，我不想做个混蛋，只是想看看我是否能替换我方法中所有的`if`,我很快想到了这个解决方案。

## 切换到使用匹配

```
pub fn reply(message: &str) -> &str {
    let message = message.trim();
    let question = message.ends_with('?');
    let any_alpha = message.chars().any(char::is_alphabetic);
    let is_yelling = message.to_ascii_uppercase() == message;
    let is_empty = message.len() == 0;
    match message.is_empty() {
        true => "Fine. Be that way!",
        _ => match any_alpha && is_yelling {
            true => match question {
                true => "Calm down, I know what I'm doing!",
                _ => "Whoa, chill out!",
            },
            _ => match !is_empty && question {
                true => "Sure.",
                _ => "Whatever.",
            },
        },
    }
} 
```

## 来吧鲍勃，快点生锈吧

所以，生锈可能是我的新习惯，但我不确定版本 2 是否比我开始时的代码“更易维护”。我原以为这个练习任务只是学习一些字符串方法。原来，这也是关于学习`match`操作符。当然，你不会用这种方式编写任何解析器或 AI——这只是为了练习。

导师指出的另一件小事是，我在之前的代码中确实有`let check_message = message.trim()`。Rust 中更惯用的方法是将它重新分配给同一个变量，因此在这个版本中使用了`let message = message.trim()`。我的猜测是:更少的内存分配，更少的变量需要你去跟踪，并且你不会在方法的后面错误地使用错误的变量。实际上，这可能不是一个过时的想法——这只是一个很好的编程技巧。

## 摇头晃脑带着一种特质

有人告诉我，模式在 Rust 中非常强大，这是尽可能开始使用`match`的一个重要原因。我也对`traits`和`impls`有所了解，我在[早先的帖子](https://rust.graystorm.com/2019/07/10/rust-functions-methods-and-traits/)中提到过。这让我想到，也许我应该研究一下语法，用一个`trait`来代替它。这个想法导致了这个代码。

```
pub fn reply(message: &str) -> &str {
    enum Quality {
        Empty,
        YellQuestion,
        YellStatement,
        AskQuestion,
        Statement, };

    trait HasQuality {
        fn has_quality(&self) -> Quality;
    }

    impl HasQuality for str {
        fn has_quality(&self) -> Quality {
            let message = self.trim();
            let question = message.ends_with('?');
            let any_alpha = message.chars().any(char::is_alphabetic);
            let is_yelling = message.to_ascii_uppercase() == message;
            let is_empty = message.len() == 0;

            match message.is_empty() {
                true => Quality::Empty,
                _ => match any_alpha && is_yelling {
                    true => match question {
                        true => Quality::YellQuestion,
                        _ => Quality::YellStatement,
                    },
                    _ => match !is_empty && question {
                        true => Quality::AskQuestion,
                        _ => Quality::Statement,
                    },
                },
            }
        }
    };
    match message.has_quality() {
        Quality::Empty => "Fine. Be that way!",
        Quality::YellQuestion => "Calm down, I know what I'm doing!",
        Quality::YellStatement => "Whoa, chill out!",
        Quality::AskQuestion => "Sure.",
        _ => "Whatever.",
    }
} 
```

所以在这里，我们提出了质量`trait`的概念，然后我们为内置的`str`原语实现这个`trait`。我们已经用我们自己的`trait`把你能做的扩展到了`str`！Bob 的第 3 版确实有助于揭示一些我需要磨练的 Rust 思维模式。

当然，我确保版本 3 仍然通过了这个任务的所有 Exercism 测试。这种变化接近了我的知识极限，我无法在没有书本帮助的情况下完成它——只是服从每一个编译器的抱怨。然而，我比以前的 Rust 代码做得更快，所以我认为一些学习(和博客)正在深入人心！我自己也很惊讶，我也把这个解决方案贴到了 exercisem 上。想听听导师对这种方法有什么看法(无双关之意！).现在，我只需要记住继续使用匹配，特征，冲击，和其他生锈的电源！

## 更新时间:2019-07-17 13:30

哇，我刚刚从健身教练那里得到了一些很棒的建议！你可以坐在`(expression, expression, ...)`上`match`，看看这个吧！

```
impl HasQuality for str {
    fn has_quality(&self) -> Quality {
        let message = self.trim();
        let question = message.ends_with('?');
        let any_alpha = message.chars().any(char::is\_alphabetic);
        let is_yelling = message.to_ascii_uppercase() == message;
        let is_empty = message.len() == 0;

        match (is_empty, any_alpha && is_yelling, question) {
            (true, _, _) => Quality::Empty,
            (false, true, true) => Quality::YellQuestion,
            (false, true, false) => Quality::YellStatement,
            (false, false, true) => Quality::AskQuestion,
            _ => Quality::Statement,
        }
    }
}; 
```

帖子[使用 Match-Bob 重温](https://rust.graystorm.com/2019/07/17/using-match-bob-revisited/)最早出现在[学 Rust](https://rust.graystorm.com) 上。