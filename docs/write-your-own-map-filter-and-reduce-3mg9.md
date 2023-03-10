# 写你自己的地图过滤器和减少

> 原文：<https://dev.to/powerc9000/write-your-own-map-filter-and-reduce-3mg9>

所以我们大多数人都知道这些功能已经存在。

```
[].map();
[].filter();
[].reduce(); 
```

但是它们内部是如何工作的呢？这其实很简单。所以还是自己写吧。凉爽的

### 地图

```
function map(array, cb){
   const result = [];
   for(let i = 0; i < array.length; i++){
      const item = array[i];
      result.push(cb(item, i));
   }
   return result;
} 
```

地图很简单。

### 滤镜

```
function filter(array, cb){
    const result = [];
    for(let i = 0; i < array.length; i++){
       const item = array[i];
       const check = cb(item, i);
       if(check){
            result.push(item);
       }
    }
    return result;

} 
```

也不算太强硬。

### 减少

```
function reduce(array, cb, init){
    let result = init;
    for(let i = 0; i < array.length; i++){
        const item = array[i];
        result = cb(result, item, i);
    }

    return result;
} 
```

Reduce 稍微复杂一点，但也不是太疯狂。我们只是确保将他们上一次迭代传递的内容返回到下一次迭代。

## 最后注:

有趣的是。Reduce 可以是我们所有其他循环的基础结构，我们可以用它来实现`map`和`filter`

### 地图使用缩小

```
function map(array, cb){
    return reduce(array, (carry, item, i) => {
        const result = cb(item, i);
        carry.push(result);
        return carry;
    }, [])
} 
```

### 过滤使用减少

```
function filter(array, cb){
    return reduce(array, (carry, item, i) => {
        const check = cb(item, i);
        if(check){
            carry.push(result);
        }
        return carry;
    }, []);
} 
```

无论如何，除非你有一个好的理由，否则不要使用你自己的地图。但是很高兴知道事情是如何在一个较低的水平上工作的。这样你就可以在未来更好地利用它们。

感谢阅读。