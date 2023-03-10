# 如何在 Vuejs / Javascript 中停止 requestAnimationFrame

> 原文：<https://dev.to/coolgoose/how-to-stop-requestanimationframe-in-vuejs-javascript-5f75>

TLDR:

```
let id = window.requestAnimationFrame(fancyFunctionHere)
window.cancelAnimationFrame(id); 
```

Enter fullscreen mode Exit fullscreen mode

现在是更长的版本。从技术上讲，在 Vue.js 中，你可能有使用 window.requestAnimationFrame 的组件/mixin。因为`fancyFunctionHere`被用作回调，所以每次你调用`window.requestAnimationFrame`时，你都会得到一个新的 id，你应该在`destroy`方法上使用它来停止它。

不幸的是，这一点在 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/cancelAnimationFrame) 上并不明确，所以希望我的文档编辑和代码示例中的注释能够顺利完成。

```
{
    created() {
        this.id = window.requestAnimationFrame(
            this.fancyFunctionHere
        );
    },

    destroyed() {
        window.cancelAnimationFrame(this.id);
        this.id = undefined;
    },

    data() {
        return {
            id: undefined
        }
    }

    methods: {
        fancyFunctionHere() {

        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode