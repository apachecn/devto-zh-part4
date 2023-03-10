# 如何为你的 web 应用程序创建一个圆形菜单？

> 原文：<https://dev.to/victorqribeiro/how-to-create-a-radial-menu-for-your-web-app-d2h>

# 放射状菜单

非常容易设置的高度可定制的圆形菜单。

[![screenshot](img/179864539526816213ec8c48a4ce0617.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---tp7xCpK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xvs5ag2079jfaoklpwkl.png)

在这里观看现场直播[-(如果您在台式机上，请点击右键/在智能手机上按住)](https://victorribeiro.com/radialMenu)

## 关于

为您的 web 应用程序创建一个定制的漂亮的圆形菜单，它可以用作独立的菜单或覆盖默认的上下文菜单。在台式机或笔记本电脑上，单击鼠标的第二个按钮(上下文菜单)。在智能手机或平板电脑上，点击屏幕并按住几秒钟，菜单就会弹出。

随时提交新的样式并与公众分享。

## 文档

让我们创建一个新的单选按钮，这样您就可以看到它有多简单。您需要将 *RadialMenu.js* 添加到您的 web 应用程序中，然后创建一个新菜单。让我们看看代码:

```
<script src="RadialMenu.js"></script>

<script>
const radial = new RadialMenu();
</script> 
```

就是这样。这个库有一些默认的按钮作为例子，所以你可以看到它是如何工作的。让我们继续阅读文档，这样我们可以学习如何用我们自己的按钮创建我们自己的圆形菜单。

要在您的 web 应用程序中创建一个圆形菜单，您需要将一个配置对象传递给构造函数。默认情况下，已经设置了许多值，但是您可以随意更改其中的每一个值。

```
const mySettings = {
    textColor: 'red', //define the color of the text on the buttons
    buttons: [
        {'text': '\uf053', 'action': ()=>{ history.go(-1) } }, //create a button that goes back on history
        {'text': '\uf054', 'action': ()=>{ history.go(1) } }, //create a button tha goes forward on history
    ]
};

const radial = new RadialMenu(mySettings); 
```

获取库，阅读文档并在这里查看代码