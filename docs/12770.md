# 用 Javascript 解决两个和问题

> 原文：<https://dev.to/yongliang24/solve-the-two-sum-problem-with-javascript-4jlk>

### 目标

给定一个数字数组和一个目标数字，从数组中找出两个数字之和等于目标数字。不能对同一指数求和两次。

### 残暴的武力解决

使用嵌套的 for 循环来检查数组中所有可能的和，并在读取时匹配目标数。这个解决方案的时间复杂度是 O(n^2).

```
function twoSum(numArray, numTarget)
{
 const arraySize = numArray.length;

 for(let i = 0; i < arraySize; i++){

     for(let j= i + 1; j < arraySize; j++){

       if(numTarget - numArray[i] === numArray[j]){

          return `The sum of indices ${i} and ${j} equals ${numTarget}` 
        }
     }
  }
 return "target not found."
}

//calling 

twoSum([1,2,3], 5)

//returns 'The sum of indices 1 and 2 equals 5'

twoSum([1,2,3], 6) //returns "target not found" 
```

### 使用键/值对解决方案

我们可以不考虑 index1 + index2 = target 的和，而是考虑 index2 = target - index1，然后在 key/value 对象中查找 index2，我们将把数组值存储为键，把数组索引存储为值。

对象或哈希表中的查找时间是 O(1)常数时间，因此，该实现将时间复杂度提高到 O(N)。

伪代码:

1.  创建一个空对象
2.  用 for 循环迭代数组
3.  假设 num 2 = target-num 1(num 2 是我们要在对象中查找的数字)
4.  在每次迭代中，将值/对添加到对象中
5.  如果 num2 在对象中，我们已经找到它，返回索引和总和

```
function twoSum(numArray, target){

  const numObject = {} //create an empty object

  for(let eachNum in numArray){

    const otherNum = target - numArray[eachNum]

    if(otherNum in numObject){

      return `${otherNum} + ${numArray[eachNum]} = ${target}`
    }

    numObject[numArray[eachNum]] = eachNum

//adding key/value has to go after the if statement to avoid adding the same index twice.
  }

  return "target not found"
}

twoSum([1,2,3,4,5], 8) //output: '3 + 5 = 8'
twoSum([1,2,3,4,5], 10) //"target not found" 
```