# 微编码挑战-解决方案

> 原文：<https://dev.to/codeguppy/micro-coding-challenges-with-solutions-106m>

掌握这些编码挑战可能不会让你在谷歌找到工作...但是在[codeguppy.com](https://codeguppy.com)你将离理解编码和构建你的第一个 JavaScript 游戏更近一步

这些编码挑战是为初学者设计的，因此解决方案仅使用简单/经典的编程元素来实现。每个解决方案都附有一个在线链接，帮助你在[codeguppy.com](https://codeguppy.com)的代码游戏场快速运行它

> 注意:在线代码使用 codeguppy 的特定函数`println()`来打印结果。这个辅助函数的语法与`console.log()`相似

## 编码挑战#1:打印 1 到 10 的数字

[https://codeguppy.com/code.html?mrgCtLGA90Ozr0Otrs5Z](https://codeguppy.com/code.html?mrgCtLGA90Ozr0Otrs5Z)

```
for(var i = 1; i <= 10; i++)
{
    console.log(i);
} 
```

## 编码挑战#2:打印小于 100 的奇数

[https://codeguppy.com/code.html?eDLA5XPp3bPxP79H2jKT](https://codeguppy.com/code.html?eDLA5XPp3bPxP79H2jKT)

```
for(var i = 1; i <= 100; i += 2)
{
    console.log(i);
} 
```

## 编码挑战#3:打印带 7 的乘法表

[https://codeguppy.com/code.html?fpnQzIhnGUUmCUZy1fyQ](https://codeguppy.com/code.html?fpnQzIhnGUUmCUZy1fyQ)

```
for(var i = 1; i <= 10; i++)
{
    var row = "7 * " + i + " = " + 7 * i;
    console.log(row);
} 
```

## 编码挑战#4:打印 1 到 10 的所有乘法表

[https://codeguppy.com/code.html?78aD4mWSCzoNEVxOQ8tI](https://codeguppy.com/code.html?78aD4mWSCzoNEVxOQ8tI)

```
for(var i = 1; i <= 10; i++)
{
    printTable(i);
    console.log("");
}

function printTable(n)
{
    for(var i = 1; i <= 10; i++)
    {
        var row = n + " * " + i + " = " + n * i;
        console.log(row);
    }
} 
```

## 编码挑战#5:计算 1 到 10 的数字之和

[https://codeguppy.com/code.html?Vy6u9kki2hXM4YjsbpuN](https://codeguppy.com/code.html?Vy6u9kki2hXM4YjsbpuN)

```
var sum = 0;

for(var i = 1; i <= 10; i++)
{
    sum += i;
}

console.log(sum); 
```

## 编码挑战#6:算 10！

[https://codeguppy . com/code . html？iiux 4 gnxondnu 0 vrywa](https://codeguppy.com/code.html?IIuJX4gnXOndNu0VrywA)

```
var prod = 1;

for(var i = 1; i <= 10; i++)
{
    prod *= i;
}

console.log(prod); 
```

## 编码挑战#7:计算大于 10 小于 30 的偶数之和

[https://codeguppy.com/code.html?DcOffOyoIArmNZHVNM2u](https://codeguppy.com/code.html?DcOffOyoIArmNZHVNM2u)

```
var sum = 0;

for(var i = 11; i <= 30; i += 2)
{
    sum += i;
}

console.log(sum); 
```

## 编码挑战#8:创建一个将摄氏温度转换为华氏温度的函数

[https://codeguppy . com/code . html？你好 5mWm6QIMRjY1m9XAmI](https://codeguppy.com/code.html?oI5mWm6QIMRjY1m9XAmI)

```
function celsiusToFahrenheit(n)
{
    return n * 1.8 + 32;
}

var r = celsiusToFahrenheit(20);
console.log(r); 
```

## 编码挑战 9:创建一个将华氏温度转换为摄氏温度的函数

[https://codeguppy.com/code.html?mhnf8DpPRqqgsBgbJNpz](https://codeguppy.com/code.html?mhnf8DpPRqqgsBgbJNpz)

```
function fahrenheitToCelsius(n)
{
    return (n - 32) / 1.8;
}

var r = fahrenheitToCelsius(68);
console.log(r); 
```

## 编码挑战#10:计算数字数组中数字的和

[https://codeguppy . com/code . html？ttevr 0 aj 33 zyclr 685 l](https://codeguppy.com/code.html?TteeVr0aj33ZyCLR685L)

```
function sumArray(ar)
{
    var sum = 0;

    for(var i = 0; i < ar.length; i++)
    {
        sum += ar[i];
    }

    return sum;
}

var ar = [2, 3, -1, 5, 7, 9, 10, 15, 95];
var sum = sumArray(ar);
console.log(sum); 
```

## 编码挑战#11:计算一组数字的平均值

[https://codeguppy.com/code.html?7i9sje6FuJsI44cuncLh](https://codeguppy.com/code.html?7i9sje6FuJsI44cuncLh)

```
function averageArray(ar)
{
    var n = ar.length;
    var sum = 0;

    for(var i = 0; i < n; i++)
    {
        sum += ar[i];
    }

    return sum / n;
}

var ar = [1, 3, 9, 15, 90];
var avg = averageArray(ar);

console.log("Average: ", avg); 
```

## 编码挑战#12:创建一个接收数字数组并返回一个仅包含正数的数组的函数

解决方案 1:

[https://codeguppy.com/code.html?0eztj1v6g7iQLzst3Id3](https://codeguppy.com/code.html?0eztj1v6g7iQLzst3Id3)

```
function getPositives(ar)
{
    var ar2 = [];

    for(var i = 0; i < ar.length; i++)
    {
        var el = ar[i];

        if (el >= 0)
        {
            ar2.push(el);
        }
    }

    return ar2;
}

var ar = [-5, 10, -3, 12, -9, 5, 90, 0, 1];
var ar2 = getPositives(ar);

console.log(ar2); 
```

## 编码挑战#12:创建一个接收数字数组并返回一个仅包含正数的数组的函数

解决方案 2

[https://codeguppy.com/code.html?KefrPtrvJeMpQyrB8V2D](https://codeguppy.com/code.html?KefrPtrvJeMpQyrB8V2D)

```
function getPositives(ar)
{
    var ar2 = [];

    for(var el of ar)
    {
        if (el >= 0)
        {
            ar2.push(el);
        }
    }

    return ar2;
}

var ar = [-5, 10, -3, 12, -9, 5, 90, 0, 1];
var ar2 = getPositives(ar);

console.log(ar2); 
```

## 编码挑战#12:创建一个接收数字数组并返回一个仅包含正数的数组的函数

解决方案 3

[https://codeguppy . com/code . html？qjbqubna 7z 10 n6 pymb 8](https://codeguppy.com/code.html?qJBQubNA7z10n6pjYmB8)

```
function getPositives(ar)
{
    return ar.filter(el => el >= 0);
}

var ar = [-5, 10, -3, 12, -9, 5, 90, 0, 1];
var ar2 = getPositives(ar);
console.log(ar2); 
```

## 编码挑战#13:在一组数字中找出最大的数字

[https://codeguppy . com/code . html？tmqggomruj 6 psvev 8 HD](https://codeguppy.com/code.html?THmQGgOMRUj6PSvEV8HD)

```
function findMax(ar)
{
    var max = ar[0];

    for(var i = 0; i < ar.length; i++)
    {
        if (ar[i] > max)
        {
            max = ar[i];
        }
    }

    return max;
}

var ar = [-5, 10, -3, 12, -9, 5, 90, 0, 1];
var max = findMax(ar);
console.log("Max: ", max); 
```

## 编码挑战#14:不用递归打印前 10 个斐波那契数

[https://codeguppy . com/code . html？雷科 fpxhbv wnwi 2d 8 orh](https://codeguppy.com/code.html?rKOfPxHbVwxNWI2d8orH)

```
var f0 = 0;
console.log(f0);

var f1 = 1;
console.log(f1);

for(var i = 2; i < 10; i++)
{
    var fi = f1 + f0;
    console.log(fi);

    f0 = f1;
    f1 = fi;
} 
```

## 编码挑战#15:创建一个函数，使用递归找到第 n 个斐波那契数

[https://codeguppy.com/code.html?IneuIg9O0rRV8V76omBk](https://codeguppy.com/code.html?IneuIg9O0rRV8V76omBk)

```
function findFibonacci(n)
{
    if (n == 0)
        return 0;

    if (n == 1)
        return 1;

    return findFibonacci(n - 1) + findFibonacci(n - 2);
}

var n = findFibonacci(10);
console.log(n); 
```

## 编码挑战#16:创建一个函数，返回一个布尔值，表明一个数是否是质数

[https://codeguppy . com/code . html？fRYsPEc2vcZTbIU8MKku](https://codeguppy.com/code.html?fRYsPEc2vcZTbIU8MKku)

```
function isPrime(n)
{
    if (n < 2)
        return false;

    if (n == 2)
        return true;

    var maxDiv = Math.sqrt(n);

    for(var i = 2; i <= maxDiv; i++)
    {
        if (n % i == 0)
        {
            return false;
        }
    }

    return true;
}

console.log(2, " is prime? ", isPrime(2));
console.log(3, " is prime? ", isPrime(3));
console.log(4, " is prime? ", isPrime(4));
console.log(5, " is prime? ", isPrime(5));
console.log(9, " is prime? ", isPrime(9)); 
```

## 编码挑战#17:计算正整数的位数之和

[https://codeguppy . com/code . html？RHA 714 fyo 8 gwgmjwypz](https://codeguppy.com/code.html?RHA714FYio8gWgmjWYPz)

```
function sumDigits(n)
{
    var s = n.toString();
    var sum = 0;

    for(var char of s)
    {
        var digit = parseInt(char);
        sum += digit;
    }

    return sum;
}

var sum = sumDigits(1235231);
console.log("Sum: ", sum); 
```

## 编码挑战#18:打印前 100 个质数

[https://codeguppy.com/code.html?gnMVeOZXN6VhLekyvui8](https://codeguppy.com/code.html?gnMVeOZXN6VhLekyvui8)

```
printPrimes(100);

// Function prints the first nPrimes numbers
function printPrimes(nPrimes)
{
    var n = 0;
    var i = 2;

    while(n < nPrimes)
    {
        if (isPrime(i))
        {
            console.log(n, " --> ", i);
            n++;
        }

        i++;
    }
}

// Returns true if a number is prime
function isPrime(n)
{
    if (n < 2)
        return false;

    if (n == 2)
        return true;

    var maxDiv = Math.sqrt(n);

    for(var i = 2; i <= maxDiv; i++)
    {
        if (n % i == 0)
        {
            return false;
        }
    }

    return true;
} 
```

## 编码挑战#19:创建一个函数，返回数组中大于特定数字“startAt”的前“nPrimes”个素数

[https://codeguppy.com/code.html?mTi7EdKrviwIn4bfrmM7](https://codeguppy.com/code.html?mTi7EdKrviwIn4bfrmM7)

```
console.log(getPrimes(10, 100));

function getPrimes(nPrimes, startAt)
{
    var ar = [];

    var i = startAt;

    while(ar.length < nPrimes)
    {
        if (isPrime(i))
        {
            ar.push(i);
        }

        i++;
    }

    return ar;
}

// Returns true if a number is prime
function isPrime(n)
{
    if (n < 2)
        return false;

    if (n == 2)
        return true;

    var maxDiv = Math.sqrt(n);

    for(var i = 2; i <= maxDiv; i++)
    {
        if (n % i == 0)
        {
            return false;
        }
    }

    return true;
} 
```

## 编码挑战#20:将数组向左旋转 1 位

[https://codeguppy.com/code.html?MRmfvuQdZpHn0k03hITn](https://codeguppy.com/code.html?MRmfvuQdZpHn0k03hITn)

```
var ar = [1, 2, 3];
rotateLeft(ar);
console.log(ar);

function rotateLeft(ar)
{
    var first = ar.shift();
    ar.push(first);
} 
```

## 编码挑战#21:将数组向右旋转 1 位

[https://codeguppy.com/code.html?fHfZqUmkAVUXKtRupmzZ](https://codeguppy.com/code.html?fHfZqUmkAVUXKtRupmzZ)

```
var ar = [1, 2, 3];
rotateRight(ar);
console.log(ar);

function rotateRight(ar)
{
    var last = ar.pop();
    ar.unshift(last);
} 
```

## 编码挑战#22:反转数组

[https://codeguppy . com/code . html？gzddbqbbvlqyrssxi 3 vbu](https://codeguppy.com/code.html?GZddBqBVFlqYrsxi3Vbu)

```
var ar = [1, 2, 3];
var ar2 = reverseArray(ar);
console.log(ar2);

function reverseArray(ar)
{
    var ar2 = [];

    for(var i = ar.length - 1; i >= 0; i--)
    {
        ar2.push(ar[i]);
    }

    return ar2;
} 
```

## 编码挑战#23:反转字符串

[https://codeguppy.com/code.html?pGpyBz0dWlsj7KR3WnFF](https://codeguppy.com/code.html?pGpyBz0dWlsj7KR3WnFF)

```
var s = reverseString("JavaScript");
console.log(s);

function reverseString(s)
{
    var s2 = "";

    for(var i = s.length - 1; i >= 0; i--)
    {
        var char = s[i];
        s2 += char;
    }

    return s2;
} 
```

#编码挑战#24:创建一个合并两个数组并将结果作为新数组返回的函数

[https://codeguppy . com/code . html？vctkxytabitqdukvc](https://codeguppy.com/code.html?vcTkLxYTAbIflqdUKivc)

```
var ar1 = [1, 2, 3];
var ar2 = [4, 5, 6];

var ar = mergeArrays(ar1, ar2);
console.log(ar);

function mergeArrays(ar1, ar2)
{
    var ar = [];

    for(let el of ar1)
    {
        ar.push(el);
    }

    for(let el of ar2)
    {
        ar.push(el);
    }

    return ar;
} 
```

## 编码挑战#25:创建一个函数，它将接收两个数字数组作为参数，并返回一个由所有数字组成的数组，这些数字要么在第一个数组中，要么在第二个数组中，但不同时在两个数组中

[https://codeguppy.com/code.html?Y9gRdgrl6PPt4QxVs7vf](https://codeguppy.com/code.html?Y9gRdgrl6PPt4QxVs7vf)

```
var ar1 = [1, 2, 3, 10, 5, 3, 14];
var ar2 = [1, 4, 5, 6, 14];

var ar = mergeExclusive(ar1, ar2);
console.log(ar);

function mergeExclusive(ar1, ar2)
{
    var ar = [];

    for(let el of ar1)
    {
        if (!ar2.includes(el))
        {
            ar.push(el);
        }
    }

    for(let el of ar2)
    {
        if (!ar1.includes(el))
        {
            ar.push(el);
        }
    }

    return ar;
} 
```

## 编码挑战#26:创建一个函数，它将接收两个数组，并返回一个数组，其中的元素在第一个数组中，但不在第二个数组中

[https://codeguppy . com/code . html？bUduoyY6FfwV5nQGdXzH](https://codeguppy.com/code.html?bUduoyY6FfwV5nQGdXzH)

```
var ar1 = [1, 2, 3, 10, 5, 3, 14];
var ar2 = [-1, 4, 5, 6, 14];

var ar = mergeLeft(ar1, ar2);
console.log(ar);

function mergeLeft(ar1, ar2)
{
    var ar = [];

    for(let el of ar1)
    {
        if (!ar2.includes(el))
        {
            ar.push(el);
        }
    }

    return ar;
} 
```

## 编码挑战#27:创建一个函数，它将接收一个数字数组作为参数，并将返回一个包含不同元素的新数组

解决方案 1

[https://codeguppy . com/code . html？ok BTP 1 zksghxwq7jh 3i](https://codeguppy.com/code.html?OkbtP1ZksGHXwqk7Jh3i)

```
var ar = getDistinctElements([1, 2, 3, 6, -1, 2, 9, 7, 10, -1, 100]);
console.log(ar);

function getDistinctElements(ar)
{
    var ar2 = [];

    for(let i = 0; i < ar.length; i++)
    {
        if (!isInArray(ar[i], ar2))
        {
            ar2.push(ar[i]);
        }
    }

    return ar2;
}

function isInArray(n, ar)
{
    for(let i = 0; i < ar.length; i++)
    {
        if (ar[i] === n)
            return true;
    }

    return false;
} 
```

## 编码挑战#27:创建一个函数，它将接收一个数字数组作为参数，并将返回一个包含不同元素的新数组

解决方案 2

[https://codeguppy . com/code . html？NjGtyQdMP49QiaAkmwpU](https://codeguppy.com/code.html?NjGtyQdMP49QiaAkmwpU)

```
var ar = getDistinctElements([1, 2, 3, 6, -1, 2, 9, 7, 10, -1, 100]);
console.log(ar);

function getDistinctElements(ar)
{
    var ar2 = [];

    var lastIndex = ar.length - 1;

    for(let i = 0; i <= lastIndex; i++)
    {
        if (!isInArray(ar[i], ar, i + 1, lastIndex))
        {
            ar2.push(ar[i]);
        }
    }

    return ar2;
}

function isInArray(n, ar, fromIndex, toIndex)
{
    for(var i = fromIndex; i <= toIndex; i++)
    {
        if (ar[i] === n)
            return true;
    }

    return false;
} 
```

## 编码挑战#28:计算前 100 个素数的和

[https://codeguppy . com/code . html？v0O9sBfnHbCi1StE2TxA](https://codeguppy.com/code.html?v0O9sBfnHbCi1StE2TxA)

```
var n = 10;
console.log("Sum of first ", n, " primes is ", sumPrimes(10));

function sumPrimes(n)
{
    var foundPrimes = 0;
    var i = 2;
    var sum = 0;

    while(foundPrimes < n)
    {
        if (isPrime(i))
        {
            foundPrimes++;
            sum += i;
        }

        i++;
    }

    return sum;
}

// Returns true if number n is prime
function isPrime(n)
{
    if (n < 2)
        return false;

    if (n == 2)
        return true;

    var maxDiv = Math.sqrt(n);

    for(var i = 2; i <= maxDiv; i++)
    {
        if (n % i === 0)
        {
            return false;
        }
    }

    return true;
} 
```

## 编码挑战#29:打印前 100 个质数之间的距离

[https://codeguppy.com/code.html?xKQEeKYF1LxZhDhwOH7V](https://codeguppy.com/code.html?xKQEeKYF1LxZhDhwOH7V)

```
printDistances(100);

// Print distances between the first n prime numbers
function printDistances(n)
{
    var lastPrime = 2;
    var i = lastPrime + 1;
    var foundPrimes = 1;

    while(foundPrimes < n)
    {
        if (isPrime(i))
        {
            console.log(i - lastPrime, "\t", i, " - ", lastPrime);

            foundPrimes++;
            lastPrime = i;
        }

        i++;
    }
}

// Returns true if number n is prime
function isPrime(n)
{
    if (n < 2)
        return false;

    if (n == 2)
        return true;

    var maxDiv = Math.sqrt(n);

    for(var i = 2; i <= maxDiv; i++)
    {
        if (n % i === 0)
        {
            return false;
        }
    }

    return true;
} 
```

## 编码挑战#30:创建一个将两个大小不定的正数相加的函数。数字以字符串形式接收，结果也应该以字符串形式提供。

解决方案 1

[https://codeguppy.com/code.html?v5A0QBsdHaiAVA2CPN5y](https://codeguppy.com/code.html?v5A0QBsdHaiAVA2CPN5y)

```
var n1 = "2909034221912398942349";
var n2 = "1290923909029309499";
var sum = add(n1, n2);

console.log(n1, "\n", n2, "\n", sum);

function add(sNumber1, sNumber2)
{
    var s = "";
    var carry = 0;

    var maxSize = Math.max(sNumber1.length, sNumber2.length);

    for(var i = 0; i < maxSize; i++)
    {
        var digit1 = digitFromRight(sNumber1, i);
        var digit2 = digitFromRight(sNumber2, i);

        var sum = digit1 + digit2;

        var digitSum = sum % 10;
        digitSum += carry;
        s = digitSum.toString() + s;

        carry = sum >= 10 ? 1 : 0;
    }

    if (carry > 0)
        s = carry + s;

    return s;
}

function digitFromRight(s, digitNo)
{
    if (digitNo >= s.length)
        return 0;

    var char = s[ s.length - 1 - digitNo ];
    return parseInt(char);
} 
```

## 编码挑战#30:创建一个将两个大小不定的正数相加的函数。数字以字符串形式接收，结果也应该以字符串形式提供。

解决方案 2

[https://codeguppy.com/code.html?yMQXcPgfrYxuaIxqQmZc](https://codeguppy.com/code.html?yMQXcPgfrYxuaIxqQmZc)

```
var n1 = "2909034221912398942349";
var n2 = "1290923909029309499";
var sum = add(n1, n2);

console.log(n1);
console.log(n2);
console.log(sum);

function add(sNumber1, sNumber2)
{
    var maxSize = Math.max(sNumber1.length, sNumber2.length);

    var s1 = sNumber1.padStart(maxSize, "0");
    var s2 = sNumber2.padStart(maxSize, "0");

    var s = "";
    var carry = 0;

    for(var i = maxSize - 1; i >= 0; i--)
    {
        var digit1 = parseInt(s1[i]);
        var digit2 = parseInt(s2[i]);

        var sum = digit1 + digit2;

        var digitSum = sum % 10;
        digitSum += carry;
        s = digitSum.toString() + s;

        carry = sum >= 10 ? 1 : 0;
    }

    if (carry > 0)
        s = carry + s;

    return s;
} 
```

其他需要自己尝试的编码挑战:

1.  创建一个函数，返回文本中的字数
2.  创建一个将文本中每个单词的首字母大写的函数
3.  计算以逗号分隔的字符串中收到的数字的总和
4.  创建一个函数，返回文本中每个单词出现的次数。返回的将是一个包含{word，count}内对象的数组
5.  创建一个函数将 CSV 文本转换为“二维”数组
6.  创建一个将字符串转换为字符数组的函数
7.  创建一个函数来转换包含每个字符的 ASCII 码的数组中的字符串
8.  创建一个函数来转换一个包含 ASCII 码的字符串数组
9.  实施凯撒密码
10.  对数字数组实现冒泡排序算法
11.  创建一个函数来计算由 x，y 坐标定义的两点之间的距离
12.  创建一个函数，该函数将返回一个布尔值，该值指示由中心坐标和半径定义的两个圆是否相交
13.  创建一个函数，该函数将接收一个二维数组作为参数和一个数字，并将提取该数字指定的列作为一维数组
14.  创建一个将包含二进制数的字符串转换成数字的函数
15.  创建一个函数来计算一个交错数组中所有数字的总和(包含数字或其他数字数组，级数不限)
16.  在一组交错的数字或一组数字中找出最大值
17.  将一个带有数字或其他数组的交错数组深度复制到一个新数组中
18.  创建一个函数来返回字符串中最长的单词
19.  打乱字符串数组
20.  创建一个函数，它将接收 n 作为参数，并返回一个由从 1 到 n 的 n 个随机数组成的数组。这些数字在数组中应该是唯一的。
21.  找出字符串中字母的出现频率。以数组的数组形式返回结果。每个子数组有两个元素:字母和出现次数。
22.  高精度计算斐波那契(500)(全数字)
23.  算 70！高精度(所有数字)

> 如果你喜欢这种挑战，请在 Twitter 上关注 [@codeguppy](https://twitter.com/codeguppy) ，那里有时会发布编码挑战和其他新闻(甚至以图形形式)。