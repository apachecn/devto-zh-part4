# React 钩子(intermedian): useStateForm

> 原文：<https://dev.to/miguelromerogutierrez/react-hooks-intermedian-usestateform-4651>

> 注意:确保你了解 react 钩子的基础知识，在不久的将来，我会发布关于这个的帖子。

## 问题😩

你可能知道管理一个表单的状态是一件痛苦的事情，你需要创建一个对象状态，方法来管理输入的变化，并且小心你正在改变的数据。像 Formik 这样的库抽象了状态，并提供了一些有用的组件来处理它。但正如我所知的 Formik 使用起来很复杂，阅读起来也很复杂，有时你需要高阶组件，有时需要 render-prop 组件，但这两个选项都会产生难以理解的代码。

## 解

**使用 React 钩子**😁

### 上下文😮

如果您喜欢 React 的新版本，那么您肯定听说过 React 挂钩，正如文档中所说:

> **钩子允许你在不改变组件层次结构的情况下重用有状态逻辑**

React 挂钩是允许我们*挂钩*组件的状态和呈现阶段的函数，最棒的部分是我们可以创建**定制挂钩**来解决特定的问题。

但是如何使用钩子来管理我们的表单呢？这就是我写这篇文章的原因🤓

### 好了我们开始吧！😅

想想这个问题，首先我们需要将状态管理抽象到一个定制钩子中，这个定制钩子需要让我们访问它的状态，在下面的代码中我们可以看到钩子的一个简单实现来处理状态表单。

```
function MyForm(props) {
  const [formState, setFormState] = useState({...});
  const handleChanges = (event) => {...};

  return (
    ...
    <input
      onChange={handleChanges}
      value={formState.prop1}
      name="prop1"
    />
    ...
  )
} 
```

为了抽象状态，创建一个名为 **useStateForm** 的定制钩子，在那里使用钩子 *useState* 来处理状态并返回它以保持它对组件可见。我们还需要将表单的 initialState 传递给自定义钩子。

```
function useStateForm(initialState) {
  const [formState, setFormState] = useState(initialState);

  return formState
} 
```

酷！状态是在我们自定义钩子中处理的，但是现在我们需要一些 handleChange 实现来改变我们的状态🤔。

```
function useStateForm(initialState) {
  const [formState, setFormState] = useState(initialState);
  const handleChanges = (event) => {
    setFormState({
      ...formState,
      [event.target.name]: event.target.value
    })
  };
  return { formState, handleChanges };
} 
```

厉害！我们的状态是完全抽象的，正如你所看到的，这基本上是同一个组件，没有 render return 语句，这就是钩子的力量，现在我们可以重用公共状态功能，并在我们的组件之间非常容易地共享它，这将使我们节省大量的类型编码。现在我们的组件将看起来像这样:

```
function MyForm(props) {
  const {formState, handleChanges} = useStateForm({ prop1: '' });

  return (
    ...
    <input
      onChange={handleChanges}
      value={formState.prop1}
      name="prop1"
    />
    ...
  )
} 
```

现在我们可以走得更远了，如果不是让程序员负责正确设置输入的名称，而是由我们的自定义钩子来负责这件事呢？验证表单状态怎么样，或者有时我们需要格式化原始数据。你能在我的 github 项目中找到的:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[miguelromegutierrez](https://github.com/miguelromerogutierrez)/[反应-状态-形式](https://github.com/miguelromerogutierrez/react-state-form)