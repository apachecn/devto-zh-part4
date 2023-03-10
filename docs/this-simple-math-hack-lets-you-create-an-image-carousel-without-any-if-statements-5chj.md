# 这个简单的数学技巧可以让你创建一个没有任何 if 语句的图像转盘

> 原文：<https://dev.to/ranewallin/this-simple-math-hack-lets-you-create-an-image-carousel-without-any-if-statements-5chj>

如果你是一名网页开发人员或网页开发学生，你可能在你的职业生涯中至少制作过一个图像转盘。事实上，你可能犯了一些错误。虽然有大量的图像轮播库，但有时您希望(或需要)从头开始制作。

大多数图像传送带是由图像数据阵列制成的。当某个事件触发了改变(超时、按钮点击等)时，当前图像数据被替换为数组中的下一个元素。对于许多人来说，棘手的部分出现在到达数组末尾时。现在怎么办？如果您一直在编写复杂的 If 语句来检查这种情况，我会告诉您有一种更好的方法。

观察下面的代码:

```
const imageData = [ 'image1.png', 'img2.png', 'img3.png' ];
let currentImage = 0;

const handleImageChange = () => {
  currentImage = (currentImage + 1) % imageData.length;
} 
```

就是这样。这就是全部。(下面的解释让基努大吃一惊。)

[![Keanu Reeves looking dumbfounded, or maybe just dumb](img/68743c9625413fac4971bb53ce5ce352.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_G4AhwD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnhnybjm1e9ktzmg2zpr.jpg)

## 工作原理

假设我们有一个包含 10 个元素的数组。模除法(使用`%`运算符时会发生的情况)返回除法的余数。如果`currentImage`是 0，那么`(0 + 1) % 10`就是`1/10`的余数，也就是 1。这是因为我们实际上不能用 1 除以 10，所以剩下的是整个 dang 1。2 - 9 也是如此。这些数都不能被 10 整除，所以这个数本身就是余数。当我们数到 10 时，奇迹就发生了。

因为我们的数组是零索引的，所以没有第十个元素。这对我们有利！当你用一个数除以它自己时，余数是 0，这意味着我们的`currentImage`将被设置为 0。这意味着一旦我们超过了数组的末尾，它就会回到开头。很漂亮，是吧？

在计算机科学中，这被称为圆形数组。数组本身只是一个普通的 ole 数组，但是我们使用这个数学技巧来允许它无限循环。

但是，等等！如果我们想走另一条路呢？别担心，我抓住你了！

我们可以反过来做同样的事情。这个的公式是`(currentValue - 1 + totalElements) % totalElements`。如果我们把它添加到上面的例子中，它可能看起来像这样。

```
const imageData = [ 'image1.png', 'img2.png', 'img3.png' ];
const currentImage = 0;

const handleImageChange = (direction) => {
  if (direction == 'forward')
    currentImage = (currentImage + 1) % imageData.length;
  else
    currentImage = (currentImage - 1 + imageData.length) % imageData.length;
} 
```

我知道，我知道，我说过不会有任何`if`语句，现在也没有，至少不会在元素间来回移动。我们只需要知道往哪个方向走。

这不仅仅是图像传送带的优点。任何时候你需要在数组中一次增加一个元素，这将消除任何检查你是否在末尾的条件。

[迈克尔和雪莉·马丁的封面图片(flickr)](https://www.flickr.com/photos/martinvirtualtours/2589290716/in/photolist-4WNMVo-8meden-4X98cR-npccaY-hMb4JG-CwyER-2auJs4-9fRWVK-evvmG4-9fS2bi-pDp7dm-5rhQu8-8sgGyD-78g8x9-5iRXxZ-98MPyx-3cos9k-dKVhfi-6KqcpM-7hEh3q-8cZX4X-6jPyNV-9MK7A5-7QphnS-dC2Ug7-zmh9j-aqH1Dv-duAefU-puJy5b-5CMBMj-RJTLLv-4mcRoX-2ctQi2a-e1CCBK-Qm7HEq-JEJbAP-KdTura-29Ayn7Y-j1mQi-C6Xc-2bRLJTV-bx2c3J-UpL74g-avkw7C-7QphV9-M5GUc-9fV5f9-98MPKH-2QABwu-7obWa)