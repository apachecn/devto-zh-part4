# 用 Rust 和 WebAssembly 创建 Dev 的离线页面🦄💡✨

> 原文：<https://dev.to/sendilkumarn/create-dev-s-offline-page-with-rust-and-webassembly-21gn>

Dev 的[离线页面](https://dev.to/offline)很好玩。我们能用 Rust 和 WebAssembly 做到吗？

答案是肯定的。让我们做吧。

首先，我们将使用 Webpack 创建一个简单的 Rust 和 WebAssembly 应用程序。

```
npm init rust-webpack dev-offline-canvas 
```

Enter fullscreen mode Exit fullscreen mode

Rust 和 WebAssembly 生态系统提供了在 Web APIs 上提供必要绑定的`web_sys`。点击查看[。](https://rustwasm.github.io/wasm-bindgen/api/web_sys/)

示例应用程序已经有了`web_sys`依赖项。`web_sys`箱包括所有可用的 WebAPI 绑定。

> 包含所有 WebAPI 绑定将增加绑定文件的大小。只包含我们需要的 API 是非常重要的。

我们将删除现有特征

```
features = [
    'console'
] 
```

Enter fullscreen mode Exit fullscreen mode

并替换为以下内容:

```
features = [
  'CanvasRenderingContext2d',
  'CssStyleDeclaration',
  'Document',
  'Element',
  'EventTarget',
  'HtmlCanvasElement',
  'HtmlElement',
  'MouseEvent',
  'Node',
  'Window',
] 
```

Enter fullscreen mode Exit fullscreen mode

上面的特性列表是我们将在这个例子中使用的全部特性。

### 让我们写一些铁锈

打开`src/lib.rs`。

用以下内容替换`start()`功能:

```
 #[wasm_bindgen(start)]
pub fn start() -> Result<(), JsValue> {

   Ok()
} 
```

Enter fullscreen mode Exit fullscreen mode

WebAssembly 模块一实例化，`#[wasm_bindgen(start)]`就调用这个函数。点击查看更多关于规格[中的启动功能。](https://github.com/WebAssembly/design/blob/master/Modules.md#module-start-function)

我们将得到铁锈中的`window`物体。

```
 let window = web_sys::window().expect("should have a window in this context"); 
```

Enter fullscreen mode Exit fullscreen mode

然后从`window`对象获取文档。

```
 let document = window.document().expect("window should have a document"); 
```

Enter fullscreen mode Exit fullscreen mode

创建一个画布元素并将其附加到文档中。

```
 let canvas = document
         .create_element("canvas")?
         .dyn_into::<web_sys::HtmlCanvasElement>()?;

    document.body().unwrap().append_child(&canvas)?; 
```

Enter fullscreen mode Exit fullscreen mode

设置画布元素的宽度、高度和边框。

```
 canvas.set_width(640);
    canvas.set_height(480);
    canvas.style().set_property("border", "solid")?; 
```

Enter fullscreen mode Exit fullscreen mode

在 Rust 中，一旦执行脱离上下文或者当方法返回任何值时，内存就会被丢弃。但是在 JavaScript 中，只要页面启动并运行，`window`、`document`就是活动的。

因此，为内存创建一个引用并使其静态存在直到程序完全关闭是很重要的。

获取画布的渲染上下文，并创建一个包装器来保存它的生命周期。

`RC`代表`Reference Counted`。

Rc 类型提供在堆中分配的 T 类型值的共享所有权。在 Rc 上调用 clone 会在堆中产生一个指向相同值的新指针。当指向给定值的最后一个 Rc 指针被销毁时，所指向的值也被销毁。- [RC 文档](https://doc.rust-lang.org/std/rc/struct.Rc.html)

这个引用被克隆并用于回调方法。

```
let context = canvas
        .get_context("2d")?
        .unwrap()
        .dyn_into::<web_sys::CanvasRenderingContext2d>()?;

let context = Rc::new(context); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们要捕捉鼠标事件。我们将创建一个名为`pressed`的布尔变量。`pressed`将保存`mouse click`的当前值。

```
let pressed = Rc::new(Cell::new(false)); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要为`mouseDown` | `mouseUp` | `mouseMove`创建一个闭包(回调函数)。

```
 { mouse_down(&context, &pressed, &canvas); }
    { mouse_move(&context, &pressed, &canvas); }
    { mouse_up(&context, &pressed, &canvas); } 
```

Enter fullscreen mode Exit fullscreen mode

我们将把在这些事件中需要做的动作定义为独立的功能。这些函数获取画布元素的上下文和按下的状态。

```
 fn mouse_up(context: &std::rc::Rc<web_sys::CanvasRenderingContext2d>, pressed: &std::rc::Rc<std::cell::Cell<bool>>, canvas: &web_sys::HtmlCanvasElement) {
    let context = context.clone();
    let pressed = pressed.clone();
    let closure = Closure::wrap(Box::new(move |event: web_sys::MouseEvent| {
        pressed.set(false);
        context.line_to(event.offset_x() as f64, event.offset_y() as f64);
        context.stroke();
    }) as Box<dyn FnMut(_)>);
    canvas.add_event_listener_with_callback("mouseup", closure.as_ref().unchecked_ref()).unwrap();
    closure.forget();
}

fn mouse_move(context: &std::rc::Rc<web_sys::CanvasRenderingContext2d>, pressed: &std::rc::Rc<std::cell::Cell<bool>>, canvas: &web_sys::HtmlCanvasElement){
    let context = context.clone();
    let pressed = pressed.clone();
    let closure = Closure::wrap(Box::new(move |event: web_sys::MouseEvent| {
        if pressed.get() {
            context.line_to(event.offset_x() as f64, event.offset_y() as f64);
            context.stroke();
            context.begin_path();
            context.move_to(event.offset_x() as f64, event.offset_y() as f64);
        }
    }) as Box<dyn FnMut(_)>);
    canvas.add_event_listener_with_callback("mousemove", closure.as_ref().unchecked_ref()).unwrap();
    closure.forget();
}

fn mouse_down(context: &std::rc::Rc<web_sys::CanvasRenderingContext2d>, pressed: &std::rc::Rc<std::cell::Cell<bool>>, canvas: &web_sys::HtmlCanvasElement){
    let context = context.clone();
    let pressed = pressed.clone();

    let closure = Closure::wrap(Box::new(move |event: web_sys::MouseEvent| {
        context.begin_path();
        context.set_line_width(5.0);
        context.move_to(event.offset_x() as f64, event.offset_y() as f64);
        pressed.set(true);
    }) as Box<dyn FnMut(_)>);
    canvas.add_event_listener_with_callback("mousedown", closure.as_ref().unchecked_ref()).unwrap();
    closure.forget();
} 
```

Enter fullscreen mode Exit fullscreen mode

它们与你的 API 看起来非常相似，但是它们是用 Rust 编写的。

现在我们都准备好了。我们可以运行应用程序并在画布内进行绘制。🎉 🎉 🎉

但是我们没有任何颜色。

### 让我们添加一些颜色。

添加颜色样本。创建一个 div 列表，并将它们用作选择器。

定义我们需要添加到`start`程序中的颜色列表。

```
#[wasm_bindgen(start)]
pub fn start() -> Result<(), JsValue> {
    // ....... Some content
    let colors = vec!["#F4908E", "#F2F097", "#88B0DC", "#F7B5D1", "#53C4AF", "#FDE38C"];

   Ok()
} 
```

Enter fullscreen mode Exit fullscreen mode

然后遍历列表，为所有颜色创建一个 div，并将其附加到文档中。为每个 div 添加一个`onClick`处理程序来改变颜色。

```
 for c in colors {
        let div = document
            .create_element("div")?
            .dyn_into::<web_sys::HtmlElement>()?;
        div.set_class_name("color");
        {
            click(&context, &div, c.clone());  // On Click Closure.
        }

        div.style().set_property("background-color", c);
        let div = div.dyn_into::<web_sys::Node>()?;
        document.body().unwrap().append_child(&div)?;
    } 
```

Enter fullscreen mode Exit fullscreen mode

点击操作如下:

```
fn click(context: &std::rc::Rc<web_sys::CanvasRenderingContext2d>, div: &web_sys::HtmlElement, c: &str) {
    let context = context.clone();
    let c = JsValue::from(String::from(c));
    let closure = Closure::wrap(Box::new(move || {
        context.set_stroke_style(&c);            
    }) as Box<dyn FnMut()>);

    div.set_onclick(Some(closure.as_ref().unchecked_ref()));
    closure.forget();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在稍微美化一下。打开`static/index.html`并为颜色分区添加样式。

```
 <style>
       .color {
            display: inline-block;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            cursor: pointer;
            margin: 10px;
       }
 </style> 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们创建了应用程序。🎉

点击查看可用的演示应用[。](https://github.com/sendilkumarn/draw-page)

我希望这能给你一个动力，开始你的 WebAssembly 之旅。如果您有任何问题/建议/觉得我错过了什么，请随时添加评论。

你可以在推特上关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel)

## [阿里·斯皮特](/aspittel)

[Passionate about education, Python, JavaScript, and code art.](/aspittel)

对于[篇](https://dev.to/aspittel/how-to-create-the-drawing-interaction-on-dev-s-offline-page-1mbe)。

点击这里查看我更多的 WebAssembly 文章。