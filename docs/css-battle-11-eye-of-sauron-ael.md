# CSS 战#11 -索伦之眼

> 原文：<https://dev.to/pheeria/css-battle-11-eye-of-sauron-ael>

我必须承认，完成事情很难。你满怀热情地并行开始了许多事情，然后意识到你一件也完成不了。[这次](https://cssbattle.dev/play/11)我要忍。只剩下两个，*如果只考虑第一场战斗的话。顺便说一句，非常感谢库沙拉·古尔&库沙拉·阿加沃定期添加新的战斗！*

## 1。初始解

这一次，让我们试着逐步改变解决方案的设计，就像您在实际开发中所做的那样。
下面是第一次是怎么解决的。三个 div，各有不同风格，相对`body`绝对定位。

```
<div id="l"></div>
<div id="c"></div>
<div id="r"></div>
<style>
  body {
    background: #191210;
    margin: 0;
    position: relative;
  }
  #l {
    width: 60px;
    height: 30px;
    border-bottom-left-radius: 50px;
    border-bottom-right-radius: 50px;
    border: 20px solid #ECA03D;
    border-top: 0;
    z-index: 1;
    position: absolute;
    top: 50%;
    left: 50%;
    margin: 0 0 0 -150px;
  }
  #c {
    width: 100px;
    height: 100px;
    background: repeating-radial-gradient(circle at 50%, #84271C, #84271C 35%, #191210 35%, #191210 100%);
    border: 20px solid #ECA03D;
    border-radius: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    margin: -70px 0 0 -70px;
  }
  #r {
    width: 60px;
    height: 30px;
    border-top-left-radius: 50px;
    border-top-right-radius: 50px;
    border: 20px solid #ECA03D;
    border-bottom: 0;
    z-index: 1;
    position: absolute;
    top: 50%;
    left: 50%;
    margin: -50px 0 0 50px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。定位

因为我们定位的所有 div 绝对是我们零利润的冗余。此外，当我们不再有边距时，我们可以使用通配符，而不是使用`body`。这也有利于我们将所有元素的所有定位减少到`position: fixed`。

```
<style>
  * {   // body -> *
    // position: relative;
    position: fixed;
  }
  #l {
    // position: absolute;
  }
  #c {
    // position: absolute;
  }
  #r {
    // position: absolute;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 3。共享位置

`top`和`left`也可以移到通配符中，因为我们对每个元素都使用它们。

```
<style>
  * {
    top: 50%;
    left: 50%;
  }
  #l {
    // top: 50%;
    // left: 50%;
  }
  #c {
    // top: 50%;
    // left: 50%;
  }
  #r {
    // top: 50%;
    // left: 50%;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 4。没有 z 索引

我们其实不需要`z-index`。有时候我加的东西，看起来是对的，尽管没有必要。让我们删除它，因为它只会导致混乱，无论谁看它。

```
<style>
  #l {
    // z-index: 1;
  }
  #c {
    // z-index: 1;
  }
  #r {
    // z-index: 1;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 5。统一边框半径

而不是写出四种可能的边界半径(我可以取悦两种复数类型的支持者吗？)，我们可以用一个有四个值的简写。

```
<style>
  #l {
    // border-bottom-left-radius: 50px;
    // border-bottom-right-radius: 50px;
    border-radius: 0 0 50px 50px;
  }
  #r {
    // border-top-left-radius: 50px;
    // border-top-right-radius: 50px;
    border-radius: 50px 50px 0 0;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 6。共享边框

由于所有元素的边框也是相同的，我们也可以将它们移到通配符中。

```
<style>
  * {
    border: 20px solid #ECA03D;
  }
  #l {
    // border: 20px solid #ECA03D;
  }
  #c {
    // border: 20px solid #ECA03D;
  }
  #r {
    // border: 20px solid #ECA03D;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 7。简化背景

对于中圈背景，其实不需要赘述。一次就够了。

```
<style>
  #c {
    // background: repeating-radial-gradient(circle at 50%, #84271C, #84271C 35%, #191210 35%, #191210 100%);
    background: radial-gradient(#84271C 35%, #191210 35%);
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 8。辐状的

圆圈的左右两边是一样的。我们只需要旋转一个，把它放在不同的位置，就可以得到第二个。所以我们可以创建一个名为`half`的公共类，并将`l`和`r`转换成类。现在整个外观将由`half`把持，另外两个只会放置和旋转。让我们再看一遍整个代码。

```
<div class="half l"></div>
<div id="c"></div>
<div class="half r"></div>
<style>
  * {
    background: #191210;
    position: fixed;
    top: 50%;
    left: 50%;
    border: 20px solid #ECA03D;
  }
  .half {
    width: 60px;
    height: 30px;
    border-radius: 0 0 50px 50px;
    border-top: 0;
  }
  .l {
    margin: 0 0 0 -150px;
  }
  #c {
    width: 100px;
    height: 100px;
    background: radial-gradient(#84271C 35%, #191210 35%);
    border-radius: 50%;
    margin: -70px 0 0 -70px;
  }
  .r {
    transform: rotate(180deg);
    margin: -50px 0 0 50px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 9。移除边距

我们使用固定的位置，所以边距只会打乱我们的布局。它们崩溃，它们相互影响。
我们最好更精确地设置`top`和`left`。

```
<div class="half l"></div>
<div id="c"></div>
<div class="half r"></div>
<style>
  * {
    background: #191210;
    position: fixed;
  }
  .half {
    width: 60px;
    height: 30px;
    border-radius: 0 0 50px 50px;
    border: 20px solid #ECA03D;
    border-top: 0;
  }
  .l {
    top: 50%;
    left: 50px;
  }
  #c {
    width: 100px;
    height: 100px;
    background: radial-gradient(#84271C 35%, #191210 35%);
    border: 20px solid #ECA03D;
    border-radius: 50%;
    top: 80px;
    left: 130px;
  }
  .r {
    transform: rotate(180deg);
    top: 100px;
    left: 250px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 10。一些相对论

在读了[Lea Verou 的书][2]的几章后，我对相对单位着迷了。当然，你知道相对比绝对好。但是当编写 JSS 模块时，很容易忽略它们的功能，而倾向于简单。因此，我们可以将字体大小设置为 10 个像素，这将等于一个 em。所有其他测量值都绑定到它作为参考。因此，无论何时我们在一个地方改变字体大小，整个设计都会优雅地缩放。

```
<div class="half l"></div>
<div id="c"></div>
<div class="half r"></div>
<style>
  * {
    background: #191210;
    position: fixed;
    font-size: 10px;
  }
  .half {
    width: 6em;
    height: 3em;
    border-radius: 0 0 5em 5em;
    border: 2em solid #ECA03D;
    border-top: 0;
  }
  .l {
    top: 15em;
    left: 5em;
  }
  #c {
    width: 10em;
    height: 10em;
    background: radial-gradient(#84271C 35%, #191210 35%);
    border: 2em solid #ECA03D;
    border-radius: 50%;
    top: 8em;
    left: 13em;
  }
  .r {
    transform: rotate(180deg);
    top: 10em;
    left: 25em;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

我想这就是了。有什么建议吗？