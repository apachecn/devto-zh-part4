# 在 Vue/Nuxt/Vuetify 应用程序中截取手机上的后退按钮

> 原文：<https://dev.to/lilianaziolek/intercepting-back-button-on-mobile-in-vue-nuxt-vuetify-apps-222m>

# 问题陈述

OSBO 的第一版并不特别适合手机使用。感谢 Vuetify 和 Nuxt 所做的出色工作，一旦我们开始更加关注移动友好，过渡并不困难，很快我们就有了一个在移动设备上运行良好的页面。

左右我们以为。对“真实用户”的第一次测试向我们表明，当试图关闭全屏弹出窗口时，在手机上点击返回按钮是一种非常强烈的冲动——例如，当我们显示产品的放大图像时。由于我们只是在浏览器中，后退按钮会将用户带到上一页，而不是关闭弹出窗口。这可能非常令人沮丧——你在一个产品页面上，看着产品图片，点击后退——突然你又回到了产品列表页面。我们决定需要拦截 back 按钮，如果有任何弹出窗口打开，就关闭它。简单？

不幸的是，说起来容易做起来难。Javascript 中并没有“监听后退按钮事件”这种东西。

另一个复杂的问题是，我们不希望在桌面上拦截后退按钮——只有在用户可能在触摸屏上的地方——也就是在手机和平板电脑上。

# 设备检测

这是一个相当敏感的话题。不幸的是，仍然没有一个 100%可靠的方法可以同时在服务器端和客户端工作。请记住，我们有 SSR，我们希望立即提供正确的 HTML 在我们到达浏览器并质疑其功能或执行任何聪明的 Javascript 之前。在服务器上，我们只能依靠一个东西——用户代理。是的，我们知道它不是 100%可靠，但似乎没有更好的方法(很高兴被纠正-如果你知道在 SSR 渲染期间检测手机/平板电脑*的更可靠的方法，请随意评论)。*

为了避免多此一举，我们决定使用一个 Nuxt 模块:**[Nuxt-device-detect](https://www.npmjs.com/package/nuxt-device-detect)**。它公开了一组标志，如 isMobile、isTablet、isWindows 等。通过注入到 Nuxt 上下文和 Vue 实例中的对象。因此，根据用户代理，可以调用类似于:`this.$device.isMobileOrTablet`的函数并获得一个真/假值。它在客户端和服务器端都可以工作，这很好。

要将它包含在您的项目中，您只需更改两个文件:

package.json

```
{  "dependencies"  :  {  "nuxt-device-detect":  "~1.1.5"  }  } 
```

nuxt.config.js

```
{
  modules: [
   'nuxt-device-detect',
  ]
} 
```

然后在你的任何一个 vue 文件中，你可以开始拥有条件逻辑，例如:

```
<template>
    <section>
        <div v-if="$device.isMobileOrTablet">
            <touchscreen-navbar></touchscreen-navbar>
        </div>
        <div v-else>
            <desktop-navbar></desktop-navbar>
        </div>
    </section>
</template> 
```

整洁！

# 拦截手机上的后退按钮并关闭弹出框

如前所述，在 Javascript 中没有一个事件可以传达后退按钮被按下的信息。然而，有一个相当简单的解决方法。

1.  我们需要跟踪是否有弹出窗口打开。如果没有弹出窗口打开，我们应该像往常一样，即导航回来。如果打开了任何弹出窗口，而我们在移动/平板电脑上，那么我们不会返回并关闭弹出窗口。
2.  我们需要连接到 Vue 路由器，以获得路由即将更改的通知(返回上一页)。我们可以通过实现`beforeRouteLeave`和/或`beforeRouteUpdate`来实现这一点。当 Vue 路由器触发事件(“我们即将离开当前页面”)时，如果合适，我们可以做出反应并阻止这种情况发生。

我们应该确保不会出现到处重复的类似代码。我们决定使用一个 [eventbus 插件](https://dev.to/lilianaziolek/how-to-build-a-simple-event-bus-in-vue-nuxt-592)和两个补充 mixins 的组合。

## 订阅打开弹出窗口的通知

当页面组件被挂载时，我们订阅任何打开的弹出窗口的通知。如果弹出窗口没有打开，用户按下返回按钮，我们将让应用程序返回。

我们将创建一个 mixin，然后将它导入到任何需要该功能的页面中。

mobileBackButtonPageMixin.js(订阅部分)

```
export const mobileBackButtonPageMixin = {
    data() {
        return {
            dialogOpen: false
        }
    },
    mounted() {
        if (this.$device.isMobileOrTablet) {
            this.$eventBus.$on("dialogOpen", () => {
                this.dialogOpen = true;
            });
            this.$eventBus.$on("dialogClosed", () => {
                this.dialogOpen = false;
            });
        }
    },
    beforeDestroy() {
        //always remember to unsubscribe
        if (this.$device.isMobileOrTablet) {
            this.$eventBus.$off('dialogOpen');
            this.$eventBus.$off('dialogClosed');
        }
    }
    ...
}; 
```

## 从子组件通知对话框打开

在每个可以打开弹出窗口的组件中，我们需要向 eventBus 发送一个通知(`dialogOpen`)，这将允许跟踪是否有任何弹出窗口打开。此外，组件需要订阅`closeAllDialogs`，以便可以发出关闭对话框的请求(当按下移动设备上的后退按钮时)。同样，我们将使用 mixin。

mobilebackbuttondialogcomponentmixin . js

```
export const mobileBackButtonDialogComponentMixin = {
    methods: {
        notifyDialogStateViaEventBus(open) {
            if (open) {
                this.$eventBus.$emit('dialogOpen');
                this.$eventBus.$on("closeAllDialogs", () => {
                    this.closeAllDialogs();
                });
            } else {
                this.$eventBus.$emit('dialogClosed');
                this.$eventBus.$off("closeAllDialogs");
            }
        }
    },
}; 
```

这个 mixin 需要导入到组件:

```
 import {mobileBackButtonDialogComponentMixin} from "@/mixins/mobileBackButtonDialogComponentMixin";

    export default {
    mixins: [mobileBackButtonDialogComponentMixin],
    ...
    } 
```

除此之外，您需要为控制弹出窗口可见性的属性添加一个监视器。例如，如果在模板中你有这样的东西:`<v-dialog v-model="popupVisible">`那么在组件中你需要添加这个:

```
watch: {
    popupVisible: 'notifyDialogStateViaEventBus'
},
methods: {
    closeAllDialogs(){
        this.popupVisible = false;
    }
} 
```

由于我们使用 Vuetify，我们的弹出窗口是 v-dialogue，但是这种技术可以在任何其他组件框架中使用。

请注意，在一个组件中可以有多个弹出窗口——只需添加另一个观察器，并在“closeAllDialogs”方法中将另一个属性设置为 false。

## 拦截后退导航，视情况关闭弹出框

为了获得关于路由更改的通知，我们需要向包含弹出窗口的页面添加两个方法(**重要的** -这必须是一个页面，而不是一个组件)。

mobileBackButtonPageMixin.js(截取部分)

```
export const mobileBackButtonPageMixin = {
    ...
    beforeRouteUpdate(to, from, next) {
        if (this.$device.isMobileOrTablet && this.dialogOpen) {
            this.$eventBus.$emit('closeAllDialogs');
            next(false);
        } else {
            next();
        }
    },
    beforeRouteLeave(to, from, next) {
        if (this.$device.isMobileOrTablet && this.dialogOpen) {
            this.$eventBus.$emit('closeAllDialogs');
            next(false);
        } else {
            next();
        }
    }
}; 
```

一旦`beforeRouteUpdate`和`beforeRouteLeave`挂钩被调用，我们可以检查我们是否应该停止导航到上一页(我们是在移动/平板电脑上吗？我们有打开的弹出窗口吗？).如果我们需要关闭弹出窗口，我们发出一个事件来通知包含弹出窗口的组件(`this.$eventBus.$emit('closeAllDialogs');`)。然后`next(false)`告诉 Nuxt 和 VueRouter 导航不应该发生。Next()告诉它照常进行。

# 总结

这篇文章向你展示了如何截取移动后退按钮导航，并关闭一个对话框。这个例子使用了 Nuxt 和 Vuetify——可以在没有 Nuxt 的情况下应用相同的概念，当然它也可以与 vue tify 之外的组件框架一起工作。

像往常一样，完整的工作代码可以在 [github](https://github.com/lilianaziolek/blog-examples/tree/eventBusAndBackButtonIntercept/dry-examples) 中找到——确保使用`eventBusAndBackButtonIntercept`分支。为了测试它，确保你在加载页面之前切换一个样本手机，例如在 chrome devtools *中，以便发送正确的用户代理。*

附言:弹出菜单里有一个小惊喜，希望你喜欢🐶