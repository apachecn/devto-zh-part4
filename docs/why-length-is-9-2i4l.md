# 为什么(！+ [] + [] + ![]).长度为 9

> 原文：<https://dev.to/tomasforsman/why-length-is-9-2i4l>

编辑:在底部，我添加了我写这篇文章后发现的东西。

今天我坐下来想出了为什么你会在 JavaScript 中得到这个结果

```
(! + [] + [] + ![]).length
> 9 
```

所以我坐在 js 控制台前，试图把事情弄清楚。有很多这样的事情在发生

```
(!"")
>true

(![])
>false

(!+[])
>true

(!+[]+[])
>"true"

(+[]+"2")
>"02"

([]+"2")
>"2" 
```

我想出的，我可能会完全关闭其中的一些，所以请纠正任何看起来不正确的，是以下内容。

*   JavaScript 从右向左进行操作。
*   +操作数首先尝试将左边的加到右边，如果这不可能，它会尝试将右边的操作数转换为一个数字，如果做不到，它会将其转换为一个字符串。如果+的左边有什么东西，它就把它们加在一起，如果没有，它就留在那里。如果左边什么都没有，它就会消失。
*   的！operand 将右边的操作数转换为布尔值，然后再将其还原。只有下列值与布尔值 false 相同:

```
false
0
""
null
NaN
undefined 
```

所有其他值不为假，因此为真。

现在大概是这样的:

```
// We start with
(! + [] + [] + ![]).length  // Remember that we are going from right to left starting 
                            // with the operation before doing -length
![] === false               // ! converts the content of [] to true, since it's not part of 
                            // the false group above, and then reverts it.
+ === +                     // the + is lonely so far with no operand to the right but sticks 
                            // around since it has something on the left.
[]+[] === ""                // The contents of [] can't be added or converted to a number so 
                            // the right operand becomes a string. There's still something to 
                            // the left so the + stays.
!+[] === true               // This the same as !+0 since +[] is 0

// Now we got: 
("false" + "" + false).length
""+false === "false"        // + adds "" to false. It then sticks around.
"true" + "" === "true"      // The + stays after adding "true" to ""

// ---
("true"++"false") 
+"false" ==== "false"       // + has nothing but an operand to the left so it goes away.
true + "" === "true"        // + adds them together and stays

// ---
("true"+"false")
"true" + "false" === "truefalse" // + still stays

// ---
(+"truefalse")
+"truefalse" === "truefalse"     // + has nothing to do after this so it goes away

// ---
("truefalse")               // The operation is done so now we are left with what's outside.

"truefalse".length === 9 
```

是的，我确实经历了每一步，甚至那些看起来毫无意义的。我一点也不确定这是怎么回事，但这似乎就是发生在我身上的事情。

想法？

编辑:
在评论和查看文档之后，这是我现在的想法。
((！(+[]))+[]+(![]))
一元运算符是从右向左这样！+[]变成了+[] - >！0 ===真。