# 使用 React 路由器的单页应用程序

> 原文：<https://dev.to/dylanmesty/single-page-applications-using-react-router-3na5>

所以，如果这是你第一次浏览这个博客，我会简单地解释一下。在过去的 7 周里，我一直是 Lambda 学校的学生。在学校的时候，我写了日志和里程碑计划；然而，我一直想记录在那里度过的时光。从上周开始，我发表了我的第一篇博客——从那以后，我每周都会更新我在 Lambda 的经历。也就是说，这是我每周一次的单页应用总结！

#### **第七周:反应路由器**

本周我们探索了 React 路由器的世界。我了解了客户端和服务器端路由的区别。本质上，React 路由器允许客户端路由，如果你愿意，这是一个非常有效的“欺骗代码”。标准服务器端路由，要求页面重新加载，并要求服务器呈现网站或 web 应用程序的不同页面。使用 React Router，React.js 有一个内置的包，您可以在应用程序客户端的页面之间本地路由，从而无需刷新页面或获取任何非本地信息。**太牛逼了！**下面是一周的情况:

#### **第一天:反应路由器简介**

这一天是使用 React 路由器的第一印象。我们学习了安装和导入 React 路由器的基础知识。有了这个包，你就可以接触到大量的关键部件。一些基本的是:`Route`、`Link`、`BrowserRouter`。

本质上，导入`BrowserRouter`允许您的应用程序使用 React Router。我想你可以说它是*解锁键*。*神奇的关口*。*秘密通道*！将您的 React 应用程序包装在其中，赋予无限的可能性！

接下来是`Route`。帮助我理解`Route`的目的是它允许访问那条路径。实质上，一辆`Link`会带你去`Route`。`Route`允许你指定一个不同的 URL，从而加载不同的内容，即连接到那个路由。例如，你可以有一个主页:`https://www.test-website.com/`，这将呈现类似于主页的东西。

现在，假设你有不同的内容，可能是一个联系页面，或者是一个关于页面，包含的信息与主页完全不同。您可以使用`Route`来指定新的 URL 目的地:`https://www.test-website.com/about`。所有的工作都在本地完成，没有任何刷新，只是将新内容重新呈现到屏幕上。**超级好玩的**！这有无限的可能性！

最后是`Link`结束单页应用的第一天。简而言之，`Link`只是锚标记，React 组件内置了一些额外的功能。链接允许您指定想要的路线位置，并将目的地设置为锚标签的`href`属性。有了航线和链接，一切突然变得如此高效！

#### **第二天:React 路由器续..**

这一天变得更加深入，也更加令人困惑。然而，它仍然非常有趣！我们讨论了`Route`组件中`component`和`render`之间的差异。还会触碰到哪些道具默认用`Route`自动通过。以`NavLink`组件结束一天——现在**那个**就是我所说的*漂亮的*组件。

`render`和`component`非常相似，但有一个有益的区别:render 属性允许你*将道具传递给你正在渲染的组件*。在 React 路由器中，一个`Route`自动接收三个属性，但是使用`component`属性，你**没有**选项来传递任何额外的属性，比如状态，或者你已经存储在那个文件中的函数。`render`允许您简单地将这些代码作为道具传入，类似于任何其他组件——除了传入默认的`Route`道具，它们是`match`、`history`和`location`。相对来说，我仍然不确定所有这些道具的功能，所以我将把它留到以后再说。下面是两条不同路线的示例:一条路线使用`component`，另一条使用`render`。

```
<Route path="/pathHere" component={ExampleComponent} /> 
```

和

```
<Route path="/pathHere" render={(props) => 
   <ExampleComponent {...props} exampleProps={exampleProps} />
}/> 
```

在`render`的例子中，要从`Route`处接收道具，你只需通过剩余的操作员`...`接收道具，就万事俱备了！所有艰难的工作都为你完成了— **太酷了**！

`NavLink`组件非常简洁，因为它与标准的`Link`组件— `activleStyle`或`activeClassName`相比只有一个主要区别。简而言之，`NavLink`接收这些属性中的一个，或者是内联样式，或者是添加到组件中的类名。这些样式仅在该链接被使用/激活时应用。

结果呢？你在你的导航链接上得到一些*花哨的*样式！例如，你可以让`Home`标签只在你在那个页面的时候高亮显示，而`About`标签只在你查看“关于”页面的时候高亮显示——*这让我兴奋不已*！

#### **第三天:React 中的表单管理**

首先，我想说，表单有太多的含义，我不会花整整 8 页来讨论如何在 React 中使用表单，哈哈！在这一节中，我将只讲述一些基本原则。

总之，我们学会了使用`form`、`label`和`input`元素来创建一个漂亮、简单的表单。表单需要两个函数才能起作用:`onChange`和`onSubmit`。根据我的经验，这些函数通常被命名为`handleChange`和`handleSubmit`。`handleChange`负责将 React 状态设置为放置在`input`元素中的值。

```
import React, { useState } from "react";

const Form = props => {

  const [user, setUser] = useState({
    id: '',
    name: '',
    email: ''
  });

  const handleChange = (event) => {
    const { name, value } = event.target;
    setUser({...user, [name]: value});
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    setUser({
      name: '',
      email: ''
    });
  };

  return (
    <form onSubmit={(event) => handleSubmit(event)}>
      <label>
        Name:
        <input
          onChange={(event) => handleChange(event)}
          name="name"
          type="text"
          value={user.name}
         />
     </label>

     <label>
       E-Mail:
       <input
         onChange={(event) => handleChange(event)}
         name="email"
         type="email"
         value={user.email}
       />
     </label>

     <button>Submit!</button>
   </form>
  );
}
export default Form; 
```

我们有一个表单，它有一个`user`状态，保存所有的输入值。一个`handleChange`函数，它将输入字段的值设置到具有处于`user`状态的键-值对的对象。`handleSubmit`只是在提交表单后清除表单。在它下面，是实际呈现的表单。需要注意的一个重要属性是每个输入的`value`。这个`value`属性声明，无论`user`状态的值是什么，都在输入中显示该值。这样你就可以实时查看你输入的内容！

`handleSubmit`负责将数据提交给 API，使用类似于`axios.post()`的函数，同时清除输入字段，并呈现一个新的表单以供使用。我认为`handleSubmit`是对你正在开发的表单的“告别”——*回头见*！

`e.preventDefault();`或`event.preventDefault();`防止页面在您提交表单时重新加载，从而可能丢失您所有的输入值。

#### **第 4 天:使用 Formik 和 Yup 进行高级表单管理**

如果我坐下来，向你解释关于 Formik 和 Yup 的一切，我基本上是在重新教授整堂课——哈哈！这些工具是如此，如此强大，但我会尽我所能保持简洁。

Formik 是一个可以在 React 中使用的库，它本质上使您的表单同样高效，但是消除了大量的额外代码。它有内置的`Form`和`Field`组件，以及大量独特的附加组件，让您的生活更加轻松！我强烈建议看一看 Formik 的能力。我打算更多地使用它！

使用 Yup 进行探索也非常有趣——本质上，它允许您实现表单验证。你知道，那些超级酷的错误信息说，**汪汪汪**！*您的密码不符合要求*。或者，*这是无效的电子邮件格式*！从用户的角度来看，Yup 修饰了你的表单，使它们看起来非常完整。大量的创意和使用 Yup 的可能性。我真的很喜欢了解它！

#### **回顾**

这一周真的增长了不少信息，也确实考验了我的技能。在本周之前，我还没有和任何课程进行过斗争，但本周我肯定会全力以赴！然而，到了周末，我对实施我所学到的东西充满信心。React Router 是一个非常棒的工具，可以快速有效地加载不同的页面。表单是很多网站和 web 应用程序的核心元素，Formik 可以让构建令人惊叹的表单变得非常简单！此外，使用 Yup，你可以增加你的表单的趣味，使它们有组织，干净，有结构，并且容易理解！

#### **最大带走**

随着主题变得越来越深入，越来越难，每天需要几个小时才能完全掌握一个概念的情况并不少见。这一周是一次令人谦卑的经历，让我明白有些概念需要额外的关注才能完全理解。**耐心点**，**尽可能多的练习**。永远实现你所学的，**尝试**打破它。**搞定**。从错误中学习。冲洗，然后重复！