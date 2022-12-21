# 回调，特征对象和相关类型，天啊！

> 原文：<https://dev.to/deciduously/callbacks-trait-objects-and-associated-types-oh-my-2o2i>

上周，当我为我的`Widget` UI 构造块特性设计 DSL 时，我承诺要深入 Rust 程序宏。我学到的是永远不要承诺这样的事情，因为这周我甚至没有碰过这方面。你可能想至少再坐一周。

相反，我的`Widget`现在可以点击了！一个不能点击的`Button`是非常无用的，所以我想在尝试任何新奇的东西之前，我应该让这个系统正常工作。

## 回调

为了保持渲染与游戏逻辑的分离，我需要这个库的用户能够随意定义可点击的功能。我选定的解决方案有点不稳定——要创建一个可点击的小部件，需要提供一个返回动作类型的回调函数。当一个点击被注册时，网格会遍历它的子网格，查看点击的确切位置。如果找到匹配，该动作将通过连续的`handle_click`调用冒泡，直到它上面的某个父部件决定处理该动作。

这提供了相当高的灵活性——应用程序中的任何小部件都可以选择处理消息。这允许您使用容器小部件，这些容器小部件可以将它们的子部件作为一个组来处理，并且只在需要时传递全局状态更改所必需的内容。

第一个问题是以一种可克隆且易于存储在结构中的方式表示这个回调。我最终使用了一个 [`Rc`](https://doc.rust-lang.org/std/rc/struct.Rc.html) ，或者引用计数的智能指针:

```
pub struct Callback<T> {
    f: Rc<dyn Fn() -> T>,
}

impl<T> Callback<T> {
    /// Call this callback
    pub fn call(&self) -> T {
        (self.f)()
    }
}

impl<T> Clone for Callback<T> {
    fn clone(&self) -> Self {
        Self {
            f: Rc::clone(&self.f),
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要构造它们，您可以在闭包上调用`Callback::from()`，如演示应用程序中使用的:

```
let button = Button::new(
    // Display name
    &format!("{:?}", self.value),
    // Optional size, if None will calculate based on display name
    Some((VALUES.die_dimension, VALUES.die_dimension).into()),
    // Border color
    Color::from_str("black").unwrap(),
    // Click action
    Some(Callback::from(move || -> FiveDiceMessage {
        FiveDiceMessage::HoldDie(id)
    })),
); 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，我还没有想出如何不要求返回类型有一个`'static`生存期:

```
impl<T, F: Fn() -> T + 'static> From<F> for Callback<T> {
    fn from(func: F) -> Self {
        Self { f: Rc::new(func) }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是引导我走向动作缩减器类型的东西的一部分——动作本身可以只是普通的数据，就像例子一样！您为您的消息定义了一个枚举:

```
#[derive(Debug, Clone, Copy)]
pub enum FiveDiceMessage {
    HoldDie(usize),
    RollDice,
    StartOver,
} 
```

Enter fullscreen mode Exit fullscreen mode

然后用减速器来处理它们:

```
impl Game {
    // ..

    /// Handle all incoming messages
    fn reducer(&mut self, msg: FiveDiceMessage) {
        use FiveDiceMessage::*;
        match msg {
            HoldDie(idx) => self.hold_die(idx),
            RollDice => self.roll_dice(),
            StartOver => self.reset(),
        }
    }

    // ..
} 
```

Enter fullscreen mode Exit fullscreen mode

它不是一个真正的“减速器”，我们在原地突变而不是使用纯函数，但它是一个类似的模式。

理想情况下，最好能够接受更通用的回调，而不是将用户锁定在这种模式中。不过，我现在对此很满意。然而，将这个整合到我的`Widget`定义中有点棘手。在前一篇文章中，我给出了我正在处理的特征定义:

```
/// Trait representing things that can be drawn to the canvas
pub trait Drawable {
    /// Draw this game element with the given top left corner
    /// Only ever called once mounted.  Returns the bottom right corner of what was painted
    fn draw_at(&self, top_left: Point, w: WindowPtr) -> Result<Point>;
    /// Get the Region of the bounding box of this drawable
    fn get_region(&self, top_left: Point, w: WindowPtr) -> Result<Region>;
}

/// Trait representing sets of 0 or more Drawables
/// Each one can have variable number rows and elements in each row
pub trait Widget {
    /// Get the total of all regions of this widget
    fn get_region(&self, top_left: Point, w: WindowPtr) -> Result<Region>;
    /// Make this object into a Widget.  Takes an optional callback
    fn mount_widget(&self) -> MountedWidget;
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要在`Widget`中添加一个方法，该方法检测点击，并且无论回调返回什么都冒泡:

```
pub trait Widget {
    // ..
    /// Handle a click in this region
    fn handle_click(
        &mut self,
        top_left: Point,
        click: Point,
        w: WindowPtr,
    ) -> Result<Option<???>>;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果传递的点击落在这个小部件中，该方法需要返回从这些存储的回调中输出的任何内容。事情是这样的，`Callback<T>`已经变得很普通了。这就产生了一个问题，因为我们不能用这种类型参数化特征本身，就像这样:

```
pub trait Widget<T> {
    // ..
    /// Handle a click in this region
    fn handle_click(
        &mut self,
        top_left: Point,
        click: Point,
        w: WindowPtr,
    ) -> Result<Option<T>>;
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要能够构造`Widget`特征对象，并且这个`T`就是`Sized`。这不好——trait 对象是一个[动态大小的类型](https://doc.rust-lang.org/nomicon/exotic-sizes.html),而*在编译时不能有一个已知的大小。依赖于单态化的泛型方法意味着您确实拥有这些信息——鱼和熊掌不可兼得。上次我有点忽略了这一点，但它需要更多的解释。*

## 特质对象

这个库利用动态调度，允许不同的应用程序和不同的后端使用一个公共接口换入和换出。为了利用它，您实例化了下面的结构:

```
/// Top-level canvas engine object
pub struct WindowEngine {
    window: Rc<Box<dyn Window>>,
    element: Box<dyn Widget>,
} 
```

Enter fullscreen mode Exit fullscreen mode

`Widget`和`Window`特征只是定义了一些需要可用的方法——它们没有描述任何特定的类型。当我们真的把一个真实的类型放到一个`Box`中来放入这个结构时，我们完全丢失了类型信息，只保留了特征信息。一个 [vtable](https://en.wikipedia.org/wiki/Virtual_method_table) 被分配给指向特征指定的每个方法的指针，并且指向它的指针实际上*也*包含这个 vtable 指针来完成运行代码所需的信息。然而，这意味着我们不能在指针后面使用单态化的泛型类型，因为我们甚至不知道我们有什么类型。这都是通过运行时反射经由这些 vtable 指针来处理的，不能使用其他任何东西。这是一件好事，它让我们可以定义所有不同形状和大小的`Widget`(就内存而言)并完全相同地使用它们。这就是为什么`Widget<T>`如此成问题的原因——它需要知道编译时使用的所有类型，而我们显然不知道。

## 关联类型

幸运的是，有一个简单的、符合人体工程学的解决方案。不用参数化特征，您可以将一个类型作为特征定义的一部分进行关联:

```
pub trait Widget {
    type MSG; // Associated message type

    /// Handle a click in this region
    fn handle_click(
        &mut self,
        top_left: Point,
        click: Point,
        w: WindowPtr,
    ) -> Result<Option<Self::MSG>>;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以参数化实例化的结构，而不需要`Widget`定义本身携带任何行李:

```
pub struct WindowEngine<T: 'static> {
    window: Rc<Box<dyn Window>>,
    element: Box<dyn Widget<MSG = T>>,
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当创建 vtable 时，它仍然拥有您的特定应用程序的状态管理所需的所有信息，而根本不会将您的类型限制在`'static`之外。WindowEngine 本身用那个类型变得单态化，即`WindowEngine<FiveDiceMessage>`，所以这个`WindowEngine`包含的所有`Widgets`将使用相同的类型。

当你写这个类型的时候，你只需要在应用级别填写它:

```
impl Widget for Die {
    type MSG = FiveDiceMessage; // Specify associated type
    fn mount_widget(&self) -> MountedWidget<Self::MSG> {
         // ..
    }
    fn get_region(&self, top_left: Point, w: WindowPtr) -> WindowResult<Region> {
        // ..
    }
    fn handle_click(
        &mut self,
        top_left: Point,
        click: Point,
        w: WindowPtr,
    ) -> WindowResult<Option<Self::MSG>> {
        // ..
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

关联类型是我从使用一些标准库特征中了解到的一个特性。例如， [std::str::FromStr](https://doc.rust-lang.org/std/str/trait.FromStr.html) 是否指定了要使用的错误类型:

```
impl FromStr for Color {
    type Err = WindowError; // Associated Err type

    fn from_str(s: &str) -> std::result::Result<Self, Self::Err> {
        match s {
            "black" => Ok(Color::new(0, 0, 0)),
            "red" => Ok(Color::new(255, 0, 0)),
            "blue" => Ok(Color::new(0, 0, 255)),
            "green" => Ok(Color::new(0, 255, 0)),
            // ..
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我从来没有想过我需要用其中的一个来写我自己的特征，直到我倒过来。事情是这样的。

### 挥之不去的担忧——phantom data？

有一点我觉得不太舒服。我有一个通用的`Text`窗口小部件，设计来只是在画布上扑通一声弹出一个字符串，这是不可点击的。它的`handle_click`方法不返回任何东西，所以我在`Widget` impl:
中使用了`None`变量

```
impl<T: 'static> Widget for Text<T> {
    type MSG = T;
    // ..
    fn handle_click(&mut self, _: Point, _: Point, _: WindowPtr) -> Result<Option<Self::MSG>> {
        Ok(None)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这仍然要求我们用这个特定的小部件树的消息类型来参数化`Text`，即使它从未被使用过，因为返回类型仍然包含`Some(T)`变量的类型。这就是它停止对我大喊大叫的原因:

```
/// A widget that just draws some text
pub struct Text<T> {
    phantom: std::marker::PhantomData<T>,
    text: String,
}

impl<T> Text<T> {
    pub fn new(s: &str) -> Self {
        Self {
            phantom: std::marker::PhantomData,
            text: s.into(),
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

根据[文档](https://doc.rust-lang.org/beta/std/marker/struct.PhantomData.html)，`PhantomData`是一个零大小的类型，它只是“告诉编译器你的类型好像存储了一个 T 类型的值，尽管它并不真的存储”这听起来像是我正在做的事情，但是我不知道这是否是我应该尝试重构的一个组件，或者处理这种情况的正确方法。

## 天啊！

抛开疑问和疑虑，一切都按计划进行。也许，我们会在某个时候使用那些程序宏。

*🇸🇮·扬科·菲利- @specialdaddy 在 Unsplash 上拍摄的照片*