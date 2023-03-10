# web 组件浏览器中的遗传算法

> 原文：<https://dev.to/d3spis3d/genetic-algorithm-in-the-browser-with-webassembly-1e97>

这是我关于 Rust 和遗传算法 WebAssembly 系列的第二篇文章。在这篇文章中，我将利用我在 Rust 中实现的遗传算法来解决旅行推销员问题。我将采用 Rust 实现，并做一些调整，使它能够编译成 WebAssembly，并与 JavaScript 和浏览器交互。然后，我将开发一个 web 应用程序，为运行遗传算法提供用户界面。

这个帖子的所有代码可以在[这里](https://github.com/d3spis3d/wasm-genetic)找到，现场演示可以在[这里](https://d3spis3d.github.io/wasm-genetic/)找到。

[![Cracked textured Photo by Vincent Burkhead on Unsplash](img/930ed312795c6ec7b255b01d5f0ed1ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9wIHp0iA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w002gywiv7p03155v854.jpg) 
照片由[文森特·伯克黑德](https://unsplash.com/@creativejunkie?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/abstract?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

对于这个项目，我使用的是 [Rust Webpack 模板](https://github.com/rustwasm/rust-webpack-template)，它被设计用来创建 monorepo 风格的 Web 应用程序，这些应用程序由 JavaScript、HTML 和 Rust 生成的 WebAssembly 组成。这个模板依赖于一些工具；`wasm-pack`、`npm`，设置 WebAssembly 编译目标`wasm32-unknown-unknown`。关于设置的更多细节可以在`wasm-pack` [文档](https://rustwasm.github.io/docs/wasm-pack/introduction.html)中找到。

首先，需要对 Rust 代码进行一些更改，以支持对 WebAssembly 的编译。第一件事是添加`wasm-bindgen`，它将提供定义 Rust 代码的哪些部分将被导出并可从 JavaScript 访问的方法。将`wasm-bindgen`宏添加到**模拟**结构中，告知库将其构建为 WebAssembly 模块的导出，为构造函数添加宏允许使用 JavaScript 中的`new Simulation()`创建**模拟**。

```
#[wasm_bindgen]
pub struct Simulation {
    population: Vec<Path>,
    city_list: Vec<City>,
    max_iterations: usize,
    crossover_rate: f64,
    mutation_rate: f64,
    survival_rate: f64,
}

#[wasm_bindgen]
impl Simulation {
    #[wasm_bindgen(constructor)]
    pub fn new(
        population_size: usize,
        city_list: String,
        max_iterations: usize,
        crossover_rate: f64,
        mutation_rate: f64,
        survival_rate: f64,
    ) -> Simulation {
        ...
    }
} 
```

Rust 实现使用了[和](https://crates.io/crates/rand)机箱；幸运的是，rand crate 使用`wasm-bindgen`提供了对 WebAssembly 编译目标的支持。这可以在 Cargo.toml 项目中设置，从 0.7 版本开始可以无缝地工作。

```
[dependencies.rand]
version = "0.7"
features = [
  "wasm-bindgen",
] 
```

另一个有用的 crate 是`js_sys`，它提供了到 JavaScripts 内置对象和标准库的绑定。这有助于将模拟结果整理成调用 JavaScript 可以理解的格式。**模拟**的 run 方法的返回类型从`js_sys`变成了 Array，并且构造了一个数组，第一个条目是为解决方案访问的节点的有序列表，第二个条目是 fitness。

```
pub fn run(&mut self) -> Array {

      ...

      let array = js_sys::Array::new();
      let order = js_sys::Array::new();
      for o in fittest.order {
          order.push(&JsValue::from(o as u32));
      }

      array.push(&order);
      array.push(&JsValue::from(fittest.fitness));

      array
  } 
```

运行模拟的网络应用程序设计简单；它有算法参数的输入，一个用户可以点击添加城市的区域，以及一个添加默认城市列表的预设按钮。

[![Screenshot of web application for Genetic Algorithm](img/45b4a9ee19323923bbbafd90b8c79405.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X_c70pHS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/abp9edfuv9n7p33ujmym.png)

为了防止主线程在运行 WebAssembly 时被阻塞，该模块在 Web Worker 中运行。第一步是更改 Rust Webpack 模板提供的 Webpack 配置，以分别构建 app 和 worker JavaScript 文件。

```
const appConfig = {
  target: 'web',
  entry: "./js/index.js",
  output: {
    path: dist,
    filename: '[name].[hash].js',
  },
  devServer: {
    contentBase: dist
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'index.html',
      chunks: ['main']
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [{ loader: 'style-loader' }, { loader: 'css-loader' }],
      }
    ]
  }
}

workerConfig = {
  target: 'webworker',
  entry: "./js/worker.js",
  output: {
    path: dist,
    filename: 'worker.js',
    globalObject: 'self'
  },
  devServer: {
    contentBase: dist
  },
  resolve: {
    extensions: [".js", ".wasm"]
  },
  plugins: [
    new WasmPackPlugin({
      crateDirectory: path.resolve(__dirname, "crate"),
    }),
  ]
}

module.exports = [appConfig, workerConfig] 
```

工人代码非常简单。它导入 WebAssembly 模块，设置一个消息处理程序来接收来自主线程的带有模拟参数的数据，运行**模拟**，并向主线程回发一条带有结果的消息。

```
import("../crate/pkg").then(({ Simulation }) => {

  onmessage = function(msg) {
    const {
      iterations,
      citiesString,
      population_size,
      crossover,
      mutation,
      survival
    } = msg.data

    const s = new Simulation(
      iterations,
      citiesString,
      population_size,
      crossover,
      mutation,
      survival
    )

    const result = s.run()

    self.postMessage({
      path: result[0],
      fitness: result[1]
    })
  }
}) 
```

应用程序代码包括一个单击处理程序，它捕获用户在绘图空间内的单击并添加一个点。点的位置存储在 DOM 元素的`data-x`和`data-y`属性中，以便稍后传递给 WebAssembly 代码。

```
const draw = document.getElementById('draw')

function addDot(x, y) {
  const dot = document.createElement('div')
  dot.className = 'dot'
  dot.style = `top: ${y}px; left: ${x}px;`
  dot.setAttribute('data-x', x)
  dot.setAttribute('data-y', y)
  draw.appendChild(dot)
}

draw.onclick = function(event) {
  addDot(event.offsetX, event.offsetY)
} 
```

应用程序的主要部分是表单的`onsubmit`函数。它负责:

*   捕获模拟的输入参数，
*   提取用户已经绘制的城市的坐标，
*   向工人发送带有参数的消息，
*   设置消息处理器以接收模拟运行的结果并得出解决方案，
*   在解决方案画布上绘制城市。

```
form.onsubmit = function(event) {
  event.preventDefault()
  const [iterations, population_size, crossover, mutation, survival] = event.target

  const cities = []
  const count = draw.children.length
  for (let c = 0; c < count; c++) {
    cities.push([
      draw.children[c].getAttribute('data-x'),
      draw.children[c].getAttribute('data-y')
    ])
  }

  const citiesString = cities.map(c => c.join(',')).join(';')

  worker.postMessage({
    iterations: parseInt(iterations.value, 10),
    citiesString,
    population_size: parseInt(population_size.value, 10),
    crossover: parseFloat(crossover.value),
    mutation: parseFloat(mutation.value),
    survival: parseFloat(survival.value)
  })

  worker.onmessage = function(msg) {
    const { path, fitness } = msg.data

    for (let p = 0; p < path.length - 1; p++) {
      const start = cities[path[p]]
      const end = cities[path[p+1]]

      ctx.beginPath()
      ctx.moveTo(start[0], start[1])
      ctx.lineTo(end[0], end[1])
      ctx.stroke()
    }
  }

  const ctx = canvas.getContext('2d')

  cities.map(c => {
    ctx.beginPath()
    ctx.arc(c[0], c[1], 2, 0, 2 * Math.PI, false)
    ctx.fillStyle = 'black'
    ctx.fill()
  })
} 
```

完整的代码可以在[这里](https://github.com/d3spis3d/wasm-genetic)找到，还有演示[这里](https://d3spis3d.github.io/wasm-genetic/)。

在本文中，我展示了如何修改 Rust 代码以编译成 WebAssembly，并构建一个 web 应用程序来使用 WebAssembly 模块。在本系列的最后一篇文章中，我将介绍如何使用 Wasmtime 在浏览器之外运行这个 WebAssembly 模块。