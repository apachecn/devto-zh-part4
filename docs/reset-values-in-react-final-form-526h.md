# 以 React 最终形式重置值

> 原文：<https://dev.to/doxomo/reset-values-in-react-final-form-526h>

**提交**后如何重置 React 最终表单中的值

起初我写了下面的代码:

```
<Form
  onSubmit={onSubmit}
  render={({ handleSubmit, form }) => (
    <form
      onSubmit={event => {
        handleSubmit(event).then(() => {
          form.reset();
        })
      }}
    >
    ...
    </form>
  }/> 
```

如果我们实现这样的函数，当我们试图提交无效的表单时，我们将得到一个错误“未捕获的类型错误:无法读取未定义的属性‘then’”。
无效表单-有验证错误的表单。

**解决方案**

为了避免这个错误，我们需要将`handleSubmit(event)`放置到一个变量中，如果这个变量不是未定义的，就调用`.then()`。

```
onSubmit={(event) => {
  const promise = handleSubmit(event);
  promise && promise.then(() => {
    form.reset();
  })
  return promise;
}} 
```