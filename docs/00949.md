# javascript 函数中处理必需参数的不同方式

> 原文：<https://dev.to/duranenmanuel/a-different-way-of-handling-required-arguments-in-javascript-functions-2n57>

*原贴于[Enmascript.com](https://enmascript.com/articles/2019/09/22/a-different-way-of-handling-required-arguments-in-javascript-functions)T3】*

<mark>**#你知道吗**是一个新的英语脚本系列，我们对我们认为可能相关或被低估的主题进行简短的解释，如果你正在寻找更完整的深入文章，请查看我们的 **[其他系列](https://dev.to/series)** 。</mark>

通常在 javascript 中，如果你想验证需要的参数，你可以做类似这样的事情:

```
function Person(water, food, candy) {
    if (!water || !food) {
        throw new Error('water and food are required for Person');
    }

    // Do something with water and food...
} 
```

如果没有提供`water`或`food`，上面的构造函数`Person`将抛出一个错误，这是一种相对常见和流行的验证参数的方式，但是有一种不同的更实用的方式来实现这一点。

创建一个抛出错误的帮助函数:

```
const required = name => {
    throw new Error(`Parameter ${name} is required`);
}; 
```

在上面的例子中，我们将参数`name`传递给函数，因为打印所需参数的名称感觉更自然。

然后我们这样用:

```
function Person(
    water = required('water'),
    food = required('food'),
    candy
) {
    // Do something with water and food
} 
```

这里发生了什么事？这种验证是如何进行的？很直接:

如果我们没有传递所需的参数值，那么执行`required`函数会抛出一个错误，并在每次调用该函数时强制用户传递每个强制值。

将参数名传递给`required`函数只是一种偏好；我们可以决定不通过它，让 helper 函数更简单，这是个人的选择。

这种方法不限于这种实现，我们可以使用相同的原理来创建我们自己的参数验证器，并在我们的函数中重用，以增强一致性和安全性。

你有和这个话题相似或相关的想法吗？[与我们分享](https://www.reddit.com/r/javascript/comments/d7pvw7/a_different_way_of_handling_required_arguments_in/)。