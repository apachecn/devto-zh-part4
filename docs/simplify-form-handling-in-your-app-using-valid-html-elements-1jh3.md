# 使用有效的 HTML 元素简化应用程序中的表单处理

> 原文：<https://dev.to/davidwells/simplify-form-handling-in-your-app-using-valid-html-elements-1jh3>

> 最初发布于 [davidwells.io](https://davidwells.io/blog/simple-forms)

如果你的网站或应用做了一些事情(任何事情)，你很可能在某个地方有这样的流程:

1.  用户在表单中输入信息
2.  运行输入验证(可选)
3.  用户提交表单
4.  数据发送到后端进行处理🚀

为了处理这个流程，我们需要在代码中处理表单输入。

很多时候，在 JS 疲劳的土地上，我看到开发人员跳到框架特定的库或更大的“表单状态”解决方案来解决表单处理。

有很多很多的库可以解决表单问题 [formsy React](https://github.com/christianalfoni/formsy-react) ， [redux-forms](https://redux-form.com/8.2.2/) ， [final-form](https://github.com/final-form/final-form) ， [formik](https://jaredpalmer.com/formik/) ，不胜枚举...

> 旁白:这些库提供了许多特性&支持各种边缘情况等。需要就用吧！感谢各自开源作者付出的时间和精力🎉

但是...我总是想知道为什么人们不使用简单的 HTML 表单&从 DOM 中获取值。

根据我构建大型 web 应用程序的经验，这些更大的**“表单状态”**解决方案已经超出了我的需要。

这篇文章是关于使用简单有效的 HTML 表单输入进行表单处理的。

这种方法适用于任何框架或普通的 HTML。

## 为什么你没有 HTML？

HTML 表单在网络出现的时候就已经存在了。这些输入节点经过了实战测试，运行良好。在这篇文章中，我们将使用它们来获取表单值，并做一些有趣的应用程序工作。

使用原生的[表单事件监听器](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit_event)可以很好地解决这个问题，但是开发人员围绕 DOM APIs 的人机工程学以及从不同类型的输入中获取值是一个棘手的问题。

```
<html>
  <body>
    <!-- The awesome app form -->
    <form id="baz" action="/" method="POST">
      <label>
        <span>Email address</span>
        <input name="email"type="email" required />
      </label>

      <button type="submit" name="submit" class="button">
        Submit Form
      </button>
    </form>

    <!-- The codez -->
    <script>
    /* Get form selector */
    const form = document.querySelector('form[id=baz]')

    /* Make awesome app logic function */
    function doStuff(event) {
      // 1\. Get event.target
      // 2\. Loop over DOM nodes
      // 3\. Get current node values
      // 4\. Do app logic
    }

    // Attach the listener
    form.addEventListener('submit', doStuff, false)

    // Lastly Remember to remove listener if in SPA
    form.removeEventListener('submit', doStuff, false)
    </script>
  </body>
</html> 
```

上面的代码还不错，但是遍历表单输入并从不同类型的表单元素中获取值可能会很麻烦。

如果我们需要附加到多个表单上呢？逻辑开始变得合理。

## 怎样才能简化？

有两种方法可以解决这个问题。

### 1。使用获取表单数据

第一种是使用 [get-form-data](https://github.com/insin/get-form-data) ，它非常小，可以让你从一个有效的表单元素中获取所有的值。

```
const form = document.querySelector('form[id=baz]')

/* Make awesome app logic function */
function doStuff(event) {
  const data = getFormData(form)
  console.log(JSON.stringify(data))
  // Do stuff with the form data
}

// Attach the listener
form.addEventListener('submit', doStuff, false) 
```

它超级简单，在[反应](https://github.com/insin/react-auto-form)中运行良好。

### 2。使用分析工具表单

第二个，是[表单实用程序库](https://github.com/DavidWells/analytics/tree/master/packages/analytics-util-forms)。这进一步将事件监听器附加到一个或多个表单上。

Form utilities 是一个很小的库(2.6kB)，用于从有效的 HTML 表单中读取值。我创建了这个表单——utils 库是为了在一些即将到来的 [**分析插件**](https://getanalytics.io/plugins) 中使用。

[表单实用程序库](https://www.npmjs.com/package/analytics-util-forms)公开了这些方法`onSubmit`、`onChange`、&、`listen`。

您可以监听单个的输入更改，或者完整的“用户点击了提交按钮”事件。

查看示例 HTML，了解如何使用:

*   [示例 HTML 一](https://github.com/DavidWells/analytics/blob/master/packages/analytics-util-forms/examples/all-forms.html)
*   [示例 HTML 二](https://github.com/DavidWells/analytics/blob/master/packages/analytics-util-forms/examples/single.html)

并继续阅读以了解 API。

### 用 form-utils 监听表单提交

听取提交的表格，做一些有输入的事情。

这将接收表单提交，并在正常提交表单之前触发一个自定义回调。

```
import { onSubmit } from 'analytic-util-forms'

// Add to single form
const formOne = document.querySelector("form[id=one]")
onSubmit(formOne, (event, data) => {
  console.log('form', event.target)
  console.log('form data', JSON.stringify(data, null, 2))
})

// Add to single form with options
onSubmit('form[id=two]', {
  /* Turn on debug to disable submissions and see values */
  debug: true,
  /* Turn off sensitive values filter */
  disableFilter: false,
  //* // Exclude field by name or regex pattern of name attribute
  excludeFields: [
    /private/,
    'shhhh'
  ],
  /* Custom filter function. Return false to exclude data */
  filter: (fieldName, value) => {
    if (fieldName === 'hello') {
      return false
    }
    // credit card number
    if (value.match(/^\d{4}[- ]?\d{4}[- ]?\d{4}[- ]?\d{4}$/)) {
      return false
    }
    return true
  }
}, (event, data) => {
  console.log('form', event.target)
  console.log('form data', JSON.stringify(data, null, 2))
})

// Remove onSubmit listener
const cleanUpFuntion = onSubmit('form[id=three]', (event, data) => {
  console.log('form', event.target)
  console.log('form data', JSON.stringify(data, null, 2))
})
cleanUpFuntion() // <-- call function to clean up listener

// Listen to all forms on page
onSubmit('all', (event, data) => {
  console.log('form', event.target)
  console.log('form data', JSON.stringify(data, null, 2))
}) 
```

### 用 form-utils 监听表单域的变化

倾听形式的变化，用输入做一些事情。

```
import { onChange } from 'analytic-util-forms'

// Add to single form with no options
const formOne = document.querySelector("form[id=one]")
onChange(formOne, (event, data) => {
  console.log('form', event.target)
  console.log('form data', JSON.stringify(data, null, 2))
})

// Add to single form with options
onChange('form[id=two]', {
  /* Turn on debug to disable submissions and see values */
  debug: true,
  /* Turn off sensitive values filter */
  disableFilter: false,
  //* // Exclude field by name or regex pattern of name attribute
  excludeFields: [
    /private/,
    'shhhh'
  ],
  /* Custom filter function. Return false to exclude data */
  filter: (fieldName, value) => {
    if (fieldName === 'hello') {
      return false
    }
    // credit card number
    if (value.match(/^\d{4}[- ]?\d{4}[- ]?\d{4}[- ]?\d{4}$/)) {
      return false
    }
    return true
  }
}, (event, data) => {
  console.log('form', event.target)
  console.log('change data', JSON.stringify(data, null, 2))
})

// Remove onChange listener
const cleanUpFuntion = onChange('form[id=three]', (event, data) => {
  console.log('form', event.target)
  console.log('change data', JSON.stringify(data, null, 2))
})
cleanUpFuntion() // <-- call function to clean up listener

// Listen to all forms on page
onChange('all', (event, data) => {
  console.log('form', event.target)
  console.log('form data', JSON.stringify(data, null, 2))
}) 
```

### 听提交&用表格改变事件

Listen 会将`onChange` & `onSubmit`监听器附加到表单

```
import { listen } from 'analytic-util-forms'

// Add to single form with no options
const formOne = document.querySelector("form[id=one]")
listen(formOne, (event, data, type) => {
  console.log('listen type', type)
  console.log('listen form', event.target)
  console.log('listen form data', JSON.stringify(data, null, 2))
})

// Listen to all forms with options
listen({
  /* Turn on debug to disable submissions and see values */
  debug: true,
  /* Turn off sensitive values filter */
  disableFilter: false,
  /* Custom functionality handler for onSubmit */
  onSubmit: (event, data) => {
    console.log('submit form', event.target)
    console.log('submit data', JSON.stringify(data, null, 2))
  },
  onChange: (event, data) => {
    console.log('change form', event.target)
    console.log('change data', JSON.stringify(data, null, 2))
  },
  /* Include only specific forms. This negates 'all'
  includeForms: [
    'form[id=content-form]',
    window.document.forms[1]
  ],
  /**/
  /* Exclude forms by selectors or node.
  excludeForms: [
    'form[name=two]',
    window.document.forms[2]
  ],
  /**/
  //* // Exclude field by name or regex pattern of name attribute
  excludeFields: [
    /private/,
    'shhhh'
  ],
  /**/
  //* // Custom filter function
  filter: (fieldName, value) => {
    if (fieldName === 'hello') {
      return false
    }
    // credit card number
    if (value.match(/^\d{4}[- ]?\d{4}[- ]?\d{4}[- ]?\d{4}$/)) {
      return false
    }
    return true
  }
  /**/
}) 
```

## 安装

要在项目中安装表单助手，请运行

```
npm install analytics-util-forms 
```

## 保持小事情

为了使包的尺寸尽可能的小，我总是根据不同的包装尺寸来检查 bundlephobia。

*   [https://bundlephobia.com/result?p=final-form](https://bundlephobia.com/result?p=final-form)
*   [https://bundlephobia.com/result?p=redux-form](https://bundlephobia.com/result?p=redux-form)
*   [https://bundlephobia.com/result?p=formik](https://bundlephobia.com/result?p=formik)
*   [https://bundlephobia.com/result?p=formsy-react](https://bundlephobia.com/result?p=formsy-react)
*   [https://bundlephobia.com/result?p=analytics-util-forms](https://bundlephobia.com/result?p=analytics-util-forms)

尽可能把事情做得小，并相应地对依赖关系进行预算！

## 包装完毕

如何处理表单值最终取决于您自己。如果其他库或抽象为你工作，太好了！继续前进🚚。它们处理额外的事情，比如验证、不干净的输入和一堆其他用例。

道具给 [insin](https://github.com/insin) ，给 [get-form-data](https://github.com/insin/get-form-data) 库灵感和`analytics-util-forms`这个引擎盖下使用。