# javascript 的 7 个有用链接

> 原文：<https://dev.to/temcodes/7-useful-links-for-javascript-33j1>

**1。将值归入逻辑类型**
下面介绍如何将某个值归入逻辑类型:

```
const myBoolean = !!myVariable; 
```

Enter fullscreen mode Exit fullscreen mode

双重否定(！！)是必要的，以便将从 JavaScript 规则的角度来看为真的值转换为真，将假转换为假。

**2。移除数组中的重复值**
下面是如何移除数组中的重复值:

```
const deDupe = [... new Set (myArray)]; 
```

Enter fullscreen mode Exit fullscreen mode

指定的数据结构只存储唯一的值。因此，使用这样的数据结构和语法分散允许您基于数组 myArray 创建一个新的数组，其中没有重复的值。

**3。通过条件**创建和设置对象属性

要使用&&运算符设置对象的属性，可以使用分布语法:

```
const myObject = {... myProperty && {propName: myProperty}}; 
```

Enter fullscreen mode Exit fullscreen mode

如果作为表达式左侧计算的结果，JS 认为收到了错误的值，那么&&将不会执行进一步的计算，也不会创建和设置新的属性。MyObject 将为空。如果...myProperty 构造返回一些 JS 认为是真的结果，由于&&构造，propName 属性将出现在对象中，保留结果值。

**4。合并对象**

下面是如何创建一个新对象，其中两个其他对象将被合并:

```
const mergedObject = {... objectOne, ... objectTwo}; 
```

Enter fullscreen mode Exit fullscreen mode

这种方法可以用来组织无限数量的对象的合并。此外，如果对象具有相同名称的属性，则在最终对象中将只有一个属于源对象的属性，该属性位于其他属性的右侧。请注意，这是使用对象属性的浅层复制完成的。

**5。变量值的交换**

要在不使用辅助变量的情况下交换两个变量的值，可以这样做:

```
[varA, varB] = [varB, varA]; 
```

Enter fullscreen mode Exit fullscreen mode

之后，在 varA 中的东西将落入 varB，反之亦然。通过使用内部破坏机制，这是可能的。

**6。从数组中移除错误值**

下面是如何从数组中删除所有在 JavaScript 中被认为是假的值:

```
const clean = dirty.filter (Boolean); 
```

Enter fullscreen mode Exit fullscreen mode

在此操作的执行过程中，诸如 null、undefined、false、0 以及空行等值将从数组中删除。

7 .**。将数字转换成字符串**

要将存储在数组中的数字转换成字符串形式，可以这样做:

```
const stringArray = numberArray.map (String); 
```

Enter fullscreen mode Exit fullscreen mode

在这种转换过程中，数组的字符串元素将保持为字符串。

您也可以通过将字符串值转换为数字值来执行逆转换:

const number ray = string array . map(number)；

**结论:关于合并和扩展单行代码**

你会把哪些有用的 JS 一行用户的例子加到这个素材里？