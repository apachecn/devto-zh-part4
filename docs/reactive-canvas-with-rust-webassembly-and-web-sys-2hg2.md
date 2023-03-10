# 带有 Rust/WebAssembly 和 web-sys 的活性画布

> 原文：<https://dev.to/deciduously/reactive-canvas-with-rust-webassembly-and-web-sys-2hg2>

# 还是我怎么学会了省心和爱宏

我已经有一段时间没有在某个深奥的堆栈中构建一个带有滑块的可调整大小的点了。第三章时间到了！我估计现在是连续剧了。

最后两个演示使用的语言将整个应用程序转换成普通的 JavaScript 来解释。这一次，我们将首先把我们的应用程序编译成 WebAssembly，然后让 JavaScript 加载它。

按照这些 dot 演示的惯例，这对于这个应用程序来说是大材小用。这一个也许尤其如此。卷起袖子，我们去找些 DOM。

## 管道

这一部分很大程度上是直接从《RustWasm》一书中抄来的。如果您计划对 Rust 和 WebAssembly 做进一步的工作，那么接下来(或者现在)直接去那里吧。您将需要一个 Rust 工具链和节点/NPM 来跟进。

首先，创建一个新的库类型的板条箱:

```
$ cargo new wasm-dot --lib
$ cd wasm-dot 
```

Enter fullscreen mode Exit fullscreen mode

我们需要添加`wasm-bindgen`。这个箱子为我们自动生成所有的 JS < - > Rust FFI 胶水，这也是 Rust 成为编写 WebAssembly 的绝佳选择的原因。打开机箱根中的`Cargo.toml`，使它看起来像这样:

```
[package]
name = "wasm-dot"
description = "Demo canvas wasm app"
license = "MIT"
version = "0.1.0"
authors = ["You <you@yourcoolsite.you>"]
edition = "2018"

[lib]
crate-type = ["cdylib"]

[dependencies]
wasm-bindgen = "0.2" 
```

Enter fullscreen mode Exit fullscreen mode

板条箱类型将产生一个动态系统库，用于加载到另一种语言。现在打开`src/lib.rs`，让它看起来像这样:

```
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern {
    // import the alert() js function
    pub fn alert(s: &str);
}

// export a Rust function that uses the imported JS function
#[wasm_bindgen]
pub fn say_hi() {
    alert("Hi from Rust/Wasm!");
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在导入 JavaScript `alert()`函数，并导出调用它的我们自己的`say_hi()` Rust 函数。这就是我们所需要做的，`wasm_bindgen`正在处理细节。这将确保两个方向都如预期的那样工作。

rustwasm 团队还提供了一个名为 [`wasm-pack`](https://github.com/rustwasm/wasm-pack) 的工具来自动化 WebAssembly 打包和与 npm 的集成。你需要用`cargo install wasm-pack`安装一次，然后你可以用它来构建你的包:

```
$ wasm-pack build
[INFO]: Checking for the Wasm target...
[INFO]: Compiling to Wasm...
    Finished release [optimized] target(s) in 0.00s
[INFO]: :-) Done in 0.05s
[INFO]: :-) Your wasm pkg is ready to publish at ./pkg. 
```

Enter fullscreen mode Exit fullscreen mode

在里面`pkg/`你会找到你需要部署的一切，准备好导入任何 npm 项目。我们现在需要的是一个可以使用它的项目！因为 rustwasm 小组想到了一切，所以有一个现成的模板——用它来创建一个新项目:

```
$ npm init wasm-app www 
```

Enter fullscreen mode Exit fullscreen mode

这个`www`文件夹现在包含了一个网页，所有的机器都被设置来加载你的 wasm 库并从`index.js` :
调用它

```
import * as wasm from "hello-wasm-pack";

wasm.greet(); 
```

Enter fullscreen mode Exit fullscreen mode

有一个存根包括在内，这样它就可以运行，但我们不想从`hello-wasm-pack`导入，我们想使用我们正在开发的应用程序。为了指向正确的方向，打开`www/package.json`并添加一个`dependencies`键，直接指向来自`wasm-pack` :
的`pkg`输出方向

```
 //  ..  "dependencies":  {  "wasm-dot":  "file:../pkg"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以把`www/index.js`指向那里:

```
import * as wasm from "wasm-dot";

wasm.say_hi(); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看它能不能做到:

```
$ npm install // because we added a dependency - us!
$ npm run start 
```

Enter fullscreen mode Exit fullscreen mode

您应该会在`localhost:8080`看到请求的警报:

[![hello wasm alert box screenshot](img/6e86182ea36d06ce87200925bb3edc3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N8qUtofj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/INNppw6.png)

万岁。现在我们可以迭代了。我建议在此时开设第二个航站楼。在一个示例中，运行`npm run start`并保持它打开，在另一个示例中，每当您对 Rust 进行更改时，就调用`wasm-pack build`。

## 布局

为了处理 JavaScript 宇宙，`wasm-bindgen`项目提供了两个重要的箱子:`web-sys`为所有 Web APIS(！！)和`js-sys`提供了所有 ECMAScript 的东西，比如`Array`和`Date`(!！).是的，他们已经做了艰苦的工作。这很酷，你不需要手动定义一个`Document.createElement` extern 或任何东西。相反，只需从`Cargo.toml` :
中的`web-sys`获取我们需要的内容

```
[dependencies]
wasm-bindgen = "0.2"

[dependencies.web-sys]
version = "0.3"
features = [
    "Attr",
    "CanvasRenderingContext2d",
    "Document",
    "Element",
    "Event",
    "EventTarget",
    "HtmlCanvasElement",
    "HtmlElement",
    "HtmlInputElement",
    "Node",
    "Text",
    "Window"
] 
```

Enter fullscreen mode Exit fullscreen mode

这是一个巨大的机箱，所以每个界面都有功能门控。你只使用你需要的东西。如果你试图调用一个函数，它告诉你它不存在，仔细检查 API 文档。它总是告诉你一个给定的方法需要哪些特性:

[![feature gate screenshot](img/6d1e604b2c59a9761a9bf43df6583b24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aDxJ-J8e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/thITZh3.png)

为了确保一切顺利，我们将自己构建一个 DOM 节点，JS 风格的，但也是 Rust 风格的。删除`src/lib.rs`中的`alert()`测试，增加:

```
#[wasm_bindgen]
pub fn run() {
    // get window/document/body
    let window = web_sys::window().expect("Could not get window");
    let document = window.document().expect("Could not get document");
    let body = document.body().expect("Could not get body");

    mount_app(&document, &body);
}

fn mount_app(document: &Document, body: &HtmlElement) {
    mount_title(&document, &body);
}

// Create a title
fn mount_title(document: &Document, body: &HtmlElement) {
    // create title element
    let title = document
        .create_element("h1")
        .expect("Could not create element");
    let title_text = document.create_text_node("DOT"); // always succeeds
    title
        .append_child(&title_text)
        .expect("Could not append child to title");

    // append to body
    body.append_child(&title)
        .expect("Could not append title to body");
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`web_sys`调用，这段代码为标题手动构建一个 DOM 节点，并将其安装到页面主体。

现在我们需要在`www/index.js` :
中调用`run()`而不是`say_hi()`

```
import * as wasm from "wasm-dot";

wasm.run(); 
```

Enter fullscreen mode Exit fullscreen mode

通过运行`wasm-pack build`并重新加载`localhost:8080`来查看它是否工作:

[![dom node screenshot](img/ac085b4e0815b8826a44254b0a57e0b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s8DW3HD5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7yiqu7f.png)

哇哦。你看到那个标题有多快多 WASM 了吗？！

不，你没有，但还是。干净利落。在继续之前，让我们解决错误处理的情况。所有这些`web-sys`调用都将返回一个`Result<T, JsValue>`。我们不打算在这个小演示中处理其他类型的错误，所以只是别名:

```
type Result<T> = std::result::Result<T, JsValue>; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以让函数返回一个`Result<()>`并开始使用`?`操作符，而不是到处使用`expect()`。重构`run()`来利用这个:

```
fn get_document() -> Result<Document> {
    let window = web_sys::window().unwrap();
    Ok(window.document().unwrap())
}

#[wasm_bindgen]
pub fn run() -> Result<()> {
    let document = get_document()?;
    let body = document.body().unwrap();

    mount_app(&document, &body)?;
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

拉出`get_document()`将有助于我们稍后定义事件监听器。不过，首先，我们需要定义我们想要的 DOM 树。下面是我们在 HTML 中的目标:

```
 <div id="rxcanvas">
    <span id="size-output"></span>
    <input id="size" type="range" min="1" max="100" step="5">
    <label for="size">- Size</label>
    <p>
      <canvas id="dot-canvas"></canvas>
    </p>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

如果您曾经通过 JavaScript 操作过 DOM，那么您就非常适合了。然而，在《Rust》中，这是如此冗长的。看看创建一个简单的`<h1>DOT</h1>`元素的函数有多大！我在上面承诺会有宏-我们开始吧。

对于门外汉来说，宏是一段代码，在对所有东西进行评估之前，它会扩展成其他生锈的代码。在 Rust 中，它们看起来像函数调用，但结尾有一个感叹号。但它们根本不是函数调用——当编译器通过你的模块时，它会将所有这些函数扩展到你(或库)定义的完整 Rust 代码中。这是一种自动生成代码的机制！

这个语法是 Rust 中你唯一会看到那个`thing { () => {} }`括号模式的地方。它有自己特殊的语法。这些参数以一个`$`为前缀，放在 parens 中，并在扩展期间复制到花括号中的 Rust 代码中，就在您的代码中。

Rust 实际上有另一种类型的宏，叫做[程序宏](https://blog.rust-lang.org/2018/12/21/Procedural-Macros-in-Rust-2018.html)，那是*更强大和神秘的*，但是现在`macro_rules!`会做得很好。

下面是一个将任意数量的属性附加到 DOM 元素的宏，作为 2 元组传递:

```
macro_rules! append_attrs {
    ($document:ident, $el:ident, $( $attr:expr ),* ) => {
        $(
            let attr = $document.create_attribute($attr.0)?;
            attr.set_value($attr.1);
            $el.set_attribute_node(&attr)?;
        )*
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个要扩展的参数都标记有一个令牌类型——`ident`将允许我们传递一个 Rust 名称，而`expr`接受任何 Rust 表达式(在本例中是一个 2 元组)。当被调用时，每一个都将使用我们传入的内容，把这块铁锈粘贴到我们的函数中。

这个宏是可变的，这意味着它可以接受可变数量的参数。`$( $name:expr ),*`语法意味着它将为给定的零个或多个参数执行这个块，为每个传递到这里的参数在花括号中粘贴一个代码副本。每一次，我们处理的参数都被命名为`$attr`。

您可以这样调用它，每个属性需要多少尾随元组参数就有多少:

```
append_attrs!(document, label, ("for", "size")); 
```

Enter fullscreen mode Exit fullscreen mode

但是我们可以做得更好——宏可以调用其他宏！通过定义更多的助手，我们可以将所有的事情简化到最低限度

```
macro_rules! append_text_child {
    ($document:ident, $el:ident, $text:expr ) => {
        let text = $document.create_text_node($text);
        $el.append_child(&text)?;
    };
}

macro_rules! create_element_attrs {
    ($document:ident, $type:expr, $( $attr:expr ),* ) => {{
        let el = $document.create_element($type)?;
        append_attrs!($document, el, $( $attr ),*);
        el}
    }
}

macro_rules! append_element_attrs {
    ($document:ident, $parent:ident, $type:expr, $( $attr:expr ),* ) => {
        let el = create_element_attrs!($document, $type, $( $attr ),* );
        $parent.append_child(&el)?;
    }
}

macro_rules! append_text_element_attrs {
    ($document:ident, $parent:ident, $type:expr, $text:expr, $( $attr:expr ),*) => {
        let el = create_element_attrs!($document, $type, $( $attr ),* );
        append_text_child!($document, el, $text);
        $parent.append_child(&el)?;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有两个“顶级”宏，`append_element_attrs`和`append_text_element_attrs`。前者将把具有给定属性的无子元素附加到所提供的父元素上，而后者将包含一个文本节点子元素。注意，要向下传递可变尾随参数，只需在花括号扩展中使用相同的语法，但省略`expr`类型:

```
let el = create_element_attrs!($document, $type, $( $attr ),* ); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用一个宏调用来替换整个`mount_title()`函数:

```
fn mount_app(document: &Document, body: &HtmlElement) -> Result<()> {
    append_text_element_attrs!(document, body, "h1", "DOT",);
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

我还添加了新的返回类型，因此我们现在在末尾返回一个简单的`Ok(())`来表示成功。这个宏展开包含了`?`操作符，现在将按预期工作！

注意`"DOT"`后面的逗号是强制的——这是这个宏接受的“零个或多个”属性。尽管我们已经避免了太多的样板文件。初始函数是编译器在构建二进制文件时看到的，我们省去了输入所有函数的麻烦。谢谢你，宏！萨克罗斯。

下面是剩下的 f#@%！^g 猫头鹰:

```
fn mount_canvas(document: &Document, parent: &Element) -> Result<()> {
    let p = create_element_attrs!(document, "p",);
    append_element_attrs!(document, p, "canvas", ("id", "dot-canvas"));
    parent.append_child(&p)?;
    Ok(())
}

fn mount_controls(document: &Document, parent: &HtmlElement) -> Result<()> {
    // containing div
    let div = create_element_attrs!(document, "div", ("id", "rxcanvas"));
    // span
    append_text_element_attrs!(
        document,
        div,
        "span",
        &format!("{}", STARTING_SIZE),
        ("id", "size-output")
    );
    // input
    append_element_attrs!(
        document,
        div,
        "input",
        ("id", "size"),
        ("type", "range"),
        ("min", "5"),
        ("max", "100"),
        ("step", "5")
    );
    // label
    append_text_element_attrs!(document, div, "label", "- Size", ("for", "size"));
    // canvas
    mount_canvas(&document, &div)?;
    parent.append_child(&div)?;
    Ok(())
}

fn mount_app(document: &Document, body: &HtmlElement) -> Result<()> {
    append_text_element_attrs!(document, body, "h1", "DOT",);
    mount_controls(&document, &body)?;
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

机敏的人会注意到对`STARTING_SIZE`的引用——将该常量添加到文件的顶部，这是页面呈现时滑块将开始的位置:

```
const STARTING_SIZE: u32 = 5; 
```

Enter fullscreen mode Exit fullscreen mode

如果您来自 JavaScript，所有的`web_sys`调用看起来都非常熟悉。如果你想要一个 Web API 函数，就试着在 [`web-sys` API 文档](https://rustwasm.github.io/wasm-bindgen/api/web_sys/)中寻找。每个列表也将方便地链接到相应的 MDN 页面！利用板条箱或编写自己的抽象来使这一过程更加顺畅是完全可能的，并且留给读者作为练习。

用`wasm-pack build`重建，如果你有`webpack-dev-server`在运行(通过`npm run start`)，你可以重新加载`localhost:8080`:

[![DOM tree screenshot](img/b51a99cf5cac5c93e4c2595368ffdad4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2mLtt1gP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/yTLnwg0.png)

好东西。

## 行动

不过，这没有任何作用。眼前连一个点都没有，更不用说可调整大小的了。下一步是在画布上画点:

```
// draw dot
fn update_canvas(document: &Document, size: u32) -> Result<()> {
    // grab canvas
    let canvas = document
        .get_element_by_id("dot-canvas")
        .unwrap()
        .dyn_into::<web_sys::HtmlCanvasElement>()?;
    // resize canvas to size * 2
    let canvas_dim = size * 2;
    canvas.set_width(canvas_dim);
    canvas.set_height(canvas_dim);
    let context = canvas
        .get_context("2d")?
        .unwrap()
        .dyn_into::<web_sys::CanvasRenderingContext2d>()?;

    // draw

    context.clear_rect(0.0, 0.0, canvas.width().into(), canvas.height().into());
    // create shape of radius 'size' around center point (size, size)
    context.begin_path();
    context.arc(
        size.into(),
        size.into(),
        size.into(),
        0.0,
        2.0 * std::f64::consts::PI,
    )?;
    context.fill();
    context.stroke();

    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你用 JavaScript 做过，这也不会太陌生。一个不熟悉的元素是那些`dyn_into`呼叫。为了让它工作，你需要在文件的顶部导入另一个文件:

```
use wasm_bindgen::JsCast; 
```

Enter fullscreen mode Exit fullscreen mode

当你用`Document::get_element_by_id(&str)`抓取一个元素时，它返回一个`Element`类型。一个普通的`Element`没有`width`或者`height`，尽管——这是一个明确的`canvas`元素。`HtmlCanvasElement`对象确实有这些字段，所以我们可以尝试用`dyn_into()`进行造型。如果我们真的抓住了正确的元素，这个剧组会成功的。现在我们可以用`set_height()`和`get_context()`这样的东西。注意，所有方法都使用 snake_case 而不是 camelCase，并且不能用`canvas.height = 10;`直接修改一个字段，必须使用一个方法:`canvas.set_height(10);`。否则，这是一个等效的 JavaScript 翻译，将画布的大小调整到具有给定半径的圆的边界框，然后绘制该圆。

酷毙了。我们还需要更新用于显示当前大小的`<span>`:

```
// update the size-output span
fn update_span(document: &Document, new_size: u32) -> Result<()> {
    let span = document.get_element_by_id("size-output").unwrap();
    span.set_text_content(Some(&format!("{}", new_size)));
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

这并不奇怪，`set_text_content`是 [`Node.textContent`的二传手](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent)。让我们捆绑这两个更新:

```
// given a new size, sets all relevant DOM elements
fn update_all() -> Result<()> {
    // get new size
    let document = get_document()?;
    let new_size = document
        .get_element_by_id("size")
        .unwrap()
        .dyn_into::<web_sys::HtmlInputElement>()?
        .value()
        .parse::<u32>()
        .expect("Could not parse slider value");
    update_canvas(&document, new_size)?;
    update_span(&document, new_size)?;
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

这将是我们滑块输入的 onChange 处理程序，在这个特殊的 FFI-interop-y `Closure` :
中调用

```
fn attach_listener(document: &Document) -> Result<()> {
    // listen for size change events

    update_all()?; // call once for initial render before any changes

    let callback = Closure::wrap(Box::new(move |_evt: web_sys::Event| {
        update_all().expect("Could not update");
    }) as Box<dyn Fn(_)>);

    document
        .get_element_by_id("size")
        .unwrap()
        .dyn_into::<web_sys::HtmlInputElement>()?
        .set_onchange(Some(callback.as_ref().unchecked_ref()));

    callback.forget();

    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

这使用了一个`web_sys::Closure`。这允许您将 Rust 定义的闭包传递给 JS，用作事件监听器回调。这确实有些奇怪，我将带你去看[这本书](https://rustwasm.github.io/wasm-bindgen/examples/closures.html)，以便更好地理解为什么会这样。那个`as_ref().unchecked_ref()`链让你从`web_sys::Closure`中提取`set_onchange`期望的`&Function`。

现在我们只需要在挂载 app 后调用这个:

```
#[wasm_bindgen]
pub fn run() -> Result<()> {
    let document = get_document()?;
    let body = document.body().unwrap();

    mount_app(&document, &body)?;
    attach_listener(&document)?;
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！重新编译，重新加载，并高兴地调整大小。Aww *yisss* 。

[![finished screenshot](img/3a23f69fa1d4f20c13f9ae61d3b42baa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w8WRO3B1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TxCIBFH.png)

完整的代码可以在这里找到[。](https://github.com/deciduously/wasm-dot)

照片由 Leo Rivas 在 Unsplash 上拍摄