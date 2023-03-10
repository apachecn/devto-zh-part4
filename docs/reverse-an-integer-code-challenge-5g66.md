# 逆转整数码挑战

> 原文：<https://dev.to/rygelxvi/reverse-an-integer-code-challenge-5g66>

密码问题 7

### 给定一个 32 位有符号整数，取整数的反数位。

示例:
输入:-123
输出:-321

输入:120
输出:21

输入:123
输出:321

乍一看，这个问题很容易。在 Ruby 中，只需要转换成字符串，反转，再转换回整数，Ruby 有所有这些很好的内置函数来做这些事情，而且很简单。

```
def reverse(x)

    x.to_s.reverse.to_i

end 
```

Enter fullscreen mode Exit fullscreen mode

但是，这不支持负数，因为反函数不支持负数。这就是事情的经过。

```
irb(main):016:0> x = -123
=> -123
irb(main):017:0> x.to_s.reverse.to_i
=> 321 
```

Enter fullscreen mode Exit fullscreen mode

我们真正想要的是-321，适应它的一种方法是创建一个变量，指示它是否应该是负的，并在必要时将它添加到 return 语句中。

```
def reverse(x)

    neg = x < 0 
    x.to_s.reverse.to_i
    return neg ? -x : x

end 
```

Enter fullscreen mode Exit fullscreen mode

快到了。我们没有考虑问题的关键部分。它是一个 32 位整数。

整数可能很大，但是在编程时有限制。从技术上讲，你可以有一个无限长的数，并且是一个整数，但是这也会破坏你正在处理的任何东西。所以当问题说 32 位整数时，它说的是最大的 32 位二进制数...

基数 2 数学中的 1111111111111111111111111111111 等于

10 进制数学中的 4294967295。

然而，问题还指定了有符号，所以第一位是+或-，结果是 31 1 而不是 32，所以我们最终得到的幻数是

Two billion one hundred and forty-seven million four hundred and eighty-three thousand six hundred and forty-eight

所以范围是-2147483648 到 2147483648 对吗？但是 0 呢？正整数得到它，所以这个范围的结果是

-2147483648 至 2147483647，不包括这些号码。

为了适应这种情况，我们必须在返回结果之前检查整数输入是大于还是小于这些范围。

```
def reverse(x)

    neg = x < 0
    x = x.to_s.reverse.to_i

    return  0 if (x > 2147483646 || x < -2147483647)

    return neg ? -x : x
end 
```

Enter fullscreen mode Exit fullscreen mode

如果你不想使用太多的内置函数，反过来构建总是好的做法。

```
def reverse(x)
    i = 0

    neg = x < 0
    x = x.to_s

    x.length % 2 == 0? (half = x.length/2) : (half = x.length/2 + 1)
    len = x.length - 1

    while i < half
      temp = x[i]        
      x[i] = x[len - i]
      x[len - i] = temp
      i += 1
    end

    x = x.to_i

    return  0 if (x > 2147483646 || x < -2147483647)

    return neg ? -x : x
end 
```

Enter fullscreen mode Exit fullscreen mode

在 Javascript 中，我们以
结束

```
var reverse = function(x) {
    const neg = x < 0

    x = Math.abs(x).toString().split("").reverse().join("")
    x = parseInt(x)

    if (neg) {
        x = - + x
    }

    if (x < -2147483647 || x > 2147483646){
        return 0
    } else {
        return x
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，parseInt 可以与它上面的行组合在一起，但是在不同的行上阅读会更容易一些。

直到我的下一次代码挑战。