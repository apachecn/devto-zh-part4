# 美丽王国；用 TypeScript 构建的 HTML 解析器

> 原文：<https://dev.to/ajahso4/beautiful-dom-a-html-parser-built-with-typescript-26op>

# 美丽王国

Beautiful-dom 是一个轻量级库，它反映了解析抓取的 HTML/XML 页面所需的 HTML DOM API 的功能。它对与从 HTML 节点提取数据相关的 HTML 节点的方法和属性进行建模。它是用 TypeScript 编写的，可以用作 CommonJS 库

## 你得到了什么

*   解析 HTML 文档的能力，就像在实时浏览器中处理 HTML 文档一样
*   从 HTML 节点返回基本数据的快速查询
*   搜索和解析后 HTML 节点的就地顺序。
*   使用 CSS 选择器的复杂查询。

## 如何使用

```
npm install --save beautiful-dom 
```

```
const BeautifulDom = require('beautiful-dom');
const document = `
<p class="paragraph highlighted-text" >
  My name is <b> Ajah, C.S. </b> and I am a <span class="work"> software developer </span>
</p>
<div class = "container" id="container" >
 <b> What is the name of this module </b>
 <p> What is the name of this libray </p>
 <a class="myWebsite" href="https://www.ajah.xyz" > My website </a>
</div>
<form>
  <label for="name"> What's your name? </label>
  <input type="text" id="name" name="name" />
</form>
`;
const dom = new BeautifulDom(document); 
```

## API

文档对象上的方法。

*   document.getElementsByTagName()
*   document . getelementsbyclassname()
*   document.getElementsByName()
*   document.getElementById()
*   document.querySelectorAll()
*   document.querySelector()

HTML 节点对象上的方法

*   node.getElementsByClassName()
*   node.getElementsByTagName()
*   node.querySelector()
*   node.querySelectorAll()
*   node.getAttribute()

HTML 节点对象的属性

*   node.outerHTML
*   node.innerHTML
*   节点.文本内容
*   节点. innerText

它们的用法就像在实际的 HTML DOM 中使用期望的方法参数一样。

## 示例为文档对象

```
 let paragraphNodes = dom.getElementsByTagName('p');
// returns a list of node objects with node name 'p'

let nodesWithSpecificClass = dom.getElementsByClassName('work');
// returns a list of node objects with class name 'work'

let nodeWithSpecificId = dom.getElementById('container');
// returns a node with id 'container'

let complexQueryNodes = dom.querySelectorAll('p.paragraph b');
// returns a list of nodes that satisfy the complex query of CSS selectors

let nodesWithSpecificName = dom.getElementsByName('name');
// returns a list of nodes with the specific 'name'

let linkNode = dom.querySelector('a#myWebsite');
// returns a node object with with the CSS selector

let linkHref = linkNode.getAttribute('href');
// returns the value of the attribute e.g 'https://www.ajah.xyz'

let linkInnerHTML = linkNode.innerHTML
// returns the innerHTML of a node object e.g ' My website '

let linkTextContent = linkNode.textContent 
// returns the textContent of a node object e.g ' My website '

let linkInnerText = linkNode.innerText
// returns the innerText of a node object e.g ' My website '

let linkOuterHTML = linkNode.outerHTML
// returns the outerHTML of a node object i.e. '<a class="myWebsite" href="https://www.ajah.xyz" > My website </a>' 
```

## 举例为一个节点对象

```
 let paragraphNodes = dom.getElementsByTagName('p');
// returns a list of node objects with node name 'p'

let nodesWithSpecificClass = paragraphNodes[0].getElementsByClassName('work');
// returns a list of node objects inside the first paragraph node with class name 'work' 

let complexQueryNodes = paragraphNodes[0].querySelectorAll('span.work');
// returns a list of nodes in the paragraph node that satisfy the complex query of CSS selectors

let linkNode = dom.querySelector('a#myWebsite');
// returns a node object with with the CSS selector

let linkHref = linkNode.getAttribute('href');
// returns the value of the attribute e.g 'https://www.ajah.xyz'

let linkInnerHTML = linkNode.innerHTML
// returns the innerHTML of a node object e.g ' My website '

let linkTextContent = linkNode.textContent 
// returns the textContent of a node object e.g ' My website '

let linkInnerText = linkNode.innerText
// returns the innerText of a node object e.g ' My website '

let linkOuterHTML = linkNode.outerHTML
// returns the outerHTML of a node object i.e. '<a class="myWebsite" href="https://www.ajah.xyz" > My website </a>' 
```

## 投稿

如果你有任何想法，你想包括的功能或任何错误修复，你可以发送一个 PR。

(需要节点版本 6 或更高版本)

*   克隆回购

```
git clone https://github.com/ChukwuEmekaAjah/beautiful-dom.git 
```

使用 TypeScript 构建这个 NodeJS 模块是一件令人兴奋的事情，因为我最近学会了如何使用 TypeScript，还有什么更好的方法来实践和试验新知识呢？

我将感谢对项目的评论和贡献，以及关于我可能没有解决的边缘案例以及您在使用该模块时遇到的错误的问题。