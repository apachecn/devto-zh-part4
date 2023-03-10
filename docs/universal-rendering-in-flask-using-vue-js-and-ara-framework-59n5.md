# 使用 Vue.js 和 Ara 框架在 Flask 中进行通用渲染

> 原文：<https://dev.to/phillipgd1992/universal-rendering-in-flask-using-vue-js-and-ara-framework-59n5>

通用渲染包含在服务器端渲染页面中，并使用 React、Vue.js、Angular 等构建的相同视图组件使它们在浏览器上交互。

如今，像 [Next.js](https://nextjs.org/) 、 [Nuxt.js](https://nuxtjs.org/) 和 [NgUniversal](https://github.com/angular/universal) 这样的 JavaScript 框架使得这项任务变得更加容易。但是，如何在 Flask 这样的非 JavaScript 框架上实现呢？

## 新代理

它是 [Ara 框架](https://ara-framework.github.io/website/)的代理服务器部分，可以在任何 web 平台(Laravel、Flask 等)上用任何视图库(React、Vue.js 等)实现通用渲染。例如，在这个演示中，我们将使用 Vue.js 将一个视图渲染到用 Flask 构建的 web 应用程序中。

工作原理:

[![](img/ea4107445356dc641d19d0d6c32129db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GiWYGeBj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2A0_KCs-IFVCIN8J5RwP7uFg.png)

1.  用户向 **Nova 代理**请求页面。

2.  **Nova 代理**将请求传递给网站服务器。

3.  网站使用一个 **Nova 指令**来呈现应该包含 Nova 视图的占位符。

4.  网站将生成的 HTML 发回给 **Nova 代理**。

5.  Nova 代理在占位符上包含 Nova 视图，并将 HTML 发送回浏览器。

最后，在浏览器上，JavaScript 用于逐步增强应用程序并使其具有交互性。点击此处阅读更多关于 Nova 架构的信息。

## 设置烧瓶 App

我们将使用 [SAO](https://github.com/saojs/sao) 来生成 base Flask 应用程序。

安装骚:

```
npm i -g sao 
```

创建烧瓶应用:

```
sao marconi1992/create-flask-app ara-flask 
```

### 设置 Nova 指令

注意，`requirements.txt`文件包含一个名为[hypernova _ jinja 2 _ directive](https://github.com/ara-framework/hypernova-jinja2-directive)的包。它使用 Flask (Jinja2)的模板引擎来呈现 Nova 指令。

将`jinja`的`nova`助手设置为`__init__.py`:T3

```
from flask import Flask
from hypernova_jinja2_directive import nova
# Initialize the app app = Flask(__name__, instance_relative_config=True)

# Load the views from app import views

# Load the config file app.config.from_object('config')

app.jinja_env.globals.update(nova=nova) 
```

### 运行烧瓶应用程序

生成的应用程序包含一个运行 Flask 应用程序的`docker-compose.yml`文件。

运行它:

```
docker-compose up -d 
```

在 http://localhost:8000/ 上测试 Flask 应用程序。

浏览器:

[![flask page](img/abdd6d9827834dd3df72466d51ccc480.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eawnGDbN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ara-framework.github.io/website/img/blog/flask-page.png)

## 设置 Nova 服务

我们将使用 Vue.js 创建一个 Nova 服务。

安装 [Ara CLI](https://github.com/ara-framework/ara-cli) :

```
npm i -g ara-cli 
```

创建 Nova 服务:

```
ara new:nova -t vue nova 
```

转到 Nova 服务文件夹:

```
cd nova 
```

运行 Nova 服务:

```
npm run dev 
```

Nova 服务运行在 [http://localhost:3000](http://localhost:3000) 上。

### 测试 Nova 服务。

一旦 Nova 服务开始运行，您就可以使用有效负载向[http://localhost:3000/batch](http://localhost:3000/batch)发出一个`POST`请求，比如:

```
{  "uuid":  {  "name":  "Example",  "data":  {  "title":  "Ara Framework"  }  }  } 
```

响应中的`results`属性包含由 Nova 服务呈现的视图的`html`。

示例:

```
{  "success":  true,  "error":  null,  "results":  {  "uuid":  {  "name":  "Example",  "html":  "<div data-hypernova-key=\"Example\" data-hypernova-id=\"4d9e81bd-6413-4661-ab56-ed5bb4f59cae\"><h1 data-server-rendered=\"true\">Ara Framework</h1></div>\n<script type=\"application/json\" data-hypernova-key=\"Example\" data-hypernova-id=\"4d9e81bd-6413-4661-ab56-ed5bb4f59cae\"><!--{\"title\":\"Ara Framework\"}--></script>",  "meta":  {},  "duration":  1.210146,  "statusCode":  200,  "success":  true,  "error":  null  }  }  } 
```

### 更新示例视图

视图只呈现了一个简单的标题。我们可以通过添加一个改变标题文本的输入元素来使它具有交互性。

用以下代码替换`nova/src/components/Example.vue`:

```
<template>
  <div>
    <h2 class="title">{{title}}</h2>
    <input type="text" v-model="title">
  </div>
</template>

<script>
export default {
  props: ['title']
}
</script> 
```

## 设置 Nova 代理

在根文件夹中创建 Nova 代理的配置文件:

```
touch nova-proxy.json 
```

在`nova-proxy.json`文件中添加以下配置，将传入请求代理到 Flask web 服务器。

```
{  "locations":  [  {  "path":  "/",  "host":  "http://localhost:8000",  "modifyResponse":  true  }  ]  } 
```

### 运行 Nova 代理

在运行命令之前，我们需要使用 Nova 服务端点设置`HYPERNOVA_BATCH`变量。

```
export HYPERNOVA_BATCH=http://localhost:3000/batch 
```

您需要在创建了`noxa-proxy.json`文件的地方运行下面的命令，或者传递带有配置文件路径的`--config`参数。

```
ara run:proxy --config ./nova-proxy.json 
```

该命令在 http://localhost:8080 上运行 Nova 代理。

### 渲染 Nova 视图(Vue.js)

在 Jinja2 模板页面中使用 Nova 指令:

`app/templates/index.html`

```
{% extends "base.html" %}
{% block body %}
  <div class="container">
    <h1 class="title">Hello Flask</h1>
    {{ nova('Example', {'title':'Ara Framework'}) }}
  <div>
{% endblock %} 
```

`nova`助手需要视图的`name`,以及渲染视图所需的`data`。

打开 [http://localhost:8080](http://localhost:8080) 上的页面，注意到呈现了 Nova 视图。

[![flask-example-vue-render](img/9f9f7afe3fbb6190bd2f634ca202e376.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hmzXBT-k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ara-framework.github.io/website/img/blog/flask-example-vue-render.png)

呈现的视图还不是交互式的，如果我们在 input 元素中键入内容，标题文本不会更新。这是因为我们没有加载客户端脚本。

## 水合物新星浏览器上的视图

水合是使用在服务器上呈现视图组件时使用的状态在浏览器上安装视图组件的过程。

为了丰富视图，我们需要在浏览器上加载`client.js`脚本。

更新 Flask app 中的`base.html`文件:

```
<!DOCTYPE html>
<html lang="en">
<head>
    ....
</head>
<body>
    ....
    {% block body %}
    {% endblock %}
    <script src="http://localhost:3000/public/client.js"></script>
</body>
</html> 
```

最后，服务器上呈现的 Nova 视图在浏览器上是交互式和动态的。

[![interactive-nova-view](img/81f91547ab82e740962e5aa8adcf9090.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--od9u16ix--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ara-framework.github.io/website/img/blog/interactive-nova-view.gif)

## 结论

Nova 代理使我们能够在任何 web 框架上使用现代视图库。因此，如果您之前使用非 JavaScript 框架(Laravel、Flask、Ruby on Rails 等)开发了一个 web 应用程序，那么 Nova Proxy 可以帮助您在短时间内逐步将其视图迁移到 Javascript 视图库(React、Vue.js)。