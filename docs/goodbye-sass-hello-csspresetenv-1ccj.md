# 再见萨斯，你好 CssPresetEnv

> 原文：<https://dev.to/galvez/goodbye-sass-hello-csspresetenv-1ccj>

很长一段时间，我参与的每个项目都依赖于`node-sass`和`sass-loader`。CSS 本身不允许嵌套规则，这一直困扰着我。最近我得知你可以通过`nuxt.config.js`访问 Nuxt 中的 [stage 0 css-preset-env](https://preset-env.cssdb.org/features#nesting-rules) 特性:

```
export default {
  build: {
    postcss: {
      preset: {
        stage: 0
      }
    }
  }
} 
```

现在 Nuxt 中的`<style>`块可以读作:

```
<style>
& .content {
  flex-basis: 75%;
  padding: 20px;
  & h3 {
    user-select: none;
    & span {
      color: #2f495e !important;
      text-shadow: 1px 1px #fff;
      margin-bottom: 0px;
      border-bottom: 2px solid #2f495e;
    }
    &:not(:first-of-type) {
      margin-top: 50px;
    }
  }
}
</style> 
```

注意，语法[有点不同](https://tabatkins.github.io/specs/css-nesting/)(当前的语法荧光笔不能真正找到它):你总是需要使用嵌套选择器(`&`)，但除此之外感觉都差不多。

还有很多其他的 [css-preset-env 好东西](https://preset-env.cssdb.org/features)可以使用。

一定要检查他们！