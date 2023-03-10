# Array.reduce()的乐趣:通过名称获取嵌套对象

> 原文：<https://dev.to/sergeis/fun-with-array-reduce-get-a-nested-object-by-name-377j>

假设你在全局范围内有一个对象，比如

```
var level0 = {level1:{level2:{level3:'holy grail'}}}; 
```

你想要访问的最后一级，它的名字是一个字符串:

```
var nestedName = 'level0.level1.level2.level3'; 
```

当然，有许多方法可以获得 level3 的值，其中一种方法是使用 Array.reduce():

```
 var getNestedValueByName = name=>name.split('.').reduce((acc, next)=>acc[next], window);

console.log(getNestedValueByName(nestedName));
// holy grail 
```

也适用于数组，只是必须使用索引而不是名称:

```
var a = [['first', 'second'],['third', 'fourth']];

console.log(getNestedValueByName('a.1.0'));
// third 
```