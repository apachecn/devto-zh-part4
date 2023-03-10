# 如何在 Javascript 中反转给定的字符串

> 原文：<https://dev.to/yongliang24/how-to-reverse-a-given-string-in-javascript-alc>

### 目标

创建一个函数，将一个字符串作为参数，并返回该字符串的反向版本。

### 方法 1

1.将字符串转换成数组，这样我们就可以遍历每个字符。

2.向后迭代数组，并将每个字符推入一个新数组。我们可以使用内置的 reverse()函数，也可以自己写一个。

3.最后用。join()将数组转换回字符串并返回它。

```
//This solution uses all javascript built-in methods

function reverseString(str){

    let strArray = str.split("") 
    //it's important not to add space on the quotation mark here.

    strArray = strArray.reverse()

    return strArray.join("") //also no space on quotation mark here
}

reverseString("amazon google") //call the function

"elgoog nozama" //the returned string 
```

自己写逆向方法:

```
function reverseString(str){

    let revArray = [];
    let strArray = str.split("")

    // Looping from the end to the beginning
    for(let i = strArray.length; i >= 0; i--) {

       //push each character into the new array
        revArray.push(strArray[i]); 
    } 

    // Joining the array elements 
    return revArray.join(''); 
} 
```

而不是使用内置的。split()将字符串转换为数组，我们也可以使用 for...循环的。这样我们就可以遍历字符串中的每个字符，而不是数组。

```
function stackReverse(str){
    let strArray = [];

    for(let eachLetter of str){
        strArray.push(eachLetter)
    }

    strArray.reverse();

    return strArray.join("")

}

stackReverse("google amazon") //call the function

"nozama elgoog" //return value 
```

这是我关于如何在 javascript 中反转字符串的发现，希望你喜欢。