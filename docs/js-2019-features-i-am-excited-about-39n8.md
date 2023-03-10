# 令我兴奋的 JS 2019 功能

> 原文：<https://dev.to/hozefaj/js-2019-features-i-am-excited-about-39n8>

在过去的几年里，TC39 每年都会更新 JS。这比每隔几年就大量更新要好得多。这给了开发者一个更好地学习的机会，甚至从浏览器的角度使这些特性可用。

从 2019 年的功能集中，下面是我最兴奋的几个。

### 1。`trimStart()`和`trimEnd()`

添加在`String`原型上

这允许修剪字符串开头和结尾的空白。我们已经有了`trim()`函数，但是它在两端都起作用。可能会有这样一个用例，您可能会保留两端的空白。

```
const paddedStr = ' How new trim functions work '
paddedStr.trimStart() // prints 'How new trim functions work '
paddedStr.trimEnd() // prints ' How new trim functions work'
paddedStr.trim() // prints 'How new trim functions work' 
```

Enter fullscreen mode Exit fullscreen mode

### 2。`flat()`

添加在`Array`原型上

这有助于多维数组的扁平化。它带有一个可选参数，告诉函数我们想要展平多少层深度。默认值为 1。

```
const oneLevelNesting = ['bread', ['peanut butter', 'jelly'], 'bread'];
oneLevelNesting.flat() // ['bread', 'peanut butter', 'jelly', 'bread']

const twoLevelNesting = ['bread', [['peanuts', 'oil'], ['strawberries', 'sugar']], 'bread'];
twoLevelNesting.flat() //  ['bread', ['peanuts', 'oil'], ['strawberries', 'sugar'], 'bread']
twoLevelNesting.flat(2) // ['bread', 'peanuts', 'oil', 'strawberries', 'sugar', 'bread'] 
```

Enter fullscreen mode Exit fullscreen mode

### 3。可选`catch`绑定

可选的 catch 绑定允许您编写一个 try/catch，而不必将 error 参数与`catch`一起使用。

```
//before
try {
   doSomething()
} catch(error) {
   console.log("Not using the error parameter here but have to include it")
}
// after
try {
   doSomethingElse()
} catch {
   console.log("No param? No Problem!")
} 
```

Enter fullscreen mode Exit fullscreen mode

除此之外还有其他功能，如[`flapMap()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap)[`Object.fromEntries`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)，还有一个是给`Symbol`属性添加描述。就我个人而言，在我的经验中，我没有发现这些的必要性。

*为了更深入地了解所有这些特性，我推荐观看 egghead.io.* 上的 [`javascript-es2019-in-practice`](https://egghead.io/courses/javascript-es2019-in-practice)

我想听听其他人对他们最感兴趣的功能的看法。