# 6-10 点挑战问题#001 解决方案

> 原文：<https://dev.to/akbhairwal/6-10pm-challenge-problem-001-solution-3gmd>

[问题#001](https://dev.to/akbhairwal/6-10pm-challenge-problem-001-172p)

将零移动到末尾

```
public int[] moveZerosToEnd(int[] arr) {
    int j = 0;
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] != 0) {
            arr[j] = arr[i];
            j++;
        }
    }
    for (; j < arr.length; j++) {
        arr[j] = 0;
    }

    return arr;
} 
```