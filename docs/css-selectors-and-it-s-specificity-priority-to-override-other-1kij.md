# CSS 选择器及其特殊性(优先于其他)

> 原文：<https://dev.to/gvinod1991/css-selectors-and-it-s-specificity-priority-to-override-other-1kij>

## 一个开发人员没搞清楚选择器是什么问题？

有时，我们对一些 html 元素应用样式，但并不按照我们的代码工作，最终以代码中的错误结束。为了避免这种错误，开发人员应该清楚地理解 css 选择器的概念及其特性。

以下是将样式应用于 html 元素的几个其他问题。

*   如何将样式应用于嵌套在一个元素中的所有元素。

*   我们面临的挑战是选择列表中所有的奇数和偶数 li 元素，并对每个元素应用不同的样式。

*   如果我们有一个以上的元素应用相同的风格。

*   我们必须只针对一个元素，它在整个 html 文档中应该是唯一的。

为了解决上述问题，我们使用不同类型的选择器，并将样式应用于 html 元素。

## CSS 中的选择器是什么？

在普通英语中，我们可以说一个选择某物的事物。就像 css 中的选择器也可以选择或查找 HTML 元素。选择器用于定位 html 元素，以便对其应用样式。

最常用的 and 选择器有

*   作为类型选择器调用的 HTMl 元素/标记名
*   班级
*   身份证明（identification）

在 Css 中，我们可以有更多的选择器，这有点难以理解

*   伪类
*   伪元素
*   属性选择器
*   通用选择器
*   组合器(组合多个选择器)

让我们逐一探索每个选择器

## 类型选择器(按标签名)

我们可以通过名称选择一个或多个元素。

```
p{
    color:green;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，绿色应用于 html 文档中的所有 p 元素。

## 类(。)

类(。)选择器识别所有具有属性类的元素。如果有多个元素具有相同的类名，那么相同的样式将应用于所有元素。参见下面的代码。

```
.box{
    width:200px;
    height:200px;
    border:1px solid #ccc
}

<div class="box">
    I am box
</div>
<div class="box">
    I am 2nd box
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## Id(#)

id(#)选择器仅识别一个具有属性名称 id 的元素。如果有多个元素具有相同的 id，那么它会选择第一个元素。检查下面的代码

```
#avatar{
    width:100px;
    height:100px;
    border-radius:50%;
    border:1px solid #ccc
}
<div id="avatar">
    avatar
</div>
<div id="avatar">
    avatar2
</div> 
```

Enter fullscreen mode Exit fullscreen mode

上述样式仅适用于第一个 div 元素。

当与常用的选择器结合使用时，所有高级选择器都标识特定的元素

## 伪类(:)

如你所知，伪类用于定义 html 元素的状态，如鼠标悬停或访问后的链接。因此，当元素处于特定状态时，这可以用作选择器来样式化元素。

常见的伪类有:hover，:visited，:link 和:active。这些选项与其他选择器结合使用，以找到元素的特定状态，并对其应用样式

```
a:hover{
    color:tomato /* On mouse hover,color of a is tomato color */
}
a:visited{
    color:red /* On linked visited,color of a is red */
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过使用:n-child()和从最后一个开始计数的父元素的特定编号的子元素:n-last-child 来选择父元素的特定编号的子元素。

```
li:nth-child(2){
    color:green
}

The above code selects every li element which is 2nd child of it's parent and apply color property to green.

li:nth-child(even){
    color:yellow /* selects the li element which is even child of it's parent */
}

li:nth-child(odd){
    color:yellow /* selects the li element which is odd child of it's parent */
}

li:nth-lat-child{
    color:blue /* selects the li element which is 2nd child but counting from last*/
} 
```

Enter fullscreen mode Exit fullscreen mode

## 伪元素(::)

最常见的伪元素是::before 和::after。这些用于将一些内容放置/添加到元素的特定部分，如之前或之后。

```
span::before{
    content:url('left-arrow.png);
    padding:2px   
}

span::after{
    content:url('right-arrow.png);
    padding:2px
} 
```

Enter fullscreen mode Exit fullscreen mode

其他很少使用的伪元素有::first-letter 和::first-line 分别选择单词的第一个字母和段落的第一行。

```
p::first-letter{
    font-size:36px;
    font-weight:bold
}

p::first-line{
    font-weight:bold
} 
```

Enter fullscreen mode Exit fullscreen mode

## 属性选择器(【属性】)

要对具有特定属性和属性值元素应用样式，我们可以使用属性选择器。

```
a[href="https://www.google.com"]{

    color:violet /* All a with href and it's value https://www.google.com are colored with violet. */
}

input[type="text"]{

    border:none /* No border property applied to all inputs with attribute type and it's value text. */
}

a[target]{
    background-color:cyan /* All a tags with attribute target have background color cyan */
} 
```

Enter fullscreen mode Exit fullscreen mode

## 通用选择器(*)

该选择器在定义时选择所有元素。

```
*{
    background:#f5f5f5
} 
```

Enter fullscreen mode Exit fullscreen mode

它选择所有元素并应用背景色为#f5f5f5

## 组合子选择器(+、>、空格和~)

我们可以用+、>空格和~来组合两个元素，以标识两个组合元素的相对元素

### (空格)后代选择器

此类型组合匹配指定元素(空格前的元素)的后代的所有元素(空格后的元素)。请参见下面的示例。

```
div p{
    color:red /*It selects all the p elements which are inside div element*/
}
<div>
    <p>I am small paragraph.</p><!--Color is red-->
    <section>
        <p>I am paragraph inside section</p><!--Color is red-->
    </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 儿童选择器(>)

它匹配指定元素(之前的元素)的所有直接子元素(之后的元素)。

```
div > p{
    color:red /*It selects all the p elements which are only immediate children div element*/
}
<div>
    <p>I am small paragraph.</p><!--Color is red-->
    <section>
        <p>I am paragraph inside section</p><!--Color is black-->
    </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 相邻同胞选择器(+)

它标识与特定元素(+)之前的元素相邻的所有元素(+)之后的元素。

```
div + p{
    color:#ccc;/*selects all p elements that are placed immediately after div elements*/
} 
```

Enter fullscreen mode Exit fullscreen mode

### 通用同级选择器(~)

这种类型的选择器选择指定元素的所有同级元素。

```
div ~ p{
    color:#ccc;/*selects all p elements that are siblings of div elements*/
} 
```

Enter fullscreen mode Exit fullscreen mode

## 选择器特异性(优先于其他)

特异性是指浏览器如何计算和应用样式到 html 元素(哪种类型的选择器优先覆盖其他选择器的属性)

按照从高到低的特异性/优先级别查找选择器列表

1.  id(#)
2.  类(。)、伪类(:)、伪元素(::)和属性选择器
3.  类型选择器(Html 标记名)

通用选择器(*)和组合符(+、>、~、空格)对特异性没有影响。

我们不能从内部和外部样式中覆盖内联样式，因为内联样式具有覆盖所有外部和内部样式的高优先级。我们可以使用“！重要”旁的任何财产。

```
p{
    color:cyan!important;
}

<p style="color:red">I am paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

段落的颜色为青色，因为内嵌样式颜色属性被覆盖！除了财产以外很重要

注意:-我为新手开发者写了这个博客，我的意图是让 web 编码世界的新来者理解所有常用的选择器，因为 css 中还有很多选择器没有在这个博客中介绍。