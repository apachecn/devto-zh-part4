# 在 Vue 中创建自定义指令

> 原文：<https://dev.to/ratracegrad/creating-custom-directives-in-vue-58hh>

指令是带有 v 前缀的特殊属性。指令的工作是当它的表达式的值改变时，对 DOM 反应性地应用副作用。Vue.js 提供了大量的指令供您使用。您可能已经使用过 v-if、v-repeat、v-model 和 v-show 指令。

在这篇文章中，我将解释指令的各个部分以及可以使用的内容。然后，我将向您展示如何创建广泛的自定义指令，以便您可以将编程需求直接应用到 DOM 元素。因此，让我们开始讨论指令包含了什么。

## 指令名称

最基本的自定义指令只有一个名称。它不接受任何参数，也没有任何修饰符。如果不传递值，这将不是很灵活，但是您仍然可以拥有 DOM 元素的一些功能。

你可能熟悉的一个例子是指令的最基本版本是 v-else。下面是一个我们即将创建的自定义指令的例子:

`<app-navigation v-sticky></app-navigation>`

## 向指令传递值

您可以将值传递到自定义指令中。这里有一个例子:

`<div v-if="isVisible">Show this</div>`

在此示例中，如果数据属性 isVisible 为 true，则显示 v-if 指令。我们知道这是在寻找一个数据属性，因为它包含在引号中。相反，如果我们想把一个字符串作为值传递给指令，你可以这样做:

`<div v-color="'red'">Show this</div>`

## 论据

自定义指令可以在指令名称后带一个由冒号表示的“参数”。这里有一个例子:

`<app-navigation v-sticky:bottom></app-navigation>`

在上面的例子中，自定义指令的名称是 **sticky** 。这个说法是**底**。

指令只能接受一个参数。

## 修饰语

修饰符是由点表示的特殊后缀，表示指令应该以某种特殊方式绑定。修饰符控制指令的行为。下面是一个自定义指令的例子，我们将在后面创建:

`<span v-format.underline>guide</span>`

在上面的例子中，`.underline`修饰符告诉 v-format 指令对文本应用下划线。

通过将它们链接起来，可以在一条指令上使用多个修饰符。这里有一个例子:

`<span v-format.bold.highlight.underline>guide</span>`

在上面的例子中，文本将被加粗、突出显示并带有下划线。

## 创建自定义指令

现在您已经了解了 Vue.js 中指令的基础知识。除了 core 中提供的默认指令集，Vue 还允许您注册自己的自定义指令。让我们创建自己的自定义指令。

在其基础上，我们可以通过使用`Vue.directive`并给它一个名字来创建一个全局指令。下面是一个使用名字 **sticky** 创建定制指令的例子。

```
Vue.directive('sticky'); 
```

当我们想在一个 DOM 元素上使用这个自定义指令时，它看起来像:

```
<app-navigation v-sticky></app-navigation> 
```

既然我们已经创建了第一个自定义指令，现在我们需要创建它背后的代码。在我们编写代码之前，我们需要理解 Vue 提供给我们在自定义指令中使用的值。

## 挂钩为指示标志

Vue 为定制指令提供了一系列钩子。每个钩子都可以选择几个参数。以下是可用的挂钩:

*   **bind** —当指令附加到元素时，这种情况会出现一次。
*   **inserted** —一旦元素被插入到父 DOM 中，就会出现这种情况
*   **update** —当元素更新了，但是子元素还没有更新时，就会出现这种情况
*   **componentUpdated** —一旦组件和子组件被更新，就会出现这种情况
*   **解除绑定** —一旦指令被删除，就会出现这种情况

其中每一个都有 **el** 、**绑定**和 **vnode** 参数可供它们使用。这些论点是:

*   **el** —绑定所在的元素
*   **binding** —一个包含传入钩子的参数的对象。有许多可用的参数，包括名称、值、旧值、表达式、参数和修饰符。
*   **vnode** —如果需要，允许您直接引用虚拟 DOM 中的节点。

绑定和**虚拟节点**都应该被视为只读。

**update** 和 **componentUpdated** 都公开了另外一个名为 **oldvnode** 的参数。 **oldvnode** 参数用于区分传递的旧值和新值。

**绑定**和**更新**是五个中最有用的。

## 获取代码

如果你想看到所有这些自定义指令的运行，你可以[派生这个我创建的代码沙箱](https://codesandbox.io/embed/my2ppwlk9p?fontsize=14&source=post_page)。我们将创建的所有定制指令都可以在 main.js 文件中找到。只需取消注释演示编号来查看代码。

## 演示#1 v-sticky

如果您在代码沙箱中跟随，这是在 main.js 文件中找到的演示#1。v-sticky 指令已经应用于。当您滚动时，导航在屏幕上保持固定。

让我们编写我们希望 v-sticky 指令具有的行为。当这个指令应用于一个 DOM 元素时，我们希望这个元素固定在屏幕上。下面是我们的 v-sticky 指令的定制代码:

```
 Vue.directive('sticky',
    function(el, binding, vnode) {
        el.style.position = 'fixed';
    }
)); 
```

让我们来分解代码中的内容。我正在使用 Vue.directive 创建一个名为“sticky”的新全局指令。在这个名字之后，我们有一个函数，它有我们之前讨论过的三个参数。在函数中，我获取指令所应用到的元素，并获取它的样式和位置。我把它设置为固定。

稍后，我们将对这个自定义指令应用一个修饰符。

## 演示 2 号 v-橙色

我们将创建的下一个自定义指令是 v-orange。该指令将文本颜色设置为橙色。以下是该指令的代码:

```
 Vue.directive("orange", function(el, binding, vnode) {
    el.style.color = "orange";
}); 
```

我们可以将该指令应用于 HelloWorld 组件中显示的消息。一旦应用，欢迎消息现在是橙色的。

[![](img/1e7646a0dcc14677b3cb843201e16502.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0XiQ1jWn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jenniferbland.com/wp-content/uploads/orangeText.png)

## 演示# 3v-颜色

前面的指令不是很通用。如果您希望文本是蓝色而不是橙色，您将不得不编写另一个自定义指令。我们将创建一个名为 v-color 的新自定义指令。这个自定义指令将接受一个传递给它的值。这个值是我们希望应用于欢迎消息的颜色。

前面我提到过，绑定是一个包含传入指令的参数的对象。该对象中包含的一项是传入的值。我们将在代码中使用它来将文本设置为该值。

```
 Vue.directive("color", function(el, binding, vnode) {
    el.style.color = binding.value;
}); 
```

现在我们的指令灵活多了。您可以传入任何众所周知的颜色字符串，如“红色”或“蓝色”，也可以传入有效的十六进制颜色，如#ffff00。这是我们新的 v-color 指令应用了三次的图像。第一次是红色，第二次是蓝色，最后一次是黄色，使用十六进制代码#ffff00。

[![](img/c5fee5f376429d68dceb6bcef05cf9b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5lFiV4vJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jenniferbland.com/wp-content/uploads/v-color.png)

## 演示# 4v-粘着一个论点

您可以为自定义指令提供参数。我们将修改之前创建的`v-sticky`代码来接受一个参数。大多数网站在屏幕顶部有导航，在屏幕底部有页脚。

我们将使用参数来告诉我们导航应该固定在屏幕的顶部还是底部。绑定对象将包含一个名为 arg 的值，该值包含我们传递到自定义指令中的参数。

为了使事情更简单，如果没有参数传递到指令中，我假设导航应该固定在屏幕的顶部。如果我收到一个参数，那么导航就固定在屏幕的底部。

为了区分顶部和底部导航，我给顶部导航添加了灰色的背景色，给底部导航添加了褐色的背景色。下面是代码:

```
 Vue.directive("sticky", function(el, binding, vnode) {
    const loc = binding.arg === "bottom" ? "bottom" : "top";
    el.style.position = "fixed";
    el.style[loc] = 0;
    if (loc === "bottom") {
        el.style.background = "burlywood";
    } else {
        el.style.background = "#7e7e7e";
    }
}); 
```

在将我们更新的自定义指令应用到导航和页脚之后，它看起来像这样。

[![](img/0dcb433283e05d5bec76e9065f3b6e07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FVFtV5yP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jenniferbland.com/wp-content/uploads/customNav.png)

## 演示# 5v-格式使用修饰符

您可以向自定义指令添加任意数量的修饰符。我们将创建一个名为 format 的新自定义指令。该自定义指令将接受以下一个或多个修饰符:

*   强调
*   大胆的
*   高光

绑定参数的**是一个对象。该对象包含自定义指令的所有修饰符。绑定上的修饰符实际上也是一个对象。该对象将为每个已应用的修改器包含一个关键点。我们将使用它来应用不同的文本格式。下面是代码:** 

```
 Vue.directive("format", function(el, binding, vnode) {
    const modifiers = binding.modifiers;
    if (modifiers.underline) {
        el.style.textDecoration = "underline";
    }
    if (modifiers.bold) {
        el.style.fontWeight = "bold";
    }
    if (modifiers.highlight) {
        el.style.background = "#ffff00";
    }
}); 
```

在上面的代码中，我们获得了 modifiers 对象，并将其赋给名为 modifiers 的变量。然后我们检查我们支持的每一个可能的修改器。如果该修饰符存在，那么我们应用相应的文本修饰。

我们已经将下划线修饰符应用于单词 guide。我们将粗体修饰词应用于单词配置/定制。我已经对单词 check out 应用了高亮修饰符。

为了展示您可以将多个修饰符应用于一个自定义指令，我将所有三个修饰符应用于文本 Installed CLI Plugins。

这是它的样子。

[![](img/99da799c4c91bcb690ff2e2951827f83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q1I_p3M0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jenniferbland.com/wp-content/uploads/plugins.png)

## 演示# 6v-hook-展示生命周期挂钩的演示

前面我谈到了在您的自定义指令中您可以使用的生命周期挂钩。如果您希望您的自定义指令基于生命周期挂钩工作，那么您将需要为您的代码使用不同的格式。您将拥有一个对象，而不是在自定义指令的名称后使用函数。该对象上的键将是一个或多个可用的生命周期挂钩。

在代码沙箱中，我向 About 视图添加了一些代码。代码有一个按钮。当您单击该按钮时，该数字会更新。在 HelloWorld 组件中，我将 v-hook-demo 组件应用于第一个 div。

下面是 v-hook-demo 组件的代码。

```
 Vue.directive("hook-demo", {
    bind(el, binding, vnode) {
        console.log("bind");
    },
    inserted(el, binding, vndoe) {
       console.log("inserted");
    },
   updated(el, binding, vnode) {
       console.log("updated");
   },
   componentUpdated(el, binding, vnode, oldVnode) {
       console.log("componentUpdated");
   }
}); 
```

如果您刷新屏幕并查看您的控制台，您会注意到`bind`和`inserted`生命周期挂钩已经实现。如果您转到“关于”页面并单击按钮，您将看到 componentUpdated 生命周期挂钩已实现。

## 结论

本文向您概述了构成 Vue.js 中指令的项目。在介绍之后，我将带您浏览创建自定义指令的六个示例。这些示例显示了一个基本的自定义指令，一个传递了值的指令，一个使用参数的指令，最后一个使用修饰符的指令。最后一个例子展示了可用的生命周期挂钩。

我希望你喜欢这篇文章。如果您有任何问题或想要留下反馈，请留下您的评论。

## 培训课程

我在我的网站 CodePrep 上创建培训课程。我有关于 Vue、Webpack、Flexbox、函数式编程等等的培训课程。点击这里查看。