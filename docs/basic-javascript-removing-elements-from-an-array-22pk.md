# 基本 Javascript:从数组中移除元素

> 原文：<https://dev.to/mshin1995/basic-javascript-removing-elements-from-an-array-22pk>

本周我将再次讨论 Javascript 的另一个基本主题。我将讨论从数组中移除元素的各种方法。虽然这个主题很简单，但是我经常发现自己混淆了某些方法和它们实际上是做什么的。通过写这个指南，我希望能够进一步巩固我在这些方法上的知识，并帮助其他可能处于相同情况的人。

### **1。shift()**

```
let breads = [wheat, ciabatta, sourdough, brioche]
breads.shift() // returns wheat
console.log(breads) // [ciabatta, sourdough, brioche] 
```

使用 shift 方法移除数组中的第一个元素。它返回第一个元素并完全修改原始数组。

### **2。pop()**

```
let fruits = [apple, banana, kiwi, orange]
fruits.pop() // returns orange
console.log(fruits) // [apple, banana, kiwi] 
```

使用 pop 方法移除数组中的最后一个元素。它返回最后一个元素并完全修改原始数组。

### **3。拼接()**

```
let cars = [Toyota, Ford, Jeep, BMW]
cars.splice(1, 1) // returns [Ford]
console.log(cars) // [Toyota, Jeep, BMW] 
```

拼接方法可用于在阵列中删除或添加元素。第一个参数是要移除或添加元素的索引位置。第二个参数是要从数组中移除的元素数量。在上面的示例中，参数要求移除索引 1 处的一个元素。此方法返回一个包含已移除元素的新数组，并修改原始数组。

使用此方法，您可以从数组中移除一定范围的元素。

```
let heroes = [Superman, Batman, Spiderman, Ironman, Thor, Hulk, Flash]
heroes.splice(2, 4) // returns [Spiderman, Ironman, Thor, Hulk]
console.log(heroes) // [Superman, Batman, Flash] 
```

### **4。slice()**

```
let shoes = [Nike, Adidas, Reebok, Jordan, Vans]
shoes.slice(1, 4) // returns [Adidas, Reebok, Jordan]
console.log(shoes) // [Nike, Adidas, Reebok, Jordan, Vans] 
```

slice 方法可用于从数组中移除元素，而无需修改原始数组。第一个参数指定起始索引位置，第二个参数指定结束索引位置(不包括结尾)。将使用这些索引范围内的元素创建一个新的数组对象。

我希望这对刚开始学习 Javascript 的人或者需要复习一个简单但重要的主题的人有所帮助。让我知道我是否遗漏了任何其他方法。感谢阅读！