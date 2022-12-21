# 学习 NextJs -第 9 天

> 原文：<https://dev.to/eperedo/learning-nextjs-day-9-3lhl>

默认情况下，NextJs 允许你自带静态文件(Js、css、images、json 等),无需任何额外的配置。为了做到这一点，你需要在你的项目中有一个**“静态”**文件夹。

因此，让我们用这个特定的名称
创建文件夹

```
mkdir static 
```

现在，您可以将图像(或任何其他静态文件)放在那里，并在您的页面中使用它。让我们假设你在**静态**中有你的应用的标志，在【mylogo.png 的**的创意名称下。**

你可以使用下面的路径来显示 img 标签中的 logo

```
/static/mylogo.png 
```

让我们创建一个 **contact.js** 页面

```
function Contact() {
  return (
    <img src="/static/mylogo.png" alt="My App Name" />
  );
}

export default Contact 
```

如果你启动你的 NextJs 服务器，进入 url **/contact** ，你会看到你的 logo。

### 自定义服务器

如果您已经将 next 与 express、 [hapijs](https://dev.to/eperedo/learning-nextjs-day-8-11) 或任何其他 web 框架集成，您还需要在您的 web 框架上声明该路由。

如果你使用 hapijs，路线会是这样的

```
// other code omited 
// for reading purposes

server.route({
  method: 'GET',
  path: '/static/{p*}',
  handler: nextHandlerWrapper(app),
}); 
```

这样你就告诉 HapiJs 在每次请求静态文件夹时使用 nextHandlerWrapper。