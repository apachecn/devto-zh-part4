# 如何修复悬停时粗体元素移动的问题

> 原文：<https://dev.to/mingyena/fix-inline-elements-shifting-issue-in-bold-on-hover-4fb>

使用`&:hover{ font-weight: bold }`是 FE 开发人员的常识。但是当我们使用`bold`时，字体会向右移动。有时它会导致断行或布局问题。

这里有两个*仅 css 的*解决方案:

## 1 `text-shadow`

```
text-shadow: 0 0 1px black;
text-shadow: 0 0 1px black, 0 0 1px black; //bolder 
```

Enter fullscreen mode Exit fullscreen mode

1.  值= X 坐标
2.  值= Y 坐标
3.  值=模糊半径
4.  值=阴影的颜色

作为`bold`的替代解决方案，第一和第二值可以设置为 0。

***注意***
*Safari 不支持十进制数，也就是说必须是 1px。*
*火狐对模糊半径很敏感，1px 太多*

## 2 `text-stroke`

```
text-stroke: 1px black;
-webkit-text-stroke: 1px black; //Firefox and Edge 
```

Enter fullscreen mode Exit fullscreen mode

***注意***
*[IE 不支持文字笔画](https://caniuse.com/#feat=text-stroke)*

## 3 `Conclusion`

### `text-stroke`

没有 IE 似乎是最好的解决方案。这是我能找到的包含 IE 的最佳解决方案。
`@media all and (-ms-high-contrast: none), (-ms-high-contrast: active)`可以探测 IE 悬停。

```
@mixin m-hover-ie {
  @media all and (-ms-high-contrast: none), (-ms-high-contrast: active) {
     @content;
  }
 a:hover{
        text-stroke: 1px black;
        -webkit-text-stroke: 1px black;
    }
     @include m-hover-ie{
       &:hover{
        text-shadow: 0 0 1px black; 
       }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### `text-shadow`

如果你真的想在 IE 中使用文本阴影...
`-moz-touch-enabled`可以探测火狐悬停。[但这不是一个标准功能，不会对每个用户都有效。](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/-moz-touch-enabled)
以我个人经验，在 Firefox v60 及以上版本上工作。

```
@mixin m-hover-firefox {
    @media (-moz-touch-enabled: 0){
        @content;
    }
}
a:hover{
      text-shadow: 0 0 1px;
      @include m-hover-firefox{
        text-shadow: 0 0 .2px;
      }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/mingyena/embed/pozYmrJ?height=600&default-tab=result&embed-version=2](https://codepen.io/mingyena/embed/pozYmrJ?height=600&default-tab=result&embed-version=2)