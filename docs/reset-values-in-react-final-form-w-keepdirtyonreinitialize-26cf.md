# 使用 keepDirtyOnReinitialize 重置 React 最终表单中的值

> 原文：<https://dev.to/jetrockets/reset-values-in-react-final-form-w-keepdirtyonreinitialize-26cf>

**提交后如何在 React 最终表单中用 keepDirtyOnReinitialize 重置值。**

**问题**

如果对表单应用了 keepDirtyOnReinitialize，那么 form.reset()将不再能够删除字段的值。

**解决方案**

解决方案很简单——如果表单提交成功:首先将 keepDirtyOnReinitialize 改为 false ->执行 form reset form.reset() ->并将 keepDirtyOnReinitialize 改回 true。

```
<Form
  onSubmit={onSubmit}
  keepDirtyOnReinitialize
  render={({ handleSubmit, form }) => (
    <form
       onSubmit={(event) => {
       const promise = handleSubmit(event);
       promise && promise.then(() => {
         form.setConfig('keepDirtyOnReinitialize', false);
         form.reset();
         form.setConfig('keepDirtyOnReinitialize', true);
       })
      return promise;
      }}
    >
    ...
   </form> }/> 
```