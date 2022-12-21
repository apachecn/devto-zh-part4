# 使用 Formik 构建更简单的反应表单

> 原文：<https://dev.to/nsebhastian/building-easier-react-forms-with-formik-4hn1>

使用 React 构建表单包括将状态设置为用户数据的容器，将 props 设置为控制状态如何使用用户输入更新的手段。可以在用户输入之间进行验证，并在表单提交时执行任意提交功能。

下面是一个基本的 React 表单示例，它没有使用库，只使用了最少的引导样式:

[https://codesandbox.io/embed/q9r66xl44](https://codesandbox.io/embed/q9r66xl44)

在下面的例子中，我们首先在`constructor`方法中初始化所需的状态值。因为我们有两个必需的输入——`email`和`password`——我们为输入值、输入有效性和输入错误初始化状态:

```
constructor(props) {
  super(props);
  this.state = {
    formValues: {
      email: "",
      password: ""
    },
    formErrors: {
      email: "",
      password: ""
    },
    formValidity: {
      email: false,
      password: false
    },
    isSubmitting: false
  };
} 
```

接下来，我们创建表单的 render 方法，输入值来自 state:

```
render() {
  const { formValues, formErrors, isSubmitting } = this.state;
  return (
    <div className="container">
      <div className="row mb-5">
        <div className="col-lg-12 text-center">
          <h1 className="mt-5">Login Form</h1>
        </div>
      </div>
      <div className="row">
        <div className="col-lg-12">
          <form onSubmit={this.handleSubmit}>
            <div className="form-group">
              <label>Email address</label>
              <input
                type="email"
                name="email"
                className={`form-control ${
                  formErrors.email ? "is-invalid" : ""
                }`}
                placeholder="Enter email"
                onChange={this.handleChange}
                value={formValues.email}
              />
              <div className="invalid-feedback">{formErrors.email}</div>
            </div>
            <div className="form-group">
              <label>Password</label>
              <input
                type="password"
                name="password"
                className={`form-control ${
                  formErrors.password ? "is-invalid" : ""
                }`}
                placeholder="Password"
                onChange={this.handleChange}
                value={formValues.password}
              />
              <div className="invalid-feedback">{formErrors.password}</div>
            </div>
            <button
              type="submit"
              className="btn btn-primary btn-block"
              disabled={isSubmitting}
            >
              {isSubmitting ? "Please wait..." : "Submit"}
            </button>
          </form>
        </div>
      </div>
    </div>
  );
} 
```

现在我们需要编写`handleChange`方法来更新用户输入的状态:

```
handleChange = ({ target }) => {
  const { formValues } = this.state;
  formValues[target.name] = target.value;
  this.setState({ formValues });
  this.handleValidation(target);
}; 
```

每当状态值被更新时，我们将针对用户输入运行一个验证方法。这是我们的`handleValidation`方法:

```
handleValidation = target => {
  const { name, value } = target;
  const fieldValidationErrors = this.state.formErrors;
  const validity = this.state.formValidity;
  const isEmail = name === "email";
  const isPassword = name === "password";
  const emailTest = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/i;
  validity[name] = value.length > 0;
  fieldValidationErrors[name] = validity[name]
    ? ""
    : `${name} is required and cannot be empty`;
  if (validity[name]) {
    if (isEmail) {
      validity[name] = emailTest.test(value);
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be a valid email address`;
    }
    if (isPassword) {
      validity[name] = value.length >= 3;
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be 3 characters minimum`;
    }
  }
  this.setState({
    formErrors: fieldValidationErrors,
    formValidity: validity
  });
}; 
```

这个基本表单的最后一部分是提交过程的`handleSubmit`方法。我们需要检查`formValidity`值，如果有任何`false`值，再次运行验证方法而不提交表单。

```
handleSubmit = event => {
  event.preventDefault();
  this.setState({ isSubmitting: true });
  const { formValues, formValidity } = this.state;
  if (Object.values(formValidity).every(Boolean)) {
    alert("Form is validated! Submitting the form...");
    this.setState({ isSubmitting: false });
  } else {
    for (let key in formValues) {
      let target = {
        name: key,
        value: formValues[key]
      };
      this.handleValidation(target);
    }
    this.setState({ isSubmitting: false });
  }
}; 
```

现在表单已经可以使用了。React 只为您的应用程序提供“视图”层，这意味着它只提供制作表单组件的基本必需品。`component`、`state`和`props`就像拼图块，你必须把它们拼在一起才能构建一个工作表单。

正如您所看到的，对于只有两个文本框的表单来说，这是相当多的代码。想象一下，在一个有 10 个或更多输入的表单中，您需要记录多少状态值。呀！

是的，用 React 制作表单一点也不好玩；很啰嗦，很死板。构建表单和创建验证方法是很无聊的任务。在每个表单中，您至少需要执行以下操作:

1.  设置表单值、表单错误和表单有效性的状态
2.  处理用户输入和更新状态
3.  创建验证函数
4.  处理提交

以自然的“反应”方式构建表单要求您编写从设置状态到表单提交过程的每个部分。我做过无数的 React 表单，我总是觉得构建表单的这一部分非常枯燥和耗时。幸运的是，我不是唯一有这种感觉的人。

## Enter Formik

贾里德·帕尔默出于在构建 React 表单时的挫败感，编写了 Formik 库。他需要一种方法来标准化输入组件和表单提交流程。Formik 帮助您编写构建表单的三个最烦人的部分:

1.  获取表单状态内外的值
2.  验证和错误消息
3.  处理表单提交

这又是一个相同的表单，但这次使用的是 Formik:

[https://codesandbox.io/embed/w63wr2xqq7](https://codesandbox.io/embed/w63wr2xqq7)

这个新表单只使用了 Formik 库中的四个额外组件:`<Formik />`、`<Form />`、`<Field />`和`<ErrorMessage />`。为了释放 Formik 的力量，你可以将你的表单封装在`<Formik />`组件中:

```
<Formik>
  <Form>
    {/* the rest of the code here */}
  </Form> </Formik> 
```

让我们看看与 React 的自然方式相比，Formik 如何使构建表单更容易。

### 获取表格状态的值

Formik 将通过它的`initialValues` prop 在内部设置状态来存储用户输入，因此您不再需要从构造函数初始化状态。

为了获取 Formik 内部状态的值，可以使用`<Field />`组件替换常规的 HTML `<input />`组件。这个组件将完成保持 Formik 状态和输入值同步的魔法，所以你不必将`value`和`onChange`道具传入`<Field />`组件:

```
<Formik
  initialValues={{ email: "", password: "" }}
  onSubmit={({ setSubmitting }) => {
    alert("Form is validated! Submitting the form...");
    setSubmitting(false);
  }}
>
  {() => (
    <Form>
      <div className="form-group">
        <label htmlFor="email">Email</label>
        <Field
          type="email"
          name="email"
          className="form-control"
        />
      </div>
      <div className="form-group">
        <label htmlFor="password">Password</label>
        <Field
          type="password"
          name="password"
          className="form-control"
        />
      </div>
    </Form>
  )}
</Formik> 
```

有了 Formik，就不再需要在`constructor`中初始化状态并创建自己的`handleChange`方法了。一切都搞定了。

### 验证和错误信息

Formik 中的验证会在特定事件期间自动执行。所有常见的事件，比如用户输入之后、焦点改变时和提交时，都包括在内，您不必担心它们。你所需要做的就是将一个函数传递给 Formik 的`validate` prop。

比较 Formik 验证和 vanilla React 验证之间的代码:

```
// Formik validation code. Take values from Formik
validate={values => {
  let errors = {};
  if (values.email === "") {
    errors.email = "Email is required";
  } else if (!emailTest.test(values.email)) {
    errors.email = "Invalid email address format";
  }
  if (values.password === "") {
    errors.password = "Password is required";
  } else if (values.password.length < 3) {
    errors.password = "Password must be 3 characters at minimum";
  }
  return errors;
}}

// Vanilla React validation code. Take values given by handleChange
handleValidation = target => {
  const { name, value } = target;
  const fieldValidationErrors = this.state.formErrors;
  const validity = this.state.formValidity;
  const isEmail = name === "email";
  const isPassword = name === "password";
  const emailTest = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/i;
  validity[name] = value.length > 0;
  fieldValidationErrors[name] = validity[name]
    ? ""
    : `${name} is required and cannot be empty`;
  if (validity[name]) {
    if (isEmail) {
      validity[name] = emailTest.test(value);
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be a valid email address`;
    }
    if (isPassword) {
      validity[name] = value.length >= 3;
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} should be 3 characters minimum`;
    }
  }
  this.setState({
    formErrors: fieldValidationErrors,
    formValidity: validity
  });
}; 
```

验证就绪后，现在需要输出错误消息。Formik 的`<ErrorMessage />`组件将自动显示给定名称的`<Field />`组件的错误信息。你可以通过`component`道具调整显示什么 HTML 标签。因为这个示例表单使用了 Bootstrap 的风格，你还需要添加一个`className`道具:

```
// Formik error message output
<Field
  type="email"
  name="email"
  className={`form-control ${
    touched.email && errors.email ? "is-invalid" : ""
  }`}
/> <ErrorMessage
  component="div"
  name="email"
  className="invalid-feedback"
/>

// Vanilla React error message output
<input
  type="email"
  name="email"
  className={`form-control ${
    formErrors.email ? "is-invalid" : ""
  }`}
  placeholder="Enter email"
  onChange={this.handleChange}
  value={formValues.email}
/> <div className="invalid-feedback">{formErrors.email}</div> 
```

错误消息的代码实际上是一样的，但是 Formik 验证中的代码比 vanilla React 中的少得多。好样的，福米克！

### 有了 Yup 更容易验证

虽然您已经感受到了在验证过程中使用 Formik 的好处，但是您可以通过使用对象模式验证器使它变得更加简单。

对象模式验证器是一个简单的库，它允许您定义 JavaScript 对象的蓝图，并通过验证过程确保对象值与蓝图相匹配。这在验证表单数据时特别有用，因为它实际上是保存在 Formik 的`values` prop 中的一个对象。

现在有一个这样的库是 Yup，Formik 的作者非常喜欢 Yup，以至于他包括了一个特殊的道具来连接 Yup 和 Formik，名为`validationSchema`。这个道具会自动将 Yup 的验证错误转换成一个漂亮的对象，它的键匹配`values`和`touched`。

下面是一个使用 Yup 作为验证模式的 Formik 示例。注意验证属性是如何从`<Formik />`组件中移除的:

[https://codesandbox.io/embed/olql6q2m1q](https://codesandbox.io/embed/olql6q2m1q)

有了 Yup 的对象模式验证器，您不必再手动编写`if`条件。你可以通过[访问它的 GitHub repo](https://github.com/jquense/yup) 来了解更多关于 Yup 和它能做什么样的验证。

### 表单提交流程

Formik 的`<Form />`组件将自动运行您的验证方法，如果有任何错误，将取消提交过程。虽然您必须将 onSubmit 属性包含到常规的`<form />`元素中，但是 Formik 的`<Form />`包装器将运行您传递到`<Formik />`组件中的`onSubmit`属性函数:

```
// Formik's submit code. Won't be executed if there are any errors.
onSubmit={({ setSubmitting }) => {
  alert("Form is validated!");
  setSubmitting(false);
}}

// Vanilla React submit code. Check on validity state then run validation manually.
handleSubmit = event => {
  event.preventDefault();
  this.setState({ isSubmitting: true });
  const { formValues, formValidity } = this.state;
  if (Object.values(formValidity).every(Boolean)) {
    alert("Form is validated!");
    this.setState({ isSubmitting: false });
  } else {
    for (let key in formValues) {
      let target = {
        name: key,
        value: formValues[key]
      };
      this.handleValidation(target);
    }
    this.setState({ isSubmitting: false });
  }
}; 
```

Formik 至少只需要提交四行代码，并且不需要跟踪表单输入的有效性。这真是太棒了！

## 但是 redux-form 呢？

当然， [redux-form](https://github.com/erikras/redux-form) 很好用，但是你首先需要使用 redux。如果你用的是 MobX 呢？如果将来出现一个新的、更好的库，而您想用它取代 Redux，该怎么办？除此之外，您的 React 表单实际上以某种方式影响了整个应用程序的流程吗？

考虑一下:用户名文本框的值对您的应用程序全局是否有影响？如果不是，那么真的没有必要使用 Redux 来跟踪它的值。甚至先知丹·阿布拉莫夫也说了同样的话。

redux-form 的另一个问题是，您将表单输入值存储到 Redux store 中。这意味着您的应用程序将在每次击键时调用 Redux 的 reducer 来更新一个文本框的值。这不是个好主意。

我喜欢以“Formik”的方式编写表单，但是如果您喜欢 redux-form，那也没问题。😉

## 结论

构建表单是 React 不擅长的事情之一。幸运的是，React 有一个开发人员社区，他们互相帮助，使编写代码的过程更加容易。

如果您要在 React 应用程序中编写许多表单，Formik 无疑是必不可少的开源库之一。它通过像`<Field />`和`<Form />`这样的组件抽象出表单的一部分，从而真正加速了您的开发过程并减少了样板代码。

虽然普通的 React 表单要求您指定自己的状态值和方法，但是您可以简单地将 props 传递给`<Formik />`组件来做同样的事情:处理用户输入、验证输入和表单提交。

如果您想了解更多关于 Formik 的信息，请前往文档或观看其创建者的演示。

[https://www.youtube.com/embed/oiNtnehlaTo](https://www.youtube.com/embed/oiNtnehlaTo)

## 反应蒸馏出 2.0 被释放

如果你想了解更多关于 React 的知识，以及如何使用它从头开始构建一个完整的 web 应用程序，我将为我的书[React district](https://gumroad.com/l/react-distilled/release2)提供 28%的折扣，以庆祝它的发布(从 49 美元到 34 美元)。

它包括关于 React 上下文 API 和 React 挂钩的新章节，并展示了如何使用 React 和 Firestore 创建 React 应用程序。

现在就去[抓住它](https://gumroad.com/l/react-distilled/release2)这样你今天就能成为一个反应天才！

[![1_VEWXDp2iflV30wSyBBfEPA.png](img/8bb9c0dd330c03f36998646e9dcdcc6a.png)](https://gumroad.com/l/react-distilled/release2)

* * *

最初发表于 2019 年 6 月 28 日[https://blog.logrocket.com](https://blog.logrocket.com)。