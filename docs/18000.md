# Javascript 中的数字格式化程序

> 原文：<https://dev.to/uzochukwueddie/number-formatter-in-javascript-3cl5>

在这个简短的教程中，我们将使用 Javascript 创建一个简单的数字格式化程序。大于或等于 1000 的数字将被缩短并替换为数字类别符号。符号示例:

```
Thousand    = K
Million     = M
Billion     = G
Trillion    = T
Quadrillion = P
Quatrillion = E 
```

诸如此类。

下面的方法是数字格式化功能

```
numberFormatter(number, digits){
    const symbolArray = [
      { value: 1, symbol: "" },
      { value: 1E3, symbol: "K" },
      { value: 1E6, symbol: "M" },
      { value: 1E9, symbol: "G" },
    ];
    const regex = /\.0+$|(\.[0-9]*[1-9])0+$/;
    let result = "";

    for(let i = 0; i < symbolArray.length; i++) {
        if (number >= symbolArray[i].value) {
            result = (number / symbolArray[i].value).toFixed(digits).replace(regex, "$1") + symbolArray[i].symbol;
        }
    }
    return result;
} 
```

其他可以添加的值有

```
1E12 = 1000000000000
1E15 = 1000000000000000
1E18 = 1000000000000000000 
```

## 例子

```
numberFormatter(1000, 1) = "1K"
numberFormatter(10000, 1) = "10K"
numberFormatter(2134564, 1) = "2.1M"
numberFormatter(5912364758694, 3) = "5.912T" 
```

你可以关注我的推特，也可以订阅我的 T2 YouTube 频道。