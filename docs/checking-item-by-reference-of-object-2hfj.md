# 通过对象的引用检查项目

> 原文：<https://dev.to/francajosejunior/checking-item-by-reference-of-object-2hfj>

今天，我意识到我不需要在一个对象中使用 id 属性来检查我想要在一个数组中改变什么项目...我只是通过对象的引用，当我做了严格的比较...😱

```
this.state.list.map(x => {
   // checking item by ref
   if (x === item) {
      return { ...item, count: (item.count || 0) + 1 };
   }
   return x;
}) 
```

有用！！！

我不知道它是否会影响性能，但我认为它很容易编码。

你认为这种方法怎么样？

请参见 CodeSandbox 上的

[https://codesandbox.io/embed/qk2gk](https://codesandbox.io/embed/qk2gk)