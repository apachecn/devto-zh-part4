# 了解 CSS 滤镜的基础知识

> 原文：<https://dev.to/thisdotmedia/learn-the-basics-of-css-filters-5ffc>

我们从未想过我们可以如此轻松地提升文档的视觉效果。现在我们可以了！CSS 滤镜真的是很酷的视觉效果，也是 CSS 最好玩的部分。

现在，不需要尝试 Photoshop 或任何其他编辑工具，我们可以使用 CSS 滤镜给我们的图像同样的效果。

* * *

filter:blur()| brightness()| contrast()| drop-shadow()|灰度()|色相-旋转()|反转()|不透明度()|棕褐色()|饱和()| URL()；

* * *

**模糊效果-** 模糊效果可以使用 Blur()函数应用到元素上。这个函数接受 CSS 长度值作为定义模糊半径的参数。较大的值会产生更多的模糊。如果没有提供参数，则使用值 0。

```
.image-1 {
  -webkit-filter: blur(0|2px|5px); 
  filter: blur(0|2px|5px);
} 
```

[![](img/5214f0fa7b04feb35270300dcaaa47a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JdXZIICT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1maswrlflsh0sxd81qoq.png)

**图像亮度-** 亮度()功能可用于设置图像的亮度。值为 0%将创建一个全黑的图像。大于 100%的值会使图像完全变亮，而 100%或 1 会使图像保持不变。负值不允许作为此函数的参数。

```
.image-1 {
  -webkit-filter: brightness(50%|100%|200%); 
  filter: brightness(50%|100%|200%);
} 
```

[![](img/f9d5a20a74271cf5f8cad7035fab9019.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ZtOSS3Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szhifpzuer5fzvpaze8r.png)

**图像对比度-** 对比度()功能用于调整图像的对比度。值为 0%将创建一个全黑的图像。大于 100%的值会使图像完全变亮，而 100%或 1 会使图像保持不变。

```
.image-1 {
  -webkit-filter: contrast(50%|100%|200%); 
  filter: contrast(50%|100%|200%);
} 
```

[![](img/f29996ef577322a30aaf9dbd717f9f26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZdBOfNsx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2kr84dhxkfcmj6ybaras.png)

**不透明度-**Opacity()函数将透明度应用于图像。介于 0%和 100%之间的值是效果的线性乘数。不透明度滤镜效果通过从完全透明到完全不透明的范围级别来衡量。

```
.image-1 {
  -webkit-filter: opacity(10%|40%|80%); 
  filter: opacity(10%|40%|80%);
} 
```

[![](img/2df9f2379cd6b896b6d92657917310e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YxkpP5ho--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6diw2bhv9mcg2bi2ctpe.png)

**棕褐色效果-** 棕褐色滤镜效果是赋予元素的棕灰色到深黄褐色色调，以 0%到 100%的百分比表示。

```
.image-1 {
  -webkit-filter: sepia(10%|40%|80%); 
  filter: sepia(10%|40%|80%);
} 
```

[![](img/31a9c670c0420436ad8f3864d2b202c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8dH-LiM0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ye6qd5yehw5o8whxkdzo.png)

**投下阴影-** 这给图像添加了一个阴影，类似于方框阴影。

```
.image-1{
  drop-shadow(0);
}

.image-2 {
  -webkit-filter: drop-shadow(4px 4px 10px yellow);
  filter: drop-shadow(4px 4px 10px yellow);
}

.image-3 {
  -webkit-filter: drop-shadow(8px 8px 12px yellow);
  filter: drop-shadow(8px 8px 12px yellow);
} 
```

[![](img/78bcb7a8c3636da7351a47c1e7a20a4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SzN_2gMs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6loexthcieg3yv1kg9x.png)

**灰度-** 灰度()CSS 函数将输入图像转换为灰度。介于 0%和 100%之间的值是效果的线性乘数。

```
.image-1 {
  -webkit-filter: grayscale(50%|100%|200%); 
  filter: grayscale(50%|100%|200%);
} 
```

[![](img/9143f6a02031191499459ce3254806f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GrVvJkU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0155bmqn6kr60vuixdw5.png)

CSS hue-rotate()函数和 filter 属性一起使用，对图像进行色调旋转。色调旋转是指在色环周围指定一个角度，输入样本将根据该角度进行调整。

CSS hue-rotate()函数需要一个参数来告诉它将色调旋转多少。

```
.image-1 {
  -webkit-filter: hue-rotate(0|100deg|250deg); 
  filter: hue-rotate(0|100deg|250deg);
} 
```

[![](img/04565c3bbd36a19aebb9b0261cc51e2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPwF6eIl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iccyepgudj5nnxp57n8l.png)

**反转效果-**Invert()CSS 函数反转输入图像中的颜色样本。0% (0)是默认值，代表原始图像。100%会让图像完全反转。不允许负值。

```
.image-1 {
  -webkit-filter: invert(0|40%|80%); 
  filter: invert(0|40%|80%);
} 
```

[![](img/e2015f7cf69c0edde24080b93265a7f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--un_et1K1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/34wedwa59av51fwdvbow.png)

**饱和-** 饱和滤镜用于改变元素的颜色强度。饱和元素的颜色过亮；您可以增加曝光不足图像的饱和度，反之亦然。饱和度可以用百分比表示，0%表示完全不饱和，100%表示输入不变。

```
.image-1 {
  -webkit-filter: saturate(10%|150%|350%); 
  filter: saturate(10%|150%|350%);
} 
```

[![](img/149015545fb2d428648150818a2faf81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JCF7TI_R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/teswpkkyt32j60shbmpt.png)

CSS 滤镜显示了 SVG 格式的效果演变以及它们的效果。他们用最少的努力让图像看起来更有冲击力。每个滤镜都有一个基本目的，就是将效果应用到图形元素或容器元素

当前浏览器和用户代理支持可以从 [@_“我可以使用吗...”CSS3 滤镜效果表](https://caniuse.com/#search=css%20filter)；

这篇文章是由 Megha Sachdev 写的，她是这个点的学徒。

你可以在@ megsachdev 的 Twitter 上关注她。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](https://thisdot.co) 查看我们的服务列表。