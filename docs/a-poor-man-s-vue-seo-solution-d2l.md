# 穷人的 Vue-SEO 解决方案

> 原文：<https://dev.to/kdekooter/a-poor-man-s-vue-seo-solution-d2l>

假设你刚刚开始你最新的副业项目。Vue 让你把想法变成现实。现在你希望搜索引擎开始抓取你的新网站。当然登陆页面也是用 Vue 搭建的。对于经常访问这个页面的人来说，这个页面加载速度快，响应快，这要感谢为你创建的优化捆绑包 webpack。只有网络爬虫倾向于喜欢原始的 html，而不关心页面的视觉效果。

不愿意把宝贵的业余时间花在一个成熟的 SSR 解决方案上，比如 nuxt 或者一个几乎是入侵性的预呈现器。为什么不尝试重用模板呢？你的最终用户会看到 NGINX 的神奇之处。

# 从 Vue 模板渲染静态 HTML

## 包括 Vue 模板

首先我们需要教手柄如何包含外部文件

```
const handlebars = require('handlebars')
const fs = require('fs')
const path = require('path')

handlebars.registerHelper('importVueTemplate', (relativePath) => {
  const absolutePath = path.join(__dirname, relativePath)
  const source = fs.readFileSync(absolutePath, 'UTF-8')

  // Peel out the template content
  const regex = /^<template>([^]*)<\/template>/

  let matches = regex.exec(source)

  return matches[1]
}) 
```

## 渲染降价内容

```
const handlebars = require('handlebars')
const fs = require('fs')
const path = require('path')
const marked = require('marked')

handlebars.registerHelper('renderMdFile', (relativePath) => {
  const absolutePath = path.join(__dirname, relativePath)
  const source = fs.readFileSync(absolutePath, 'UTF-8')

  return marked(source, {
    sanitize: true,
    breaks: true,
    gfm: true,
    tables: true
  })
}) 
```

## 车把模板

```
<!DOCTYPE html>
<html>
  <head>
    dochub - share the knowledge
    {{! put all of your SEO tags here }}
  </head>
  <body>

    <header>
      {{#importVueTemplate '../src/components/LandingHeader.vue'}}{{/importVueTemplate}}
    </header>

    <main>
      {{#importVueTemplate '../src/components/LandingPage.vue'}}{{/importVueTemplate}}
    </main>
  </body>
</html> 
```

## 在构建步骤中编译成 html

```
let source = fs.readFileSync(path.join(__dirname, `/index.hbs`), 'UTF-8')
let template = handlebars.compile(source)

fs.writeFileSync(path.join(__dirname, '../dist/crawlers/index.html'), template({})) 
```

# 为网络蜘蛛提供静态 HTML

现在，我们必须拦截网络爬虫流量，并将其重定向到我们刚刚创建的静态页面。

```
location / {

  root /usr/share/nginx/html;

  # Enable history mode for Vue Router
  try_files $uri $uri/ /index.html; 
  # Checking for top 8 web crawlers (see https://www.keycdn.com/blog/web-crawlers)
  if ($http_user_agent ~* (googlebot|bingbot|slurp|duckduckbot|baiduspider|yandexbot|sogou|exabot)) {
      root /usr/share/nginx/html/crawlers;
  }
} 
```

早些时候在我自己的博客上发布:[https://boplicity . nl/knowledge base/Vue/A+Poor+Mans+Vue-SEO+solution . html](https://boplicity.nl/knowledgebase/Vue/A+Poor+Mans+Vue-SEO+Solution.html)