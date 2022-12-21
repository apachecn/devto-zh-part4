# Using Rust and WebAssembly to Build Off-line Drawing Pages

> 原文：<https://dev.to/suhanyujie/rust-webassembly-2pb3>

# Using Rust and WebAssembly to Build Offline Drawing Pages

> *   原文地址: [https://dev。to/sendilkumarn/create-dev-s-offline-page-with-rust-and-web assembly-21gn](https://dev.to/sendilkumarn/create-dev-s-offline-page-with-rust-and-webassembly-21gn)
> *   Original:
> *   Author: [Kumar, Sendil n](https://dev.to/sendilkumarn)
> *   译文出自:[https://github.com/suhanyujie](https://github.com/suhanyujie)
> *   Permanent link to this article: (default)
> *   Translator: [Su Hanyu](https://github.com/suhanyujie)
> *   Please point out any improper translation, thank you!
> 
> [离线画图页](https://dev.to/offline)

The [offline drawing page](https://dev.to/offline) of Dev website is very interesting. Can we implement it with Rust and WebAssembly?

The answer is yes. Let's make it happen now.

First, we created a simple application based on Rust and WebAssembly through Webpack.

```
npm init rust-webpack dev-offline-canvas 
```

Rust and WebAssembly ecology provide `web_sys`, which provides many required bindings on the Web API. It can be checked out from [here]((https://rustwasm.github.io/wasm-bindgen/api/web_sys/)) .

The example has introduced `web_sys` dependency. `web_sys` Crate contains all available WebAPI bindings.

> If all WebAPI bindings are introduced, the size of the binding file will be increased. It is important to introduce the necessary API on demand.

We remove the existing feature list (in toml file)

```
features = [
    'console'
] 
```

And use the following substitution:

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

The above list of features is some features that we will need to use in this example.

## Start writing Rust code.

Open the file `src/lib.rs`.

Replace the `start()` function in the file with the following code:

```
#[wasm_bindgen(start)]
pub fn start() -> Result<(), JsValue> {

   Ok()
} 
```

Once the WebAssembly module is instantiated, `#[wasm_bindgen(start)]` will call this function. You can view the [details](https://github.com/WebAssembly/design/blob/master/Modules.md#module-start-function) of the start function in the specification.

We will get the `window` object in Rust.

```
let window = web_sys::window().expect("should have a window in this context"); 
```

Then get the document from the `window` object.

```
let document = window.document().expect("window should have a document"); 
```

Create a Canvas element and insert it into the document.

```
let canvas = document
         .create_element("canvas")?
         .dyn_into::<web_sys::HtmlCanvasElement>()?;

document.body().unwrap().append_child(&canvas)?; 
```

Set the width, height and border of canvas elements.

```
canvas.set_width(640);
canvas.set_height(480);
canvas.style().set_property("border", "solid")?; 
```

In Rust, once you leave the current context or the function has return, the corresponding memory will be released. However, in JavaScript, `window` and `document` are active (located in the life cycle) when the page starts and runs.

Therefore, it is important to create a reference for the memory and make it static until the end of the program.

Get the context of Canvas rendering, and wrap a wrapper on its outer layer to guarantee its life cycle.

`RC` means `Reference Counted`.

Rc type provides to allocate the value of type T in the heap and share its ownership. Calling clone on Rc will generate a new pointer to the same value in the heap. When the last Rc pointer to a given value is about to be released, the value it points to will also be released. —— [RC document](https://doc.rust-lang.org/std/rc/struct.Rc.html)

This reference is clone and used in the callback method.

```
let context = canvas
        .get_context("2d")?
        .unwrap()
        .dyn_into::<web_sys::CanvasRenderingContext2d>()?;

let context = Rc::new(context); 
```

Since we are going to capture the mouse events. We will create a boolean variable called `pressed`. The `pressed` will hold the current value of `mouse click`.
Because we have to respond to the mouse event. So we will create a Boolean variable named `pressed`. `pressed` is used to save the current value of `mouse click` (mouse click).

```
let pressed = Rc::new(Cell::new(false)); 
```

Now, we need to create a closure (callback function) for `mouseDown`, `mouseUp` and `mouseMove`.

```
{ mouse_down(&context, &pressed, &canvas); }
{ mouse_move(&context, &pressed, &canvas); }
{ mouse_up(&context, &pressed, &canvas); } 
```

We will define the actions that need to be performed when these events are triggered as independent functions. These functions receive the context of canvas elements and the mouse down state as parameters.

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

They are very similar to the API of `JavaScript` you usually write, but they are written in Rust.

Now we're all set up. We can run the application and draw in the canvas.

But we haven't set the color yet.

## Add multiple colors

Add color swatches, create a div list, and use them as color selectors.

Define the color list we need in the `start` function.

```
#[wasm_bindgen(start)]
pub fn start() -> Result<(), JsValue> {
    // ....... Some content
    let colors = vec!["#F4908E", "#F2F097", "#88B0DC", "#F7B5D1", "#53C4AF", "#FDE38C"];

    Ok()
} 
```

Then traverse the list of colors, create a div for all colors, and add it to the document. For each div, you also need to add a `onClick` handler to change the drawing board color.

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

The click function is implemented as follows:

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

Now beautify it a little. Open the `static/index.html` file. Add div style to it.

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

This is our drawing board. We have already created this application.

You can check out the example application from [here](https://github.com/sendilkumarn/draw-page) .

I hope this example can inspire you to start a wonderful WebAssembly journey. If you have any questions, suggestions or feelings, please leave me a comment.

You can follow me on [T0】 Twitter 【T1].

If you like this article, please like it or leave a message for this [article](https://dev.to/aspittel/how-to-create-the-drawing-interaction-on-dev-s-offline-page-1mbe) . ❤️

You can also read my other WebAssembly articles, [click here](https://dev.to/sendilkumarn/increase-rust-and-webassembly-performance-382h) .