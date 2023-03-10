# 带 VueJS 的波纹按钮

> 原文：<https://dev.to/takaneichinose/ripple-button-with-vuejs-5240>

这只是一个简单的复古涟漪按钮，带有一些事件和某种控件的 Javascript (VueJS)代码。

我试图重新设计动画，以及我之前做的笔的运动。我试图改进它，但我认为，它仍然需要改进。还是要多学习，重做。

* * *

# 工作原理

### 荡起一片涟漪

下面的源代码，我们可以把一个涟漪的位置。

```
// Get the exact location of the button from the screen.
let pos = el.getBoundingClientRect();

// 'this.ripples' is a looped variable for the ripples element.
this.ripples.push({
    x: e.clientX - pos.left,
    y: e.clientY - pos.top
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 膨胀的涟漪

涟漪的扩展是在 CSS 动画中完成的。

```
.ripple {
    background-color: #ff1ead;
    width: 1rem;
    height: 1rem;
    position: absolute;
    border-radius: 50%;
    /* After we placed the ripple, this code below will put the ripple element at the center of the clicked area */
    transform: translateX(-100%) translateY(-100%);
    mix-blend-mode: screen;
    animation: ripple 1000ms ease-out forwards;
}

/* From original position, we are going to scale the element to 1rem * 50\. It is somehow enormous, and it will occupy the whole space of the button, and then, set the opacity to 0\. */
@keyframes ripple {
    0%   { transform: translate(-100%, -100%); }
    80%  { transform: translate(-100%, -100%) scale(50); }
    100% { transform: translate(-100%, -100%) scale(50); opacity: 0; }
} 
```

Enter fullscreen mode Exit fullscreen mode

### [动画后的](#after-animation)

动画结束后，我们必须破坏元素，这样我们的元素就不会被一个波纹元素淹没。

```
// You will notice the code below at the template of the component.
// v-on:animationend="rippleEnd(i)"
// It is an 'onanimationend' event that will be triggered after the CSS animation was ended.

// Yes, I simply spliced the element from the ripples array.
rippleEnd: function(i) {
    this.ripples.splice(i, 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   关于 [onanimationend](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onanimationend) 。

* * *

# 建议

这种行为还不完美。特别是，当动画结束时，你不能在一秒钟后(确切的动画时间)放任何涟漪。我不知道这是一个错误，还是有什么事情我没有做好。因此，如果你们中的一些人有建议，请在下面评论。非常感谢。

谢谢大家！

[https://codepen.io/takaneichinose/embed/KJVLqm?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/KJVLqm?height=600&default-tab=result&embed-version=2)