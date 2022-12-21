# 6-10 点挑战问题#002 解决方案

> 原文：<https://dev.to/akbhairwal/6-10pm-challenge-problem-002-solution-2pm1>

[问题#002](https://dev.to/akbhairwal/6-10pm-challenge-problem-002-1m20)

**阵列产品阵列**

```
public  int[] solve(int[] arr) {
       int [] out = new int[arr.length];
       int product =1;

       for(int i=0;i<arr.length;i++){
           out[i]=product;
           product = product*arr[i];
        }

        product=1;
        for(int j=arr.length-1;j>=0;j--){
            out[j] = out[j]*product;
            product = product* arr[j];
        }
      return out;
   } 
```

n 是数组的大小
时间复杂度是 O(n)
空间复杂度是 O(n)