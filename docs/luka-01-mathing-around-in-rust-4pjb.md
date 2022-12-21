# Luka 01 -在铁锈中摸索

> 原文：<https://dev.to/teckert/luka-01-mathing-around-in-rust-4pjb>

> 本文是使用 Rust/Wasm、Vue 和 TypeScript 开发逆向波兰符号计算器网络应用程序系列的一部分。你可以在 [GitHub Repo](https://github.com/t-eckert/luka) 上阅读更多信息并关注发展。

## 分叉

随着部署管道从 [Luka 00](https://dev.to/teckert/luka-00-intent-and-deployment-29f0) 开始建立，任何对`master`分支的推送都将启动一个新的构建/部署实例并更新实时网站。太好了！但我也不想打破它。

这里的解决方案是创建一个新的`git`分支，在这种情况下，我称它为`dev`。我可以将代码推送到这个分支，而不会触发管道。更进一步，我将从`dev`创建一个名为`add-rust-four-functions`的分支，并使用该分支为计算器创建前四个函数:+、-、*、/。

然后，当这项工作完成后，我将向`dev`发出一个拉请求，然后向`master`发出一个拉请求。我将在每个拉请求中审查我自己的代码，通常会有其他人批准代码审查。在某些方面看起来很傻，可能对一些开发人员来说很陌生，但是我喜欢这个模型，因为它在生产中强制了一些合理的谨慎。

所有工作都将遵循这种分支模式:

[![Branching pattern for this project. At the start, a single branch called `master` exists. Then, a branch from that is created called `dev`. From that branch, a new branch is created called `feature0`. On this branch several commits are made. That branch is merged back into `dev`. The `dev` branch is merged back into `master`.](img/ddde4a57b6ef246265649956125bc105.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jRoKT-k1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8c7ja06osupowyxtg0o.png)

> 这是一个很好的时机来指出，我偷了很多快速图像的调色板，就像上面来自[flatuicolors.com](https://flatuicolors.com/)的那个。他们已经做了艰苦的工作来寻找哪些颜色搭配得好。

## 对算术生疏

现在我可以写一些代码了！在`add-rust-four-functions`上，我用`cargo new calculator --lib`在 Rust 中创建了一个库。现在，我将称这个库为`calculator`,因为它将完成项目的所有计算。

我正在考虑使用的模型——我很确定会有效——是让一个`stack`对象具有改变`stack`的状态并返回其整个状态的方法。如果我在 Rust 中编写整个项目，我不会使用这个模型，但是我喜欢只有这个“功能性”接口的想法，这样 TypeScript 只需要直接查询一次`stack`的状态(当它被初始化时)。

如果以后我发现这不是合适的型号，我会把它换掉。

由 Cargo 创建的默认`lib.rs`有一个单独的测试:

```
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我暂时保留这个测试模块，但是在它之上开始创建`stack`。当我写的时候，我只是让这个文档和`lib.rs`在 VS 代码中打开，然后我来回翻动。我觉得有些害怕称之为“堆栈”。堆栈是计算机科学的一个已知的部分，我可能会建议这个`struct`产生的物体的特征来创造一个错误的印象。

记住这一点，我实际上要把它叫做`State`，并让它包含一个叫做`stack`的对象。结构是

```
/// # State
/// 
/// Contains every value on the reverse polish notation calculator stack.
pub struct State {
    /// State handling for the "stack" uses a Vec that is treated as a stack.
    pub stack: Vec<f64>,
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢 Rust 的一点是能够在 Markdown 中记录代码，并从中生成文档。

首先要实现的是一个`new`函数。这是 Rust 中的一个常见范例，第一个特征(思考方法)是一个名为`new`的函数，它返回一个`struct`的实例。这里，我用堆栈中的四个零初始化对象。我不完全确定这是不是我最终要做的事情，但这是一个开始。

```
impl State {
    /// Creates a new instance of `State` with a stack of 4 zeros.
    pub fn new() -> State {
        State {
            stack: vec![0.0, 0.0, 0.0, 0.0],
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您不熟悉 Rust，值得注意的是，如果最后一行不以分号结尾，函数将返回该行的值。举个例子，
> 
> ```
> fn add(a: i32, b: i32) -> i32 {
>     return a + b;
> } 
> ```
> 
> 与相同
> 
> ```
> fn add(a: i32, b: i32) -> i32 {
>     a + b
> } 
> ```

现在我要用一个叫做`it_initializes` :
的测试来替换`lib.rs`中的默认测试

```
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_initializes() {
        let _ = State::new();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

✅，测试通过了！

我对接下来的部分有点不确定。我将尝试一种“函数式”的方法，其中每个`State`实例都有消耗`State`的函数，并通过适当的修改产生一个新的`State`实例。这样做是为了让每个特征返回一个`State`对象，而不会有一堆可变的状态在周围运行。老实说，我不知道这是否是正确的选择，但我会尝试一下，看看会发生什么。

这种设计的第一个例子可以用`push`特征来看:

```
impl State {

    ...

    /// Pushes `value` to `State.stack` then creates a new instance of `State`
    /// using the appended to `stack`
    pub fn push(mut self, value: f64) -> State {
        self.stack.push(value);
        State { stack: self.stack }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我真的希望我可以追加到`stack`并在同一行创建新的`State`。如果`Vec<>.push()`返回`Vec<>`中的值，我可能会写`State { stack: self.stack.push(value) }`，但是 Rust 的设计者很聪明。我认为他们没有这样做是有原因的。

新特质呼唤新考验:

```
#[cfg(test)]
mod tests {
    use super::*;

    ...

    #[test]
    fn it_pushes() {
        // Given
        let expected_stack = vec![3.14159];
        let pushed_value = 3.14159;

        // When
        let state = State { stack: vec![] };
        let state_prime = state.push(pushed_value);

        // Then
        assert_eq!(expected_stack, state_prime.stack);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

✅，测试通过了！

在这里，我使用了我非常喜欢的“给定，何时，然后”测试模型。马丁·福勒在这里做了很好的解释。

对于四个函数+、-、*、/。我将有一个名为`operate`的函数。它对`stack`中的最后两个值执行传递给它的操作。我希望 Wasm 将一个字符串从 TypeScript 传递给 Rust，我将让 Rust 用一个匹配语句将它解释成一个操作。

如果这个项目都是 Rust，也许我会让这个特征接受一个函数并使用它，但是我现在就满足于这个。如果 trait 无法将字符串与函数匹配，它将返回之前的状态。像这样悄无声息地失败不是我通常的做法，但我认为这对于以某种意想不到的方式崩溃可能更糟糕的 UI 来说是有意义的。如果铁锈不能理解它被要求做的事情，它只会耸耸肩，把它知道的最后一件事还回去。

```
impl State {

    ...

    /// Perform the operation passed as string on the last two values in the `stack`. 
    /// Expects a string matching one of the following: `+`, `-`, `*`, `/`.
    /// If a string is passed that doesn't match, the function will
    /// return the `State` unchanged.
    ///
    /// > The trait takes a `&str` to process operations from TypeScript,
    /// through Wasm, by passing operations as a TypeScript `string`.
    ///
    pub fn operate(mut self, operation: &str) -> State {
        // Remove the last two values from `stack`
        let z = self.stack.pop().unwrap();
        let y = self.stack.pop().unwrap();

        // Return a `Vec<f64>` with the matching operation performed.
        let mut stack_tail = match operation {
            "+" => vec![y + z],
            "-" => vec![y - z],
            "*" => vec![y * z],
            "/" => vec![y / z],
            _ => vec![y, z],
        };
        // Return a new instance of `State` with `stack_tail` appended.
        self.stack.append(&mut stack_tail);
        State { stack: self.stack }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数有五条路径。我需要编写至少五个测试。与 switch 语句一样，Match 语句会创建几个必须测试的分支。

在给定、何时、然后模型中编写函数节省了我很多时间。对于每个函数，我只需要改变`expected_stack`和`operation`的值。

```
#[cfg(test)]
mod tests {
    use super::*;

    ...

    #[test]
    fn it_adds() {
        // Given
        let expected_stack = vec![6.0];
        let operation = "+";

        // When
        let state = State {
            stack: vec![2.0, 4.0],
        };
        let state_prime = state.operate(operation);

        // Then
        assert_eq!(expected_stack, state_prime.stack);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

✅，测试通过了！

## 总结起来

现在，添加了这些函数后，我觉得可以将这个分支合并回`dev`，然后再合并回`master`。它不会改变前端的任何明显的东西，但是新的 Rust 代码会在 blob 存储中浮动。

开始提交:[70 feecdd 8 F9 a4c 645 e 5847 da 8382 DCD 58947344 e](https://github.com/t-eckert/luka/tree/70feecdd8f9a4c645e5847da8382dcd58947344e)
T3】结束提交:[f 37109 BF 4 b 7 b 3d 22 ef 0 b 54785 f 9104 f 453 d3c 8 c 4](https://github.com/t-eckert/luka/tree/f37109bf4b7b3d22ef0b54785f9104f453d3c8c4)

下一步是创建一个可以调用这些函数的基本 UI。