# 反转一个字符串——四种 JavaScript 解决方案

> 原文：<https://dev.to/sarah_chima/reverse-a-string-four-javascript-solutions-2nbm>

*本文最初发表在我的[博客](https://sarahchima.com/blog/reverse-a-string/)T3 上*

反串问题是一个常见的算法问题。在本文中，我们将考虑四种 JavaScript 解决方案。

### 一点背景信息

最近，我参加了一个关于数据结构和算法的课程。这是因为我意识到我不擅长这个。情况并非总是如此。当我开始学习 JavaScript 时，解决算法挑战对我来说是有趣的部分。我可以熬夜几个小时，只是为了解决一个挑战。但是两年和许多前端项目之后，我似乎已经忘记了我所学的一切。这就是为什么我决定回去学习和实践。

扎实的数据结构和算法知识来自大量的实践。还有什么比写下来更好的方法来记住我学到的东西呢？这就是为什么我要向你展示这一系列文章的第一部分。

让我们深入研究今天的算法挑战:反转一个字符串

### 问题

反转一根弦就是反转一根弦。好了，问题陈述是这样的:写一个反转字符串的函数。如果将“Sarah”传递给函数，它应该返回“haraS”，“listen”应该变成“netsil”。明白了吗？

现在，让我们看看四种 javascript 解决方案。

### 解 1。使用 array reverse()方法

多亏了 Array.reverse()方法，我们可以轻松地反转数组。reverse()方法原地反转数组。第一个数组元素成为最后一个，最后一个数组元素成为第一个。

但是在我们的例子中，我们使用的是字符串。这意味着我们必须使用 split 方法将字符串转换为数组，使用 reverse 方法将其反转，然后使用 join 方法将其转换回字符串。下面是代码示例。

```
function reverseString(string) {
       //convert the string to an array
       let array = string.split("");

    //Use the reverse method
    array.reverse()

    //Convert it back to a string and return
    return array.join("")
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 arrow 函数和隐式 return 将它转换成一行程序。

```
const reverseString = (string) => string.split("").reverse().join(''); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。一条善良的线。让我们进入下一个解决方案。

### 解决方案 2:良好的 Ol' For-loop

这是通过字符串反转的经典例子。如果你遇到这个问题，这可能是你首先想到的方法之一。

我们在这里要做的是创建一个空字符串来保存反转后的字符串，遍历字符串中的每个字符，并将它追加到新字符串的开头。

```
 function reverse(str) {
    let reverseString = "";

    for (let character of str) {
        reverseString = character + reverseString;
    }

    return reverseString
} 
```

Enter fullscreen mode Exit fullscreen mode

### 解决方案 3 -使用 Array.reduce()方法

reduce()方法对数组的每个元素执行一个 reducer 函数(您提供的),产生一个输出值。不熟悉的可以[看一下](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)。

要使用 reduce 方法，我们需要将字符串转换为数组。接下来，我们使用 reduce 方法将其转换为反向字符串。在这种情况下，reducer 将字符串的每个字符追加到累加器的开头，在下面的代码中，累加器是`reversed`。

```
function reverseString(string) {
    //convert the string to an array
    const array = string.split('');

    //use the reduce method to convert the array to a reversed string
    const reversedString = array.reduce((reversed, character) => {
        return character + reversed
    }, '')

    return reversedString
} 
```

Enter fullscreen mode Exit fullscreen mode

上面这个函数可以进一步简化为:

```
const reverseString = (string) => {
    return string.split('').reduce((reversed, character) => character + reversed, '')
} 
```

Enter fullscreen mode Exit fullscreen mode

使它成为一行程序，并缩短 reduce 方法中使用的变量的名称。

```
const reverseString = (string) => string.split('').reduce((rev, char) => char + rev, '') 
```

Enter fullscreen mode Exit fullscreen mode

### 解决方案 4——使用递归

你是递归的朋友吗？如果没有，我给你介绍一下。

递归是一种通过使用调用自身的函数来解决问题的方法。每当函数调用自己时，它就把问题分解成子问题。这种递归调用一直持续到子问题可以被调用而无需进一步递归的时候。

递归函数的一个重要部分是基本情况。这是递归调用终止的条件，以确保它不会导致无限循环。你可以在这里找到更多的解释。

让我们回到“反转一根弦”上来。在这种情况下，我们的基本情况是字符串为空。我们使用 string.substring()方法删除字符串中的第一个字符，并将其他字符传递给函数。然后，我们将第一个字符追加到 return 语句中，如下面的代码所示。

```
function reverse(string){
    //base case: if str is empty return str 
   if(string === ""){
        return string 
   } else{
        return reverse(string.substring(1)) + string[0]
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用三元运算符来代替 if-else 语句。

```
function reverse(string){
    return string ? reverse(string.substring(1)) + string[0] : string
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

现在你知道了，在 JavaScript 中有四种很酷的反转字符串的方法。有没有其他解决这个问题的方法，请在评论中分享。我很想知道。

有任何问题或反馈吗？请留言评论。

在 Instagram [@sarah_codes_](https://www.instagram.com/sarah_codes_/) 上关注我，我会定期发布软件开发技巧和建议。