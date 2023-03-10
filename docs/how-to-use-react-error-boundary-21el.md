# 如何使用 React 错误边界

> 原文：<https://dev.to/maybebored/how-to-use-react-error-boundary-21el>

## 重述

在之前的帖子中，我解释了我是如何发现错误边界的。在这篇文章中，让我们深入一点，理解 React 如何提供一种声明性的方法来捕捉和处理渲染过程中出现的错误。最后，我将展示我是如何使用它的。

## 声明性错误处理

在典型的 JavaScript 代码中，可以使用`try` - `catch`块来捕捉和处理错误。

```
try {
   throw new Error('A new error');
catch (error) {
   console.log('Caught this error', error);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是*命令式*代码，我们告诉程序*如何*做某事，通常是一系列的步骤。当使用 React 时就不是这样了，我们告诉程序*做什么*。

让我们来看看代码示例，我在两种方法的的其他地方找到了[,用于完成改变`button`元素颜色的相同任务](https://codeburst.io/declarative-vs-imperative-programming-a8a7c93d9ad2)

命令式的例子，我们提供了一步一步的指示来改变按钮的颜色。

```
const container = document.getElementById('container');
const btn = document.createElement('button');
btn.className = 'btn red';
btn.onclick = function(event) {
 if (this.classList.contains('red')) {
   this.classList.remove('red');
   this.classList.add('blue');
 } else {
   this.classList.remove('blue');
   this.classList.add('red');
 }
};
container.appendChild(btn); 
```

Enter fullscreen mode Exit fullscreen mode

React 示例，我们处理状态并返回按钮元素。

```
class Button extends React.Component{
  this.state = { 
     color: 'red' 
  }
  handleChange = () => {
    const color = this.state.color === 'red' ? 'blue' : 'red';
    this.setState({ color });
  }
  render() {
    return (
       <div>
         <button 
            className=`btn ${this.state.color}`
            onClick={this.handleChange}>
         </button>
       </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑到程序的当前状态，我们只是告诉 React *返回(显示)什么*。因此在试图渲染一个组件时使用一个`try-catch`块将不会*捕捉到组件中的*错误。React Error Boundary 是一种声明性的错误处理方法。

## 我如何使用 React 错误边界

使用错误边界有助于呈现回退用户界面，这是比空白屏幕更好的 UX，但我们可以做得更好。我们可以重置状态，这样用户就可以回到错误发生前看到的状态，而不必重新加载页面。

fallback UI 可以包含一个按钮，单击该按钮会将组件的状态设置为其初始状态，即`{hasError: false}`。但在此之前，我们必须重置在错误边界内呈现的子组件的状态，以便当应用程序重新呈现时，我们不会处于错误的 T2 状态。下面自己看。

[https://codepen.io/maybebored/embed/OJLvLNq?height=600&default-tab=result&embed-version=2](https://codepen.io/maybebored/embed/OJLvLNq?height=600&default-tab=result&embed-version=2)

## 结论

至此，我结束了关于 React 误差边界的两部分系列。关于重置状态的最后一点只是我的实验，我没有在网上找到任何支持它的东西，所以我很感激任何关于它的反馈。