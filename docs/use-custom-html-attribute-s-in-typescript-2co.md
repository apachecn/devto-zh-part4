# 在 TypeScript 中使用自定义 HTML 属性

> 原文：<https://dev.to/lukethacoder/use-custom-html-attribute-s-in-typescript-2co>

> 照片由[何](https://unsplash.com/@hnyuuu)在 Unsplash

安装不同的包和框架意味着您会遇到一些关于 TypeScript 的有趣问题。最近的一个例子是在 DOM 元素上使用定制的 HTML 属性。

```
declare module 'react' {
  interface HTMLAttributes<T> extends AriaAttributes, DOMAttributes<T> {
    // extends React's HTMLAttributes
    custom?: string;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码解决了我的问题，并允许您在使用 TypeScript 时添加任何自定义 HTML 属性。

```
<div custom="no_ts_errors">
  your content here
</div> 
```

Enter fullscreen mode Exit fullscreen mode