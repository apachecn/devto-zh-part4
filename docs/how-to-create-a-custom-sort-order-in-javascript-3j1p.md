# 如何在 javascript 中创建自定义排序顺序

> 原文：<https://dev.to/afewminutesofcode/how-to-create-a-custom-sort-order-in-javascript-3j1p>

[来自 undraw.co 的图片](https://undraw.co/)

[最初发布于 afewminutesofcode.com](https://afewminutesofcode.com/how-to-create-a-custom-sort-order-in-javascript/?utm_source=devto&utm_medium=website&utm_campaign=blogpost)

通常我们会希望按照数字或者按照字母顺序对数组[进行排序，但是也有一些情况下我们可能需要自定义排序顺序。](https://afewminutesofcode.com/how-to-sort-an-array-numerically-in-javascript/?utm_source=devto&utm_medium=website&utm_campaign=blogpost)

以下面的例子为例，我们想先显示正在进行的项目，然后是**待办事项**，最后是**已完成事项**。

```
const tasks = [
  {id:1, title: 'Job A', status: 'done'},
  {id:2, title: 'Job B', status: 'inProgress'},
  {id:3, title: 'Job C', status: 'todo'},
  {id:4, title: 'Job D', status: 'inProgress'},
  {id:5, title: 'Job E', status: 'todo'}
] 
```

我们将首先创建一个具有我们想要的排序顺序的数组。

```
const sortBy = ['inProgress', 'todo', 'done'] 
```

然后我们将使用 **reduce** 创建一个函数，将数据作为对象输出。`{inProgress: 0, todo: 1, done: 2}`以数组项为键，索引为值。

```
const sortByObject = data => data.reduce(
  (obj,item,index) => ({
    ...obj,
    [item]:index
}), {}
)
console.log(sortByObject(sortBy))
/* {inProgress: 0, todo: 1, done: 2} */ 
```

现在我们有了我们的排序设置，我们可以用一个可重用的函数把它放在一起，这个函数传入一个数组(`data`)、一个`sortby`数组和一个`sortField`，这样我们就知道对哪个字段进行排序。

```
const customSort = ({data, sortBy, sortField}) => {
  const sortByObject = sortBy.reduce(
  (obj, item, index) => ({
      ...obj,
      [item]: index
  }), {})
  return data.sort((a, b) => sortByObject[a[sortField]] - sortByObject[b[sortField]])
}

console.log(customSort({data:tasks, sortBy, sortField: 'status'})) 
```

这将按照我们的自定义顺序进行排序，但是如果列表中有一个项目具有不同的状态(不在我们的排序顺序中)，就会出现问题。因此，为了处理这个问题，我们将设置一个默认的排序字段来捕获排序中不需要的所有项目。

```
const tasksWithDefault = tasks.map(item => ({...item, sortStatus: sortBy.includes(item.status) ? item.status:'other'})) 
```

有了这个，如果我们再次注销我们的函数，这一次传入我们更新的排序字段，那么我们现在就有了正确的排序顺序，其他项目在列表的底部。

```
console.log(customSort({
  data:tasksWithDefault,
  sortBy: [...sortBy,'other'],
  sortField: 'sortStatus'
  })) 
```

[https://codesandbox.io/embed/friendly-fast-4sp4f](https://codesandbox.io/embed/friendly-fast-4sp4f)

如果你正在寻找更多的提示，或者想在我的下一篇文章发表时得到通知，请点击这里在线关注我:

[Instagram](https://instagram.com/afewminutesofcode)
[脸书](https://facebook.com/afewminutesofcode)
[afewminutesofcode.com](https://afewminutesofcode.com/?utm_source=devto&utm_medium=website&utm_campaign=blogpost)
[推特](http://twitter.com/afewminsofcode)
[Pinterest](https://www.pinterest.com.au/afewminutesofcode)