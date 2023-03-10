# 注意:取消注册很重要！

> 原文：<https://dev.to/felipedsc/observables-cancelar-inscricao-unsubscribe-e-importante-51bc>

## 别忘了取消订阅观察者！

当我们订阅了望台时，即使离开创建了望塔的组件，我们仍会继续聆听。当我们创建自己的观测者而不是角度所提供的观测者时，这种情况更为常见，但即使是对于这些观测者，我们也总是在**ngondstroy()**上放弃观测者，这是一种良好的做法。

在此帖子的示例中，我创建了两个链接，它们将在两个组件之间导航:com-observable 和 no-observable。

在 com-observable 组件中，我使用 xjs 的 *interval* 功能，该功能可创建一个在指定时间间隔内递增和输出数字的观测值。我注册了这个观测台，发出的数字将会显示在 stack flash 控制台(在底部栏中展开)的组件**中，我只显示一个文本，告诉您我们在哪里。**

 **请注意，当我们导航到组件“”时，值仍会显示在控制台中！如果回到 **com-observable** 元件，我们有 2 个观测员向控制台输出结果！并且新的观测值会随着我们离开再进入 **com-observable** 元件而积累起来！

**我们可以得出结论，我们的应用程序可能会产生意想不到的结果，变得非常缓慢，甚至会因此而崩溃。**为此，创建一个变量来存储登录并在**【ngondstroy()**中取消登录是至关重要的。

现在，在**com-observable**组件中，尝试从**ngondstroy()**中删除注释代码，然后再次尝试在组件之间导航，看到注册不再累积！
*备注:可能需要更新 stack flash 浏览器。*
[https://stackblitz.com/edit/angular-observables-unsubscribe?embed=1&&file=com-observable.component.ts](https://stackblitz.com/edit/angular-observables-unsubscribe?embed=1&&file=com-observable.component.ts)
如果看不到嵌入式，请点击[这里](https://stackblitz.com/edit/angular-observables-unsubscribe)。**