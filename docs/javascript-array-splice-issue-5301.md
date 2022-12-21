# JavaScript 数组拼接问题

> 原文：<https://dev.to/rfornal/javascript-array-splice-issue-5301>

这是我今天突然想到的一个问题:我从一个数组中拼接出一个名字，却得到了错误的结果。这个问题很简单，我花了 ***几个小时才找到*** 发生了什么。

现在我已经看到答案了，很明显...当我看到这个问题时，至少可以说是令人沮丧的。

找到一个好的方法来查询这个问题(找不到好的搜索词)促使我写了这篇文章。

我写了下面的代码...

```
triggerDelete: async (name) => {
  let stored = ['one', 'two', 'three', 'four', 'five'];
  stored = stored.splice(stored.indexOf(name), 1);
  return stored;
} 
```

Enter fullscreen mode Exit fullscreen mode

我简化了一些。问题很简单，当我传入**' two '**时，返回的数组是...

```
triggerDelete('two');

/*
 * returns ['two'], not ['one', 'three', 'four', 'five']
 */ 
```

Enter fullscreen mode Exit fullscreen mode

我期望 **['一'，'三'，'四'，'五']【T1]是返回的数组。**

...经过两个小时的寻找，最终要求第二双眼睛，解决方案是...

```
triggerDelete: async (name) => {
  let stored = ['one', 'two', 'three', 'four', 'five'];
  stored.splice(stored.indexOf(name), 1);
  return stored;
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，问题是我没有改变数组，我把**拼接**的结果赋回到**存储的**变量中；于是，**[‘二’]**...

希望这篇文章能让其他人从我的痛苦中解脱出来！