# lit-html 模板从零到英雄，适合初学者

> 原文：<https://dev.to/julcasans/lit-html-templates-from-zero-to-hero-for-beginners-3eje>

> 📣**更新！**📣
> *现在 lit-html 和 LitElement 统一在 [Lit](https://lit.dev/) 下。*
> *我正在写关于**点燃**的新帖子，同时你可以:*
> 
> *   *阅读这篇文章，因为原理是一样的*
> *   *用这个[指南](https://lit.dev/docs/releases/upgrade/)* 升级你的代码
> *   *访问 *Lit* 网站了解最新动态*
> 
> *你也可以使用 [lit-html 单机](https://lit.dev/docs/libraries/standalone-templates/)T3】*

非常顺利的入门`lit-html`之后，我就要去硬的，弄脏手。我将向你展示如何编写模板，所以这将是一个充满代码的会议！

> 我知道你在这里是因为你想学`LitElement`，所以你看了这个标题，发现我们继续学`lit-html`。我之所以以这种方式组织这些会议，是因为`lit-html`是`LitElement`的基础，也因为我想让你看到`lit-html`本身的巨大潜力和多功能性，可能是中小型项目的完美匹配。我向你保证，你学到的关于`lit-html`的一切都将用于`LitElement`。耐心点，我们很快就到了。

## 设置事物

首先，我们将创建一个项目，您可以在其中尝试您在这里看到的每个示例。我也建议你做出改变，尝试并创造你自己的例子。最好的学习方法是边做边学。

我们创建项目:

```
mkdir lit-train
cd lit-train
npm init --yes 
```

Enter fullscreen mode Exit fullscreen mode

最后一个命令创建了我们将用来处理依赖关系的`package.json`文件。

下一步是使用`npm`安装`lit-html`。

```
npm install --save lit-html 
```

Enter fullscreen mode Exit fullscreen mode

目前，浏览器不使用短语法:
导入 ES6 模块

```
import {html, render} from 'lit-html' 
```

Enter fullscreen mode Exit fullscreen mode

我们必须写出模块
的完整路径

```
import {html, render} from './node_modules/lit-html/lit-html.js' 
```

Enter fullscreen mode Exit fullscreen mode

因此，使用简短的语法，我们将安装`polymer-cli`。Polymer 命令行工具能够为我们的项目服务，同时它处理短语法导入。为此，只需运行命令`polymer serve`。为了使事情变得简单，我们将在本地项目和包中安装`polymer-cli`。JSON 将添加一个脚本来运行它。

```
npm install --save-dev polymer-cli 
```

Enter fullscreen mode Exit fullscreen mode

`package.json`

```
{  "name":  "lit-train",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "start":  "polymer serve"  },  "keywords":  [],  "author":  "",  "license":  "ISC"  } 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 index.html 文件，并在其中写入以下代码:

`index.html`

```
<!DOCTYPE html>
<html lang="en">
<body>
  <div id="main"></div>
  <script type="module">
    import {html, render} from 'lit-html.js';

    // write your code here

  </script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

要运行项目，请执行以下命令:

```
npm run start 
```

Enter fullscreen mode Exit fullscreen mode

您已经准备好开始编码了！现在，您可以继续阅读并尝试刚刚配置的项目中的示例。

## 编写模板

上一篇文章中的☝️Remember:模板是用带标签的模板文字编写的。我们用由`lit-html`提供的`html`函数来标记模板文字。这个函数返回一个`TemplateResult`对象。
`render`是`lit-html`提供的另一个功能。`render`接收一个`TemplateResult`对象并将其渲染到 DOM 中。

这是一个非常简单的例子:

```
// import functions from lit-html library
import {html, render} from 'lit-html';

// define the template as function that takes the data
// and returns a TemplateResult object
const template = name => {
  return html`
    <p>Hello ${name}</p>
  `;
};

// node where the template will be rendered
const mainNode = document.getElementById('main');

// render the template
render(template('Silvia'), mainNode);

// render the template again with different data
render(template('Benjamin'), mainNode); 
```

Enter fullscreen mode Exit fullscreen mode

模板的动态部分是用值绑定的 JavaScript 表达式。`TemplateResult`支持我们即将看到的某些数据类型的绑定。

### 支持绑定

*   **Text** :解析为文本的表达式可以作为节点的文本内容。请注意，文本绑定中的空字符串(“”)将呈现空文本节点。

```
// Text binding
html`<p>${someText}</p>`;
html`<div>${capitalize(user.name, user.firstName)}</div>`;

/**** Given these variables and values... ***************

let someText = 'Lorem ipsum';
let user = { name : 'JEN', firstName: 'MONROE' };  

***** The code above will render... *********************

<p>Lore ipsum</p>
<div>Jen Monroe</div>
*********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

*   **属性**:返回文本的表达式可以作为属性的值。

```
// Attribute binding
html`<div class=${classList}>Stylish text.</div>`;

/**** Given these variables and values... ****************

let classList = ['main', 'required', 'modern'].join(' ');

***** The code above will render... ***********************

<div class="main required modern">Stylish text.</div>
***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

当一个属性的行为像一个布尔值(它是否存在)时，我们用属性名加上`?`来表示它。表达式的值必须是布尔型的，如果是`true` `lit-html`则放入属性，如果是`false`则移除属性。

```
// Attribute binding (boolean)

html`<input type="submit" ?disabled=${formWithErrors} value="Submit">
     <span ?hidden=${!formWithErrors}>Form has errors!</span>`;

/**** Given these variables and values... *****************

let formWithErrors = true;

***** The code above will render... ***********************

<input type="submit" disabled value="Submit">`;
<span>Form has errors!</span>`
***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

*   **属性**:表达式也可以绑定到 JavaScript 节点的属性。我们用一个`.`作为属性名的开头。在这种情况下，表达式的类型必须与属性的类型相匹配(它可以是任何类型，甚至是复杂的对象)。

```
// Property binding
html`<custom-list .items=${users} id="user-list"></custom-list>`;

/**** Given these variables and values... ****************

const users = ['Diego', 'Ana', 'Laura', 'Piero'];
const customList = document.getElementById('user-list');
console.log(customList.items);

***** The code above will show in console *****************

 ["Diego", "Ana", "Laura", "Piero"]

***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

*   **事件**:一个表达式可以是一个事件的处理程序。为此，我们在事件名称前加上`@`，在这种情况下，我们有几个可供选择的绑定。

*全局处理函数*:表达式解析为一个将处理事件的全局函数。

```
// Event binding to a global handler function
html`<button @click=${handleClick}>Click Me!</button>`; 
```

Enter fullscreen mode Exit fullscreen mode

*内联函数*:表达式解析为内联函数。

```
html`<button @click=${()=>console.log('clicked')}>Click Me!</button>`; 
```

Enter fullscreen mode Exit fullscreen mode

*实例函数*:表达式解析为属于你对象的函数。

```
html`<button @click=${this.handleClick}>Click Me!</button>`; 
```

Enter fullscreen mode Exit fullscreen mode

*事件监听器对象*:表达式返回一个对象，这个对象*必须有*一个名为`clickHandler`的函数。

```
html`<button @click=${clickHandler}>Click Me!</button>`;

const clickHandler = {
  handleEvent(e) { console.log('clicked!'); }
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   **HTML 节点元素**:表达式可以返回一个 DOM 节点。

```
const h1 = document.createElement('h1');
h1.textContent = 'Chapter 1'
const page = html`${h1}<p>Once upon a time...</p>`;

/***** The code above will render... **********************

<h1>Chapter 1</h1>
<p>Once upon a time...</p>
***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

*   **TemplateResult** :表达式可以是另一个`TemplateResult`对象。这使得嵌套模板的组合成为可能。

```
const header = html`<h1>Chapter 1</h1>`;
const article = html`<article>${header}<p>Once upon a time...</p></article>`;

/***** The code above will render... **********************

<article>
  <h1>Chapter 1</h1>
  <p>Once upon a time...</p>
</article>
***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

*   **Iterable of `TemplateResult` objects** :返回数组或 iterables of `TemplateResult` objects 的表达式。

```
const items = [1, 2, 3];
const listItems = items.map(i => html`<li>${2*i}</li>`);
const template = html`<ul>${listItems}</ul>`;

/***** The code above will render... **********************

<ul>
 <li>2</li>
 <li>4</li>
 <li>6</li>
</ul>
***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

*   **Promise** :表达式可以返回一个必须解析的承诺，返回一个有效的绑定值。

```
const text = fetch(url).then(response => response.text());
const page = () => html`<p>${text}</p>`;

/***********************************************************
Let's say that after some seconds the fetch operation 
resolves with a the string 'some text...'

Until the promise is resolved, the code above will render
<p></p> 

Once the promise is resolved, it will render...
<p>some text...</p>

***********************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

### 作文

绑定到返回`TemplateResult`对象的表达式的一个结果是，通过组合，我们可以使用其他模板创建模板。该组合物允许:

*   使用简单的模板创建复杂的模板。
*   通过将复杂的模板分解成更简单的模板来重构它。
*   模板的重用(JavaScript 模块的使用使得重用变得容易得多，例如，一个通用模板、部分模板等的模块。)

```
const header = data => html`
    <h1>${data.title}<h1> ${data.subtitle ? html`<h2>${data.subtitle}<h2>` : ''}`;

const main = data => html`<p>${makeParagraph(data.text)}</p>`;

const composedTemplate = data => html` ${header(data)}  ${main(data)}`; 
```

Enter fullscreen mode Exit fullscreen mode

### 条件句

模板可以包含仅在满足条件时才可见的部分，也可以包含根据一个或多个条件以不同方式表示的部分。这些行为可以用条件表达式来表达，比如三元运算符`?`或条件结构，比如`if`或`switch-case`语句。

```
// using ternary operator
const main = data => html` ${data.text ?
        html`<p>${data.text}</p>` :
        html`<img src=${data.image}></img>`}`;

// using if
const main = data => {
    if (data.text) {
        return html`<p>${data.text}</p>` :
    } else {
        return html`<img src=${data.image}></img>`;
    }
}

// using switch-case
const main = data => {
    switch (data.screenSize) {
    case 's':
        return html`<img src="${data.image}-small.png"></img>`;
    case 'm':
        return html`<img src="${data.image}-medium.png"></img>`;
    case 'l':
        return html`<img src="${data.image}-medium.png"></img>
                    <img src="${data.logo}.png"></img>`;
    default:
        return html`<p>${data.text}</p>`;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 迭代次数

模板的一部分用不同的值重复是很常见的。想象一个列表，其中描绘每个项目的部分总是相同的，但是具有不同的值。对于这些情况，我们已经看到，绑定到从`TemplateResult`对象返回可重复项的表达式是可能的。这是我们在模板中重复零件或块的方式。

```
const weekDays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Staurday', 'Sunday'];

// loops with arrays
html`<ul>${weekDays.map((day) => html`<li>${day}</li>`)}</ul>`;

// loop statement (better if DOM node update is cheap)
const itemTemplates = [];
for (const day of weekDays) {
  itemTemplates.push(html`<li>${day}</li>`);
}
html`<ul>${itemTemplates}</ul>`;

/*************************************
* We'll see the use of directives 
* in a future post !!
*************************************/
// repeat directive (better if moving DOM node is cheap)
<ul>${repeat(weekDays, day => day /*id*/, (day, index) => html`<li>${day}</li>`)}</ul>`; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

正如我们所看到的，模板是纯 JavaScript 代码，因此任何表达式和语言的有效结构都可以在模板中使用。有很多方法可以定义一个模板，只受每个开发人员的偏好或风格的限制。结果是这些模板表现得非常有表现力。