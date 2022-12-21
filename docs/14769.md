# 在你的 Jekyll 站点上实现黑暗模式

> 原文：<https://dev.to/francisoliver/implementing-dark-mode-to-your-jekyll-site-37kn>

Jekyll 是一个由 Ruby 驱动的静态站点生成器，它可以使用 HTML 和 Markdown 作为标记和 Liquid 的模板引擎。它是我的[个人网站](https://francisoliver.dev)的动力技术！随着现在 web 开发中既有光明主题又有黑暗主题的流行，我们将在 Jekyll 站点中实现这一功能。

使用原生 CSS 变量和 JavaScript，我们将在我们的 Jekyll 网站上实现一个成熟的切换黑暗模式或黑暗主题。首先，我们必须首先为我们的灯光主题定义 CSS 变量。在 CSS 中，我们通过在变量名前加两个破折号来定义变量，如下面的代码示例所示。

```
html {
    --bg: #fff;
    --bg-secondary: #f3f3f3;
    --headings: #07f;
    --text: #333;
} 
```

我们可以看到，我们有四个 CSS 变量，即`--bg`、`--bg-secondary`、`--headings`和`--text`。一旦我们为浅色主题设置了所有的 CSS 变量，我们为深色主题创建另一组变量，保留变量名，但是用下面的代码
中的`data-theme='dark'`属性把它附加到`html`元素上

```
html[data-theme='dark'] {
    --bg: #333;
    --bg-secondary: #444;
    --headings: #39f;
    --text: #bbb;
} 
```

有了这两个步骤，我们只需要将 CSS 变量分配给不同的 HTML 组件。

```
body {
    background-color: var(--bg);
}

.container {
    background-color: var(--bg-secondary);
}

h1, h2, h3, h4, h5, h6 {
    color: var(--headings);
}

p, strong, b, em, span, code, small {
    color: var(--text);
} 
```

现在在你的**主 HTML 布局文件**中，检查你的黑暗模式是否工作，添加属性`data-theme='dark'`到`html`元素。

```
<!DOCTYPE html>
<html lang="en" data-theme="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="/assets/js/application.js"></script>
    <link rel="stylesheet" href="/assets/css/application.css">
    My Jekyll Site
<head>
<body>
    <!-- Jekyll Template -->
</body>
</html> 
```

现在我们让站点处于光照模式，将`data-theme`属性设置回`light`。现在我们必须创建一个按钮在两种模式之间切换，它给类`theme-toggle`能够使用 JavaScript 选择它。

```
<button class="theme-toggle">Toggle Dark Mode</button> 
```

接下来，我们将使用我们编写的本地 JavaScript 编写所需的 JavaScript，当单击按钮时，它必须在两个主题之间切换。

```
document.addEventListener('DOMContentLoaded', themeChange);

function themeChange(){
    // Select our toggle button
    let button = document.querySelector('.theme-toggle');

    // Add an event listener for a click
    button.addEventListener('click', function(e){
        // Check the current data-theme value
        let currentTheme = document.documentElement.getAttribute('data-theme');
        if(currentTheme === 'light') {
            transition();
            document.documentElement.setAttribute('data-theme', 'dark');
        } else {
            transition();
            document.documentElement.setAttribute('data-theme','light');
        }
    });

    // Adds the 'transition' class to <html> for CSS fun
    let transition = () =>{
        document.documentElement.classList.add('transition');
        window.setTimeout(()=>{
            document.documentElement.classList.remove('transition');
        }, 1000);
    }
} 
```

回到我们的 CSS 文件来添加 CSS 过渡，我们在 CSS 文件的末尾添加了下面的代码。

```
html.transition,
html.transition *,
html.transition *:before,
html.transition *:after {
    transition: all 650ms !important;
    transition-delay: 0 !important;
} 
```

如果你用的是 React.js，Angular 或者 Vue 这样的单页应用。这种实现在您浏览 web 应用程序时工作良好，但对于 Jekyll 来说，文件构建在`_sites`文件夹中，并且在部署时技术上有多个 HTML 页面和文件，当我们从一个 HTML 文件移动到另一个 HTML 文件时，黑暗模式会立即消失。为了解决这个问题，我们使用了一个 Ruby 开发人员熟知的特殊库: [**turbolinks.js**](https://github.com/turbolinks/turbolinks) 。众所周知，Ruby on Rails 框架的主要实现和 turbolinks 工作原理是在 Jekyll 站点导航时保留黑暗主题的解决方案。

Turbolinks 的工作原理是拦截站点中的锚标签或`<a>`标签，而不是对其进行常规的`GET`请求，turbolinks 拦截该请求，并进行`XMLHttpRequest`或更通常被称为`AJAX`的请求，而只是替换`<body>`标签的内容。所以从技术上来说，它使我们的 Jekyll 网站在某种意义上像一个“单页应用程序”。

我们只需要通过 CDN 或本地通过`npm`添加 turbolinks.js 库，并将其添加到我们默认 HTML 布局的头中。

```
<!DOCTYPE html>
<html lang="en" data-theme="light">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/turbolinks/5.2.0/turbolinks.js"></script>
    <script src="/assets/js/application.js"></script>
    <link rel="stylesheet" href="/assets/css/application.css">
    My Jekyll Site
<head>
<body>
    <!-- Jekyll Template -->
</body>
</html> 
```

在添加 turbolinks 时，我们必须编辑我们的 JavaScript 文件，因为我们说过我们希望代码在`'DOMContentLoaded'`事件被触发时运行，但是当我们浏览页面时，由于 turbolinks，这个事件没有被触发，因此我们的代码无法执行。我们必须将事件处理程序更改为 turbolinks 事件，即 turbolinks 文档中提供的`'turbolinks:load'`事件。现在我们的 JavaScript 文件应该看起来像下面的例子。

```
document.addEventListener('turbolinks:load', themeChange);

function themeChange(){
    // Select our toggle button
    let button = document.querySelector('.theme-toggle');

    // Add an event listener for a click
    button.addEventListener('click', function(e){
        // Check the current data-theme value
        let currentTheme = document.documentElement.getAttribute('data-theme');
        if(currentTheme === 'light') {
            transition();
            document.documentElement.setAttribute('data-theme', 'dark');
        } else {
            transition();
            document.documentElement.setAttribute('data-theme','light');
        }
    });

    // Adds the 'transition' class to <html> for CSS fun
    let transition = () =>{
        document.documentElement.classList.add('transition');
        window.setTimeout(()=>{
            document.documentElement.classList.remove('transition');
        }, 1000);
    }
} 
```

这使得我们的 JavaScript 文件可以在点击按钮时执行，并在不丢失我们在站点中选择的主题的情况下导航。请记住，这仅在浏览网站的一个会话中有效，这意味着如果使用同一网站创建另一个选项卡，将显示默认的灯光主题，如果您将来再次访问该网站，它将恢复到默认主题。

尝试使用 cookies 在浏览器上存储一些信息，以保留用户喜欢什么主题的信息。是另一个时代的另一个实现。