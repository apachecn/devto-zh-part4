# 为什么此参数类型不可分配给指定的？

> 原文：<https://dev.to/birowsky/why-is-this-param-type-not-assignable-to-the-designated-2kgo>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [为什么这个 param 类型不能赋给预期？](https://stackoverflow.com/questions/57925619/why-is-this-param-type-not-assignable-to-the-expected)

Sep 13 '19 Comments: 1 Answers: 0[![](img/e3f0373ec76330150a340eacd410b600.png)0![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/57925619/why-is-this-param-type-not-assignable-to-the-expected) </header>

我试图在这里实现[建议](https://stackoverflow.com/a/56775267/592641)，具体来说，这个函数:

```
function eventAssigner<Handlers extends Record<keyof Handlers, (e: any) => any>,
  Discriminant extends keyof any>(
  handlers: Handlers,
  discriminant: Discriminant
) {
  return <Key extends keyof Handlers>(
    event: Record<Discriminant, Key> & (Parameters<Handlers[Key]>[0])
  ): ReturnType<Handlers[Key]> => handlers[event[discriminant]](event);
} 
```

然而，`eventAssigner`拒绝了…

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57925619/why-is-this-param-type-not-assignable-to-the-expected)</button>