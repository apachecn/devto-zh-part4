# 反应钩子:对象陷阱的初始状态

> 原文：<https://dev.to/bowlendev/react-hooks-initial-state-of-object-pitfall-2cpo>

最近，我遇到了 React Hooks 的一个问题，API 调用失败，因为我需要完成的步骤遇到了一些意想不到的后果。

我想要一个对象来表示一系列需要一次完成的步骤。

[![](img/217a255626fb51cfaff741bd54631ba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--97Q0qdeA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vflg0z6bjbok6yqv1lfc.png)T3】

```
const [stepComplete, setStepComplete] = useState({ 
     stepOne: false,
     stepTwo: false,
     stepThree: false,
}); 
```

Enter fullscreen mode Exit fullscreen mode

问题出在我设置步骤的方式上。我需要一切都一样，除了我正在编辑的那一步。我不得不四处寻找一些时间，并遵循一些试错法，直到我想出该怎么做。这些都是没有结果的方法:

```
//Nope
setStepComplete.stepOne(true),
//Doesn't update the whole object and so no re-render!
setStepComplete(stepOne: true),
//This deleted the rest of the Object!
setStepComplete(...setStepComplete, stepOne: true)
Eventually I thought to destructure the object through the use of an arrow function:

setStepComplete(state => ({...state, stepOne: true})); 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。我的代码运行得非常完美！