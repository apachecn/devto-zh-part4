# 3 个 JavaScript 代码片段帮助您入门

> 原文：<https://dev.to/jasterix/3-javascript-snippets-to-get-you-started-1lh>

经过与 React 的激烈斗争，我抓住机会回答了一些关于 JavaScript 的问题。虽然这些对话是离线进行的，但我仍然认为提供一些其他编程学生正在努力解决的最常见问题的片段可能会有所帮助。

#### 操纵 DOM

*   在 JavaScript 中，这发生在 3 个部分:
*   获取请求或 API 调用
*   在 DOM 上渲染
*   事件处理

我越来越明白为什么大部分编程类博客帖子倾向于偏长。但是和往常一样，我会试着提供一些(模板化的)简短片段，很少评论，后面是第三方资源，会有更多的细节。

#### 获取请求(GET)

```
let url = "http://localhost:3000/books"
fetch(url)
.then(res => res.json())
.then(data=> {
data.forEach(book => {
parentElement.innerHTML += render(book)
}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 向 DOM 添加 API 对象

```
const render=(book)=>{
return(`<div>
    <h1>${book.title}</h1>
    <button class='del-btn> 🗑</button>
`)} 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加事件处理程序，点击时从 DOM 中删除项目

```
let divTag = document.querySelector('div')

divTag.addEventListener('click', (event) => {
if(event.target.className.contains('del-btn') {
        event.target.parentElement.remove()
      }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这些都是非常简单的例子，让你了解这三个部分是如何相互作用的。关于更详细的阅读，[freecodecamp](https://www.freecodecamp.org/news/a-practical-es6-guide-on-how-to-perform-http-requests-using-the-fetch-api-594c3d91a547/)的这篇文章为更详细地探索 JavaScript DOM 操作和 API 调用提供了一个很好的基础