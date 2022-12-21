# 使用自定义钩子使表单创建变得轻而易举

> 原文：<https://dev.to/westbrookc16/using-a-custom-hook-to-make-form-creation-a-breeze-3ce6>

在 react 中编写表单可能会很乏味。有表单库可以提供帮助，但这意味着向项目中添加了另一个依赖项，并且可能增加了一层您不需要的复杂性。在这里，我将向您展示如何使用自定义钩子创建一组简单的函数，这些函数可以用于任何表单。我假设你知道 react 的基本知识，但是我会尽可能多的解释来帮助初学者。所以现在开始。

# 什么是钩子？

有些人可能想知道钩子是什么？也许你刚刚开始使用 react，或者你还没有投入到最新最棒的 react 中。简而言之，钩子是一种跨组件共享有状态逻辑的简单方法，不需要使用渲染道具或更高级的组件。这之所以成为可能，是因为现在，react 组件的状态可以存储在函数中，可以很容易地在组件甚至项目之间共享。我强烈建议阅读 [react hooks 文档](https://reactjs.org/docs/hooks-intro.html)了解更多细节。

# 这个东西到底应该做什么？

想想一种形式。您可能有一个带有用户名和密码的登录表单，或者有一个收集订单信息并提交给数据库的表单。表单的哪些部分可以通用于所有表单？所有表单都需要一个对象来存储数据，一个 onChange 事件来捕获更改，一个事件来验证表单输入，一个 onBlur 事件来跟踪输入的状态，还有一个提交事件。它应该将一个处理特定验证的函数、一个处理表单提交的函数和一组表单初始值作为参数。我们开始吧！！

# 设置

首先让我们从基础开始。我们需要从 react 导入 useState 钩子。然后我们需要为我们的钩子设置签名。我将称它为 useForm，但你可以随便叫它什么。请注意，无论您如何称呼它，都必须以单词 use 开头，因为您可以在文档中读到一些关于钩子的规则。创建一个新文件并粘贴下面的代码:

```
import { useState } from 'react';

const useForm = (handleSubmitCallback, validateCallback, initialValues) => { 
```

这里没有什么，我们只是导入了 useState 钩子，然后用我们上面提到的参数设置了一个常量变量，相当于一个 arrow 函数。稍后我们将从这个文件中导出这个常量，这样我们就可以在其他地方使用它。

# 设置状态

现在我们需要设置我们需要的状态变量。这是通过 useState 函数完成的，该函数返回一个包含两项的数组，一个包含状态的变量和一个稍后设置状态值的函数。这些状态变量应该是不言自明的。我已经把它们包括在下面了。

```
const [form, setForm] = useState(initialValues); //for holding initial form data

    const [errors, setErrors] = useState({}); //for validation errors
    const [success, setSuccess] = useState(false); //set to true if form was submitted successfully
    const [submitting, setSubmitting] = useState(false); //set to true when first submitting the form to disable the submit button 
```

```
#setting up touch 
```

我之前提到过，我们需要一个变量来跟踪触摸状态。这对于显示表单错误非常重要。在用户有机会与表单域交互之前，您不希望显示表单域无效。被触摸的变量最初应该具有与表单初始值相同的形状，所有字段都设置为 false [not touched]。下面是代码。

```
 const touchedInitial = {};
        //if the initial values aren't populated than return an empty object.
        if (!form) return {};
        //create a new object using the keys of the form object setting all values to false.
        Object.keys(form).forEach(value => {
            touchedInitial[value] = false;
        });
        return touchedInitial;
    };
    const [touched, setTouched] = useState(setInitialTouched()); 
```

# 设置验证

验证是表单创建中经常被忽略的部分。这里我创建了一个表单验证包装函数，它调用传入钩子的函数，然后将错误状态设置为返回的值，并从自身返回该值。函数返回值和设置状态的原因是因为状态变化不会立即反映出来，所以如果您以后要在更改状态的同一个函数中使用某个值，您需要保留该值的本地副本。我们将在稍后的提交函数中看到这一点。现在这里是验证函数。

```
const validate = () => {
        let e = validateCallback();
        setErrors(e);
        return e;
    }; 
```

# 手柄模糊和手柄变化

如果您在 react 中使用过表单，这两个事件是不言自明的。我使用对象析构来获取事件目标的名称和值，然后相应地在表单对象中设置状态。

```
 const handleChange = e => {
        const { name, value } = e.target; //use destructuring ot get name/value from target for ease of use
        setForm(state => {
            //here we use the spread operator to return the object. This puts the properties of
            //state into a new object and then adds on the newly created value.
            //since properties on the right side of a spread operation always "win", the new value will be returned with the new objecgt.
            return { ...state, [name]: value };
        });
    };
    const handleBlur = e => {
        const { name } = e.target;
        setTouched(c => {
            return { ...c, [name]: true };
        });
        validate();
    }; 
```

# 处理表单提交

老实说，这是钩子中我最努力的部分，也是最需要改进的部分。我让 handleSubmit 成为一个异步函数，因为我传递给钩子的 handleSubmitCallback 函数应该返回一个解析为 true 或 false 的承诺，指示表单提交是否成功。然后，我使用这个返回来设置成功的状态，然后从钩子返回这个状态，这样调用组件就可以做它想做的任何事情，例如重定向到另一个组件，向用户显示一条消息，等等。此外，在提交表单之前，所有字段都被设置为 touched，并对表单进行验证，因此将显示所有表单验证错误。

```
 const handleSubmit = async e => {
        setSubmitting(true);
        //set all fields to touched
        const touchedTrue = {};
        Object.keys(form).forEach(value => {
            touchedTrue[value] = true;
        });
        setTouched(touchedTrue);
        e.preventDefault();
        const err = validate();

        if (Object.keys(err).length === 0) {
            //if there are no errors, set submitting=false and submit form.
            //I am setting submit to false before calling handleSubmitCallback because in my calling component I am performing a redirect with react-router and if I wait until 
            //after I get a warning about trying to set state on an unmounted component.
            setSubmitting(false);
            console.log('no errors.');
            setSuccess(await handleSubmitCallback());
        } else {
            setSubmitting(false);
            setSuccess(false);
        }
    }; 
```

# 包装完毕

现在剩下要做的就是从我的钩子中返回所有东西并导出它。

```
return {
        handleChange,
        handleBlur,
        handleSubmit,
        setForm,
        form,
        errors,
        touched,
        submitting,
        success,
    };
};
export default useForm; 
```

现在调用组件只需要用组件顶层的一行代码调用钩子:

```
const { handleChange, handleSubmit, handleBlur, setForm, form, errors, success, submitting } = useForm(
        handleSubmitCallback,
        validationCallback,
        initialValues
    ); 
```

现在这些函数可以这样使用:
<input type = " text " name = " test " onChange = { handle change }...你明白了。
如果您需要运行特定字段的计算，也可以将这些函数与内联 onBlur 或 onChange 函数结合使用，如下:

```
<input onBlur={e=>{
//do calculations here...
handleBlur(e);
}}/> 
```

如果你有任何改进的建议，请随时提出。这是我第一个真正的大发展，所以我会感谢建设性的批评，我可以如何改进。
这里是整个钩子的源代码:

```
import { useState } from 'react';

const useForm = (handleSubmitCallback, validateCallback, initialValues) => {
    const [form, setForm] = useState(initialValues); //for holding initial form data
    const [errors, setErrors] = useState({}); //for validtion errors
    const [success, setSuccess] = useState(false); //set to true if form was submitted successfully
    const [submitting, setSubmitting] = useState(false); //set to true when first submitting the form to disable the submit button
    //below is a function that creates a touched variable from hte initial values of a form, setting all fields to false (not touched)
    const setInitialTouched = form => {
        const touchedInitial = {};
        //if the initial values aren't populated than return an empty object.
        if (!form) return {};
        //create a new object using the keys of the form object setting alll values to false.
        Object.keys(form).forEach(value => {
            touchedInitial[value] = false;
        });
        return touchedInitial;
    };
    const [touched, setTouched] = useState(setInitialTouched());
    const validate = () => {
        let e = validateCallback();
        setErrors(e);
        return e;
    };
    const handleChange = e => {
        const { name, value } = e.target; //use destructuring ot get name/value from target for ease of use
        setForm(state => {
            //here we use the spread operator to return the object. This puts the properties of
            //state into a new object and then adds on the newly created value.
            //since properties on the right side of a spread operation always "win", the new value will be returned with the new objecgt.
            return { ...state, [name]: value };
        });
    };
    const handleBlur = e => {
        const { name } = e.target;
        setTouched(c => {
            return { ...c, [name]: true };
        });
        validate();
    };
    const handleSubmit = async e => {
        setSubmitting(true);
        //set all fields to touched
        const touchedTrue = {};
        Object.keys(form).forEach(value => {
            touchedTrue[value] = true;
        });
        setTouched(touchedTrue);
        e.preventDefault();
        const err = validate();

        if (Object.keys(err).length === 0) {
            //if there are no errors, set submitting=false and submit form.
            //I am setting submit to false before calling handleSubmitCallback because in my calling component I am performing a redirect with react-router and if I wait until
            //after I get a warning about trying to set state on an unmounted component.
            setSubmitting(false);
            console.log('no errors.');
            setSuccess(await handleSubmitCallback());
        } else {
            setSubmitting(false);
            setSuccess(false);
        }
    };

    return {
        handleChange,
        handleBlur,
        handleSubmit,
        setForm,
        form,
        errors,
        touched,
        submitting,
        success,
    };
};
export default useForm; 
```