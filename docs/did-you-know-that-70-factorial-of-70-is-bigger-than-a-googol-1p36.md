# 你知道吗 70！(70 的阶乘)比一个古戈尔大？

> 原文：<https://dev.to/codeguppy/did-you-know-that-70-factorial-of-70-is-bigger-than-a-googol-1p36>

你知道吗 70！(70 的阶乘)比一个古戈尔大？

古戈尔是数字 10 后面跟着 100 个零。这个数字如此之大，以至于一些研究人员认为它比宇宙中的原子数量还要多。

...而且还是 70！比一个古戈尔大！！！

googol 这个大数字也吸引了谷歌创始人的注意...谁选择了 Google 这个名字(基于“googol”)来表示搜索引擎提供了大量的信息。

理解数字以及它们的大小是日常编程的一个重要方面。这对于选择合适的体系结构或选择正确的数据结构类型是必不可少的。

作为一个有趣的练习，试着计算 70！用你最喜欢的语言。不要使用任何花哨的大数类型结构...相反，只使用基本类型进行所有的计算。

下面是来自 codeguppy.com 网站的一个 JavaScript 解决方案。要在 codeguppy.com 的[之外运行该解决方案，只需将‘println’替换为‘console . log’](https://codeguppy.com)

为了您的方便，这个[游乐场](https://codeguppy.com/code.html?m4AfgJmCABGNEvKlUNtM)也提供了代码。

```
// Coding challenge #53\. Calculate all the digits of 70! (factorial of 70)
// Playground: https://codeguppy.com/code.html?m4AfgJmCABGNEvKlUNtM

println(factorial(70));

// Calculate factorial(n) ... using big number calculations
function factorial(n)
{
    var prod = "1";

    for(var i = 2; i <= n; i++)
    {
        prod = mult(prod, i.toString());
    }

    return prod;
}

// Multiplies sNumber1 * sNumber2
// Each number is provided as string
function mult(sNumber1, sNumber2)
{
    // Calculate partial results according to multiplication algorithm
    var partialResults = [];

    for(var i = sNumber2.length - 1; i >= 0; i--)
    {
        var digit = parseInt(sNumber2[i]);

        var partialResult = multDigit(sNumber1, digit);
        partialResult += "0".repeat(partialResults.length);

        partialResults.push(partialResult);
    }

    // Sum partial results to obtain the product
    var sum = "";

    for(var r of partialResults)
    {
        sum = add(sum, r);
    }

    return sum;
}

// Multiplies number sNumber (as string) with a single digit number
function multDigit(sNumber, digit)
{
    var p = "";
    var carry = 0;

    for(var i = sNumber.length - 1; i >= 0; i--)
    {
        var numberDigit = parseInt(sNumber[i]);

        var prod = digit * numberDigit + carry;
        var prodDigit = prod % 10;
        carry = Math.floor(prod / 10);

        p = prodDigit.toString() + p;
    }

    if (carry > 0)
        p = carry + p;

    return p;
}

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

        var sum = digit1 + digit2 + carry;
        var digitSum = sum % 10;
        carry = sum >= 10 ? 1 : 0;

        s = digitSum.toString() + s;
    }

    if (carry > 0)
        s = carry + s;

    return s;
} 
```

快乐编码...别忘了去 codeguppy.com 参加更多有趣的活动。你也可以在推特和其他社交网络上关注 [@codeguppy](https://twitter.com/codeguppy) 。