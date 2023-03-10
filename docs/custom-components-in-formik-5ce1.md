# Formik 中的自定义组件

> 原文：<https://dev.to/sirech/custom-components-in-formik-5ce1>

[![React is like Angular, but totally different](img/13e7c0008b340addc9ec6de02534d503.png "React is like Angular, but totally different")](///static/96887b08014b3bd3c76a5a5b0a865292/3684f/react.png)

形式和[反应](https://reactjs.org/)。他们在一起不相配吗？如果你正在做任何严肃的*反应*开发，你迟早会构建一个复杂的表单。推出自己的自制框架的诱惑可能会出现，但你必须与之斗争。有很多好的现有解决方案可供选择。

你可以选择[来完成任务。在这种情况下，我想向您展示如何为它构建一个定制的输入组件。](https://jaredpalmer.com/formik/)

## [等等，蚁族？](#wait-formik)

福米克是新的[酷小子](https://jaredpalmer.com/formik/)。引用官方文件:

> 在没有眼泪的情况下做出反应。

我当然分享眼泪的部分。我曾经用 [redux](https://redux.js.org) 构建我的表单，使用 [react-redux-form](https://github.com/davidkpiano/react-redux-form) 。这是一个很好的库，不要误会我的意思，但最终在连接东西时会有很多摩擦。更不用说和政府打交道了。为什么，不管怎样？回过头来看，我在 *Redux* 中的状态似乎并没有给我多大帮助。

福米克采取了一种非常不同的方法。它是声明性的，依靠纯粹的*做出反应*。它使用了[渲染道具](https://reactjs.org/docs/render-props.html)，一种在 *React* 生态系统中迅速传播的模式。说到这里，[这篇文章](https://www.robinwieruch.de/react-render-props-pattern/)最终帮助我更好地理解了他们。

不管怎样，到目前为止我的印象是 *Formik* 不碍事，让你专注于构建你的表单。

## 定制

常规的组件，加上少量的造型，会让你走得很远。它们涵盖了大多数典型的用例。但是如果你想增加情趣呢？

例如，我有一个小应用程序，我想给一个星级，从一到五。我是先用常规的数字输入实现的，感觉没意思。我想点击星星，就像这样:

<figure>[![Everybody wants to be a star](img/48f47292a73ebf6ba8f3042a31ada7c5.png "Everybody wants to be a star")](///static/de712265dbc0134757a2710f7de95326/34ea4/stars.png) 

<figcaption>
息吧！
</figcaption>

</figure>

建造这样的东西时， *Formik* 进展如何？休息之后继续。

## 具象成分

在我们进入实际的表单内容之前，让我们构建`Stars`作为显示组件。我们正在建造一排五星。我们有一个 prop ( `count`)来设置已满的星星的数量，还有一个 handler ( `handleClick`)来知道特定的星星何时被点击。

```
const Stars = ({ count, handleClick }) => (
  <span className={styles.stars}>
    {[...Array(5).keys()].map(i => (
      <Star key={i} isFull={i < count} onClick={() => handleClick(i + 1)} />
    ))}
  </span>
); 
```

组件是一个薄薄的包装，包裹着一个字体很棒的 T2 可点击图标。

## 自定义输入组件

我们终于到了实质性的部分。我们如何让`Stars`组件 *Formik* 意识到？

我们将在一个[字段](https://jaredpalmer.com/formik/docs/api/field)中呈现我们的`Stars`表示组件。它还使用了渲染道具，我们将使用这些道具将它连接到我们的`Stars`。

有一个包含`value`的`field`散列，也就是集合星的数量。这将是对`count`的输入。为了在点击一颗星星后更新这个值，我们在`form`散列中使用了`setFieldValue`函数。这将在内部更新 Formik 上的值。

```
const fieldName = "stars";

const StarsInput = () => (
  <Field name={fieldName} id={fieldName} type="number">
    {({ field: { value }, form: { setFieldValue } }) => (
      <div>
        <label htmlFor={fieldName} className={"label-color"}>
          {fieldName}
        </label>
        <div>
          <Stars
            count={value}
            handleClick={number => setFieldValue(fieldName, number)}
          />
        </div>
      </div>
    )}
  </Field>
); 
```

## 把它整合成一种形式

现在我们已经准备好了自定义输入组件，我们可以在一个常规的 *Formik* 表单中呈现它，我们都准备好了:

```
const MyForm = () => {
  return (
    <section>
      <Formik
        onSubmit={onSubmit}
        initialValues={{ stars }}
      >
        {() => (
          <Form>
            <StarsInput />
            <button type="submit">Submit</button>
          </Form>
        )}
      </Formik>
    </section>
  );
}; 
```

## Codesandbox

您可以在下面的沙箱中使用这个工作实现。查看并扩展它以满足您的需求。

[https://codesandbox.io/embed/laughing-cookies-2t80u](https://codesandbox.io/embed/laughing-cookies-2t80u)