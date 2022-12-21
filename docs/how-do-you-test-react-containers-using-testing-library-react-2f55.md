# 如何使用@testing-library/react 测试 react 容器

> 原文：<https://dev.to/kaueburiti/how-do-you-test-react-containers-using-testing-library-react-2f55>

@testing-library/react 的目的是测试用户看到的内容，即呈现在 DOM 上的内容。就我所知，这就是图书馆背后的哲学，这很好！

但是我想知道，你们在使用这个库的时候是如何测试 react 容器的？

想象一下下面的情况:

```
const onChange = () => console.log('Hey!')

const Container = () => <Component onChange={onChange} /> 
```

如果测试组件接收 onChange 很重要，你会怎么做？

我看到一些帖子建议模仿组件并添加一个 CSS 类(！)基于它有无道具的事实。但是这闻起来不太好。

这种情况是不是就不要考了？