# 你如何处理一次性 IF 语句？

> 原文：<https://dev.to/mazentouati/how-do-you-handle-the-disposable-if-statements-hi6>

你好，

有时你需要写一个条件块，只执行一次，或者只执行一小段时间，然后条件就不再满足了，就像永远不满足一样。

这些 if 的一个例子是

```
 // User level IFs
if (!user.activated) {
 alert('please activate your account');
 return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Application level IFs
if ( ! isAppProperlyInstalled() ) {
  die('Please, check the installation guide...');
} 
```

Enter fullscreen mode Exit fullscreen mode

多年来，驻留代码不再被执行的想法一直困扰着我。我有点挑剔，有时会对微优化着迷，但有时我会忽略它。然而，我觉得有一个我没有意识到的伟大的设计模式可能会让我感觉更好。

你怎么想呢?你有同感吗？你是怎么处理的？

* * *

卡罗琳娜·皮门塔拍摄的照片