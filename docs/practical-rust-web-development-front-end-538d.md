# 实用 Rust Web 开发-前端

> 原文：<https://dev.to/werner/practical-rust-web-development-front-end-538d>

在这篇文章中，我将向你展示如何使用 wasm 在 Rust 中创建一个前端应用程序，我必须承认这不是一条快乐的路，有很多缺点，可能是因为 wasm 在 Rust 中还为时过早。因此，我的建议是，在生产中使用它之前应该等待，特别是在文档中，因为它有一些不太直观的地方。

## 无框架

任何有 web 开发经验的人都会尝试做的第一件事是研究任何使工作更容易的框架。有几个问题需要考虑，但是也有一些问题让我想在没有框架的情况下做，正如我在上一段中所说的，缺乏更新的文档使一切都变得困难，太多的变化和没有稳定的库，特别是对于可用的框架。

去无框架化的好处是，我可以理解我应该如何使用 wasm_bindgen，并了解它的一些缺点，如果我决定在生产中使用它，这将对我有所帮助。

如果你能设法使用一个框架，你应该，这将是一个更好的处理状态和模板的方法。

我确信制作一个框架意味着很多工作，他们背后的人也在努力工作，然而，我对他们中的大多数人都有一些问题。

Yew 是比较流行的一个，但是缺少一个路由器(集成在框架中)和使用 stdweb 这样的非官方板条箱，让我在使用之前三思。

Seed 看起来挺酷的，用 wasm_bindgen，有路由器，但是出于某种原因我还是不明白 fetch API 不行。

珀西每晚工作，我更喜欢稳定的铁锈。

所以，我决定让我的宠物项目没有框架，这没什么大不了的，但我想有更稳定的方法来生产 SPA 应用程序，遗憾的是 Rust 目前不在其中。

然而，让我们忽略这一切，说我们有足够的勇气在我们的项目中使用它。

## 基础知识

可能对你有帮助的一个提示是，你永远不要忘记你也在使用 Javascript，这意味着什么？在少数情况下，比如使用 fetch 发出 ajax 请求，您应该返回一个承诺而不是未来，然后像在 Javascript 中一样处理这个承诺，但是在 Rust 中。稍后我会给你看一个例子。

我们将从基础开始让它运行起来。

我们将在项目中添加一个 webpack.config.js 和一个 package.json 文件。

`webpack.config.js` :

```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');
const WasmPackPlugin = require("@wasm-tool/wasm-pack-plugin");

module.exports = {
    entry: './index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'index.js',
    },
    devServer: {
        historyApiFallback: true //This is important for our client Router
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: 'index.html'
        }),
        new WasmPackPlugin({
            crateDirectory: path.resolve(__dirname, ".")
        }),
        // Have this example work in Edge which doesn't ship `TextEncoder` or
        // `TextDecoder` at this time.
        new webpack.ProvidePlugin({
          TextDecoder: ['text-encoding', 'TextDecoder'],
          TextEncoder: ['text-encoding', 'TextEncoder']
        })
    ],
    mode: 'development'
}; 
```

`package.json` :

```
{  "scripts":  {  "build":  "webpack",  "serve":  "webpack-dev-server"  },  "devDependencies":  {  "@wasm-tool/wasm-pack-plugin":  "0.4.2",  "text-encoding":  "^0.7.0",  "html-webpack-plugin":  "^3.2.0",  "webpack":  "^4.29.4",  "webpack-cli":  "^3.1.1",  "webpack-dev-server":  "^3.1.0"  },  "dependencies":  {  "bootstrap":  "^4.3.1"  }  } 
```

让我们不要忘记我们的索引页面和 index.js 文件:

`index.html` :

```
<html>
  <head>
    <meta content="text/html;charset=utf-8" http-equiv="Content-Type"/>
    <link rel="stylesheet" href="node_modules/bootstrap/dist/css/bootstrap.min.css">
  </head>
  My Store
  <body>
    <div id="app"></div>
    <script src="node_modules/bootstrap/dist/js/bootstrap.min.js"></script>
  </body>
</html> 
```

`ìndex.js` :

```
const rust = import('./pkg/front_raw_mystore');

rust.catch(console.error); 
```

最后，将一个空的 lib.rs 文件添加到项目中，然后我们可以使用命令:
运行项目

```
cargo build
npm install npm run serve 
```

如果一切正常，我们可以让我们的服务器启动并运行。

## 路由器

我们还将实现我们的客户端路由器，为了实现这一点，我们需要处理历史中的状态，并对我们的 webpack 配置进行一点聚合(记住我们也在使用 Javascript)。

我们将从空载项目开始，并在`Cargo.toml` :
中添加下一个板条箱

```
[lib]
crate-type = ["cdylib"]

[dependencies]
futures = { version = "0.1.20", features = ["use_std"] }
wasm-bindgen = { version = "0.2.45", features = ["serde-serialize"]  }
js-sys = "0.3.22"
wasm-bindgen-futures = "0.3.22"
serde = { version = "1.0.80", features = ["derive"] }
serde_derive = "^1.0.59"
serde_json = "1"
console_error_panic_hook = "0.1.6"

[dependencies.web-sys]
version = "0.3.4"
features = [
  'Headers',
  'Request',
  'RequestInit',
  'RequestMode',
  'Response',
  'Window',
  'Document',
  'Element',
  'HtmlElement',
  'HtmlInputElement',
  'HtmlButtonElement',
  'HtmlFormElement',
  'HtmlCollection',
  'MouseEvent',
  'Node',
  'History',
  'Event',
  'EventTarget',
  'ErrorEvent',
  'Location',
  'console'
]

[profile.release]
debug = true 
```

每次您需要 DOM Api 中的某些东西时，可能都需要将其添加到`[dependencies.web-sys]`中。

`src/router.rs` :

```
use wasm_bindgen::prelude::*;
use web_sys::{ History, Location };

pub struct Router {
    pub history: History,
    pub location: Location
}

impl Router {
    pub fn new() -> Self {
        let window = web_sys::window().expect("no global `window` exists");
        let history = window.history().expect("no history");
        let document = window.document().expect("should have a document on window");
        let location = document.location().unwrap();

        Router { history, location }
    }

    pub fn go_to(&self, url: &str, state: &JsValue) -> Result<(), JsValue> {
        self.history.push_state_with_url(state, 
            url, Some(&format!("{}/{}", self.location.origin().unwrap(), url)))
    }
} 
```

每次用户更改 url 时，我们都需要推送状态。现在我们将添加我们的应用程序需要的所有路线，让我们添加一个标签为`components`的文件夹，并添加一个名为`routes.rs`的文件。

`src/components/routes.rs` :

```
use std::collections::HashMap;
use std::sync::Arc;
use wasm_bindgen::JsValue;
use crate::components::component::Component;
use crate::components;
use crate::app::App;

// In this struct we will have registered all our routes.
pub struct Routes(HashMap<String, Box<Component>>);

impl Routes {
    // Every time we need a new component, we register our route here.
    pub fn new(app: Arc<App>) -> Routes {
        let mut routes = Routes(HashMap::new());
        routes.0.insert("/dashboard".to_string(),
            Box::new(components::dashboard::Dashboard::new("dashboard".to_string(), app.clone())));
        routes.0.insert("/login".to_string(),
            Box::new(components::login::Login::new("login".to_string(), app.clone())));
        routes.0.insert("/register".to_string(),
            Box::new(components::register::Register::new("register".to_string(), app.clone())));
        routes.0.insert("/home".to_string(),
            Box::new(components::home::Home::new("home".to_string(), app.clone())));
        routes.0.insert("/".to_string(),
            Box::new(components::home::Home::new("home".to_string(), app.clone())));
        routes
    }

    pub fn go_to(&self, url: String, state: &JsValue) {
        self.0.get(&url).expect("Component not created").render(state);
    }

    pub fn load_components(&self, url: String, state: &JsValue ) {
        self.0.get(&url).expect("Component not created").load_components(state);
    }
} 
```

## 获取 API

我们需要一种向服务器发送 http 请求的方法，我们可以使用 Javascript Fetch API，但是记住我们使用的是 Javascript，所以我们需要用`#[wasm_bindgen]`注释每个函数并返回一个承诺。

`src/fetch.rs` :

```
use futures::Future;
use js_sys::Promise;
use wasm_bindgen::prelude::*;
use wasm_bindgen::JsCast;
use wasm_bindgen_futures::future_to_promise;
use wasm_bindgen_futures::JsFuture;
use web_sys::{Request, RequestInit, RequestMode, Response};
use serde::Serialize;

// This is the url for the server
const BASE_URL: &str = "http://localhost:8088";

#[wasm_bindgen]
pub fn fetch_request(url: &str,
                     method: &str,
                     body: Option<String>) -> Promise {
    let mut opts = RequestInit::new();
    opts.method(method);
    opts.mode(RequestMode::Cors);
    if let Some(body_string) = body {
        let js_value = JsValue::from_str(&body_string);
        opts.body(Some(&js_value));
    }

    let request = Request::new_with_str_and_init(&format!("{}/{}", BASE_URL, url), &opts).unwrap();

    request
        .headers()
        .set("Content-Type", "application/json").unwrap();

    let window = web_sys::window().ok_or_else(|| JsValue::from_str("Could not get a window object")).unwrap();
    let request_promise = 
        window
            .fetch_with_request(&request);

    let future = JsFuture::from(request_promise)
        .and_then(|resp_value| {
            assert!(resp_value.is_instance_of::<Response>());
            let resp: Response = resp_value.dyn_into()?;
            resp.json()
        })
        .and_then(|json_value: Promise| {
            JsFuture::from(json_value)
        });

    future_to_promise(future)
}

#[wasm_bindgen]
pub fn post_request(url: &str, body: String) -> Promise {
    fetch_request(url, "POST", Some(body))
}

#[wasm_bindgen]
pub fn get_request(url: &str) -> Promise  {
    fetch_request(url, "GET", None)
}

#[wasm_bindgen]
pub fn delete_request(url: &str) -> Promise {
    fetch_request(url, "DELETE", None)
} 
```

## 组件

我们将在这个博客中实现注册组件，其余的登录、主页和仪表板将在存储库中提供，我将稍后提供产品页面，但是，一旦您理解了基础知识，如果您愿意，您可以继续使用产品页面。

我们需要一个能够抽象出组件所需的大多数功能的特征。

`src/components/component.rs` :

```
use std::sync::Arc;
use wasm_bindgen::JsValue;
use web_sys::{ HtmlInputElement, Document, Element };
use wasm_bindgen::JsCast;
use serde::{Deserialize, Serialize};
use crate::app::App;

#[derive(Debug, Serialize, Deserialize)]
pub struct FlashMessage {
    pub message: String
}

// Every component should implement these methods, except for render
// that will be the same for all components. 
pub trait Component {
    fn load_components(&self, data: &JsValue) -> Result<(), JsValue>;
    fn app(&self) -> Arc<App>;
    fn url(&self) -> String;
    fn render(&self, state: &JsValue) -> Result<(), JsValue> {
        self.app().div.set_inner_html("");
        self.load_components(state)?;
        self.app().go_to(&self.url(), state)
    }
}

// I'm using a struct to reduce boilerplate creating
// inputs and other things components might need, It's a 
// way to dry your code
pub struct InputComponent(pub Arc<Document>);

impl InputComponent {
    pub fn create_input(&self, id: &str, name: &str, ttype: &str, placeholder: &str) 
        -> Result<Element, JsValue> {
            let div = self.0.create_element("div")?;
            div.set_class_name("from-group");
            let input_element = self.0.create_element("input")?;
            input_element.set_id(id);
            let input = JsCast::dyn_ref::<HtmlInputElement>(&input_element)
                .ok_or(JsValue::from_str("Error casting input"))?;
            input.set_placeholder(placeholder);
            input.set_class_name("form-control");
            input.set_name(name);
            input.set_type(ttype);
            div.append_child(input);
            Ok(div)
    }

    pub fn value_by_id(&self, id: &str) -> String {
        let element = self.0.get_element_by_id(id).expect(&format!("No {}", id));
        JsCast::dyn_ref::<HtmlInputElement>(&element).expect("Error casting input").value()
    }
} 
```

`src/components/register.rs` :

```
use std::sync::Arc;
use serde_json::json;
use wasm_bindgen::{ JsValue, JsCast };
use wasm_bindgen::closure::Closure;
use web_sys::{ HtmlButtonElement, EventTarget, ErrorEvent };
use serde::{Deserialize, Serialize};
use crate::app::App;
use crate::components::component::{ Component, InputComponent, FlashMessage };
use crate::fetch::post_request;
use crate::components;

#[derive(Debug, Serialize, Deserialize)]
pub struct RegisterUser {
    pub email: String,
    pub company: String,
    pub password: String,
    pub password_confirmation: String
}

impl RegisterUser {
    pub fn new() -> Self {
        RegisterUser {
            email: "".to_string(),
            company: "".to_string(),
            password: "".to_string(),
            password_confirmation: "".to_string()
        }
    }
}

#[derive(Clone)]
pub struct Register {
    url: String,
    app: Arc<App>
}

impl Register {
    pub fn new(url: String, app: Arc<App>) -> Self {
        Register { url, app }
    }
}

impl Component for Register {
    fn app(&self) -> Arc<App> { self.app.clone() }

    fn url(&self) -> String { self.url.clone() }

    fn load_components(&self, data: &JsValue) -> Result<(), JsValue> {

        let main_div = self.app.document.create_element("div")?;
        main_div.set_class_name("container");
        let h2_title = self.app.document.create_element("h2")?;
        h2_title.set_text_content(Some("Register an User"));

        let form = self.app.document.create_element("form")?;

        let email_div = 
            InputComponent(self.app.document.clone())
                .create_input("email", "email", "text", "Email")?;

        let company_div = 
            InputComponent(self.app.document.clone())
                .create_input("company", "company", "text", "Company")?;

        let password_div = 
            InputComponent(self.app.document.clone())
                .create_input("password", "password", "password", "Password")?;

        let password_confirmation_div = 
            InputComponent(self.app.document.clone())
                .create_input("password_confirmation", "password_confirmation", "password", "Password Confirmation")?;

        let button_element = self.app.document.create_element("button")?;
        let button = JsCast::dyn_ref::<HtmlButtonElement>(&button_element)
            .ok_or(JsValue::from_str("Error casting input"))?;
        button.set_class_name("btn btn-primary");
        button.set_text_content(Some("Send"));
        button.set_type("Submit");

        form.append_child(&email_div)?;
        form.append_child(&company_div)?;
        form.append_child(&password_div)?;
        form.append_child(&password_confirmation_div)?;
        form.append_child(&button)?;

        main_div.append_child(&h2_title)?;
        main_div.append_child(&form)?;

        let button_et: EventTarget = button_element.into();

        let document = self.app.document.clone();
        // We need to access the app property from the struct
        // inside a closure, however we need to move everything we
        // need, the best way to do that is cloning through an Arc.
        // This way the cost of cloning is reduced. 
        let app_closure = self.app.clone();
        let form_closure = Arc::new(form);
        let handler = 
            Closure::wrap(Box::new(move |event: web_sys::MouseEvent| {
                event.prevent_default();
                event.stop_propagation();
                let register_user = RegisterUser{
                    email: InputComponent(document.clone()).value_by_id("email"),
                    company: InputComponent(document.clone()).value_by_id("company"),
                    password: InputComponent(document.clone()).value_by_id("password"),
                    password_confirmation: InputComponent(document.clone()).value_by_id("password_confirmation")
                };
                let serialized_register_user = json!(register_user).to_string();
                // Here we're cloning the app again because we're
                // gonna need it in another closure.
                let app_success_closure = app_closure.clone();
                let success_response = 
                    Closure::once(move |js_value: JsValue| {
                        let message = FlashMessage { message: "User Created".to_string() };
                        components::routes::Routes::new(app_success_closure)
                            .go_to("/home".to_string(), &JsValue::from_serde(&message).unwrap());
                    });
                let error_form_closure = form_closure.clone();
                let app_error_closure = app_closure.clone();
                let error_response = 
                    Closure::once(move |js_value: JsValue| {
                        let response: &ErrorEvent = js_value.as_ref().unchecked_ref();
                        let text = response.message();
                        let alert_error = app_error_closure.document.create_element("div")
                            .expect("Creating alert not possible");
                        alert_error.set_class_name("alert alert-danger");
                        alert_error.set_text_content(Some(&text));
                        error_form_closure.append_child(&alert_error);
                    });
                post_request("register", serialized_register_user)
                    .then(&success_response)
                    .catch(&error_response);
                error_response.forget();
                success_response.forget();
            }) as Box<dyn FnMut(_)>);

        button_et.add_event_listener_with_callback("click", handler.as_ref().unchecked_ref())?;

        handler.forget();

        self.app.div.append_child(&main_div)?;

        Ok(())
    }
} 
```

正如你在前面的代码中看到的，一个有合适的模板库的框架可以节省很多工作，我只是希望我们在未来能有更好的选择或更稳定的框架。

你可以在这里看看完整的源代码[。](https://github.com/practical-rust-web-development/front_raw_mystore)

## 故障排除

### 浏览器中更好的错误

为了更好地解释正在发生的事情，你可以使用`console_error_panic_hook` crate。

### 错误:闭包被递归调用或已经被销毁

这意味着你正在使用一个闭包，你应该在使用它之后添加一个`forget`方法，这是让我有点担心的事情之一，特别是当你在文档中读到:`this function will leak memory. It should be used sparingly to ensure the memory leak doesn't affect the program too much.`的时候，然而没有其他方法让闭包工作。