# 基本 Javascript:从数组中删除重复项

> 原文：<https://dev.to/mshin1995/back-to-basics-removing-duplicates-from-an-array-55he>

作为一个目前正在寻找软件开发工作的人，我知道在编码方面有一个坚实的基础是多么重要。虽然为测试这些基础知识的编码挑战而练习可能不是最令人兴奋的，但通过让你的大脑以不同的方式思考，它们肯定是实用的。我认为这将有助于我个人和任何人回顾这些技术面试，回到基础，触及一些核心话题。本周，我想提出多种方法来删除数组中的重复项。

### **1。过滤器()**

```
function removeDuplicates(array) {
  return array.filter((a, b) => array.indexOf(a) === b)
}; 
```

Enter fullscreen mode Exit fullscreen mode

### **2。forEach()**

```
function removeDuplicates(array) {
  let x = {};
  array.forEach(function(i) {
    if(!x[i]) {
      x[i] = true
    }
  })
  return Object.keys(x)
}; 
```

Enter fullscreen mode Exit fullscreen mode

检查数组中的每个元素是否唯一。

### **3。设置**

```
function removeDuplicates(array) {
  array.splice(0, array.length, ...(new Set(array)))
}; 
```

Enter fullscreen mode Exit fullscreen mode

创建新的集合会存储数组中的唯一元素。我认为最简单的方法

### **4。地图()**

```
function removeDuplicates(array) {
  let a = []
  array.map(x => 
    if(!a.includes(x) {
      a.push(x)
    })
  return a
}; 
```

Enter fullscreen mode Exit fullscreen mode

映射整个数组，并将唯一元素添加到新数组中。

我希望有些人觉得这很有帮助！你们还能想到其他什么解决方案吗？