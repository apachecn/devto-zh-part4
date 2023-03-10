# 如何在 Javascript 中将数组转换成对象

> 原文：<https://dev.to/afewminutesofcode/how-to-convert-an-array-into-an-object-in-javascript-25a4>

[来自 undraw.co 的图片](https://undraw.co/)

[最初发布于 afewminutesofcode.com](https://afewminutesofcode.com/how-to-convert-an-array-into-an-object-in-javascript/?utm_source=devto&utm_medium=website&utm_campaign=blogpost)

为了将一个数组转换成一个对象，我们将创建一个函数并赋予它两个属性，一个数组和一个键。

```
const convertArrayToObject = (array, key) => {}; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将减少数组，并根据传入的键为每个项目创建一个惟一的属性。

我们还需要记住设置一个初始值，并传入当前值(...obj 在下面)。

```
const convertArrayToObject = (array, key) => {
  const initialValue = {};
  return array.reduce((obj, item) => {
    return {
      ...obj,
      [item[key]]: item,
    };
  }, initialValue);
}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，现在如果我们注销我们的函数(传入数组和我们的键，在本例中是 id 属性的唯一标识符)，我们将看到数组现在是一个对象。

```
console.log(
  convertArrayToObject(
    [
      { id: 111, name: 'John', age: 29 },
      { id: 112, name: 'Sarah', age: 25 },
      { id: 122, name: 'Kate', age: 22 },
      { id: 123, name: 'Tom', age: 21 },
      { id: 125, name: 'Emma', age: 24 },
    ],
    'id',
  ),
); 
```

Enter fullscreen mode Exit fullscreen mode

返回

```
{
  111:{ id: 111, name: 'John', age: 29 },
  112:{ id: 112, name: 'Sarah', age: 25 },
  122:{ id: 122, name: 'Kate', age: 22 },
  123:{ id: 123, name: 'Tom', age: 21 },
  125:{ id: 125, name: 'Emma', age: 24 }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以通过 id 轻松地在数组中查找数据，并根据需要使用它。

[https://www.instagram.com/p/BziTc90D382/embed/captioned](https://www.instagram.com/p/BziTc90D382/embed/captioned)