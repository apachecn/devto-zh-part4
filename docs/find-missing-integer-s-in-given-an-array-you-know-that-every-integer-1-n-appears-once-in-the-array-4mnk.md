# 在给定的数组中查找缺失的整数。你知道每个整数 1-N 在数组中出现一次

> 原文：<https://dev.to/kashifraza/find-missing-integer-s-in-given-an-array-you-know-that-every-integer-1-n-appears-once-in-the-array-4mnk>

以下是解决方案

```
var numbers = [0,1,3,4,5,7,8]; // Missing 2,6
var missing = [];

// Find the missing array items
for ( var i = 0; i < numbers.length; i++ ) {

    if ( (numbers[i+1] - numbers[i]) > 1 ) {
        missing.push( numbers[i+1] - numbers[1] );   
    }
}

console.log( missing );

```