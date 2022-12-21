# 构建器模式

> 原文：<https://dev.to/deciduously/the-builder-pattern-249l>

# 相亲如其 2001

我们将帮助蛋糕的约翰·麦克雷找到他略带讽刺的，奇怪的具体的梦想的女人。他是个特别的人:

[https://www.youtube.com/embed/X5KmB8Laemg](https://www.youtube.com/embed/X5KmB8Laemg)

是的，你和我都这么认为:这听起来像是 Rust 编译器的工作。这支乐队确实走在了时代的前面。我们来建模问题:

```
/// Girl type
struct Girl {}

impl Girl {
    /// Construct a Girl
    fn new() -> Self {
        Self {}
    }
}

/// Determine whether given girl matches spec
fn is_dream_girl(girl: &Girl) -> bool {
    // we don't know anything about spec yet, so odds are no
    false
}

fn main() {
    let girl = Girl::new();
    println!("Match: {}", is_dream_girl(&girl));
} 
```

Enter fullscreen mode Exit fullscreen mode

用`cargo run`运行这个命令会产生预期的输出:`Match: false`。

那么，我们具体在找什么？幸运的是，我们的男人从偏好开始，在第一行，他告诉我们他想要“一个像钻石一样有头脑的女孩”。让我们添加一个要测试的成员字段:

```
#[derive(Clone, Copy, PartialEq)]
enum Mind {
    Computer,
    Diamond,
    Garden,
    Scalpel,
    Sieve,
    Unknown,
}

struct Girl {
    mind: Mind,
}

impl Girl {
    fn new(mind: Mind) -> Self {
        Self { mind }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，测试函数可以检查所请求的变量:

```
fn is_dream_girl(girl: &Girl) -> bool {
    girl.mind == Mind::Diamond
}

fn main() {
    let girl = Girl::new(Mind::Diamond);
    println!("Match: {}", is_dream_girl(&girl));
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们在传入这个`Girl`时得到`Match: true`。等等，我们还有一些标准。接下来，我们需要“一个知道什么是最好的女孩”。这很简单——要么她做，要么她不做:

```
struct Girl {
    mind: Mind,
    knows_best: bool,
}

impl Girl {
    fn new(mind: Mind, knows_best: bool) -> Self {
        Self { mind, knows_best }
    }
}

fn is_dream_girl(girl: &Girl) -> bool {
    girl.mind == Mind::Diamond && girl.knows_best
} 
```

Enter fullscreen mode Exit fullscreen mode

添加到参数列表:

```
fn main() {
    let girl = Girl::new(Mind::Diamond, true);
    println!("Match: {}", is_dream_girl(&girl));
} 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。现在我们需要“像香烟一样燃烧的鞋子和眼睛”。听起来我们需要关联几对字符串:

```
type Attribute = (String, String);

/// Girl type
struct Girl {
    items: Vec<Attribute>,
    mind: Mind,
    knows_best: bool,
} 
```

Enter fullscreen mode Exit fullscreen mode

属性将是一个类似于`("shoes", "cut")`的元组。我们可以在构造函数中要求鞋子和眼睛属性:

```
impl Girl {
    fn new(mind: Mind, knows_best: bool, shoes: &str, eyes: &str) -> Self {
        let mut ret = Self {
            items: Vec::new(),
            mind,
            knows_best,
        };
        ret.push_item("shoes", shoes);
        ret.push_item("eyes", eyes);
        ret
    }

    fn push_item(&mut self, item_name: &str, attribute: &str) {
        self.items.push((item_name.into(), attribute.into()));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们只是检查一下这些商品，以确保我们得到了我们想要的东西

```
fn is_dream_girl(girl: &Girl) -> bool {
    let mut found_shoes = false;
    let mut found_eyes = false;
    for item in &girl.items {
        if item.0 == "shoes" && item.1 == "cut" {
            found_shoes = true;
        } else if item.0 == "eyes" && item.1 == "burn like cigarettes" {
            found_eyes = true;
        }
    }
    girl.mind == Mind::Diamond && girl.knows_best && found_shoes && found_eyes
} 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们只需要用新属性
构造`Girl`

```
fn main() {
    let girl = Girl::new(Mind::Diamond, true, "cut", "burn like cigarettes");
    println!("Match: {}", is_dream_girl(&girl));
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧。坚持住。你看到这里的问题了吗？让我们向前看...

```
I want a girl with the right allocations
Who's fast and thorough
And sharp as a tack
She's playing with her jewelry
She's putting up her hair
She's touring the facility
And picking up slack
... 
```

Enter fullscreen mode Exit fullscreen mode

它只是从那里继续！这个`Girl`构造函数已经失控了，我们只是勉强完成了第一节。如果约翰改变主意了怎么办？他可能会决定一些不重要的事情，或者增加一个新的标准。这段代码不能像那样修改，每个调用点都依赖于这个精确的参数列表，它是按照这个精确的顺序给出的，但是人们不会那样工作。可能有各种各样的变化。

## 图样

让我们利用构建器模式重新实现这个程序。当第一次构造一个`Girl`时，我们只想从一些合理的缺省值开始:

```
struct Girl {
    items: Vec<Attribute>,
    mind: Mind,
    knows_best: bool,
}

impl Girl {
    fn new() -> Self {
        Self::default()
    }
}

impl Default for Girl {
    fn default() -> Self {
        Self { mind: Mind::Unknown, knows_best: false, items: Vec::new() }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

其他一切都是一张白纸。这样我们就可以使用不带参数的`Girl::new()`来获得一个起点。要添加更多，我们可以定义方法:

```
impl Girl {
    // ..

    fn set_mind(&mut self, mind: Mind) -> &mut Self {
        self.mind = mind;
        self
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法接受一个可变引用并返回一个，所以我们可以先构造，后调整:

```
fn main() {
    let mut girl = Girl::new();
    girl.set_mind(Mind::Diamond);
    println!("Match: {}", is_dream_girl(&girl));
} 
```

Enter fullscreen mode Exit fullscreen mode

剩下的我们来补充:

```
impl Girl {
    fn push_item(&mut self, item_name: &str, attribute: &str) {
        self.items.push((item_name.into(), attribute.into()));
    }

    fn toggle_knows_best(&mut self) -> &mut Self {
        self.knows_best = !self.knows_best;
        self
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以一次添加一个，不管我们在构造时知道什么:

```
fn main() {
    let mut girl = Girl::new();
    girl.set_mind(Mind::Diamond);
    girl.toggle_knows_best();
    girl.push_item("shoes", "cut");
    girl.push_item("eyes", "burn like cigarettes");
    println!("Match: {}", is_dream_girl(&girl));
} 
```

Enter fullscreen mode Exit fullscreen mode

随着规范的增长和发展，这变得更加容易。

更复杂的场景可能需要你使用单独的类型，比如 GirlBuilder，并且在每一步都取得所有权。这将允许您在一行代码中完成所有这些:`let girl = GirlBuilder::new().set_mind(Mind::Diamond).toggle_knows_best();`这确实限制了您的配置选项，例如，如果您想要在一个`if`表达式中有条件地调用一些构建器方法。如果可能的话，这里的非拥有模式更灵活。

希望我们能帮助麦克雷先生在这么长时间后最终安定下来。

## 挑战

准备好五年后这首歌成为“老歌”。