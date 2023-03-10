# array . includes()中更易于读取的条件

> 原文：<https://dev.to/gabrielrufino/condicional-mais-legivel-com-array-includes-4oe1>

您知道 javascript 的 array . includes()函数吗？此函数是在 ES7 中指定的，可以使条件更易于阅读。

此函数确定参数是否包含在数组中。

```
const numbers = [1, 2, 3, 4]
const strings = ['Gabriel', 'Rufino']

numbers.includes(3) // true
numbers.includes(6) // false
strings.includes('Rufino') // true
strings.includes('Fernando') // false 
```

Enter fullscreen mode Exit fullscreen mode

通过了解此功能，现在可以编写条件，将变量与许多更容易读取的可能性进行比较，方法是使用变量作为参数，将“`or`”替换为“`Array.includes()`”。请参见以下示例:

### [T2`or`](#usando-o-operador-raw-or-endraw-)

```
function get(request, response) {
  const access = request.access

  if (access === 'maintainer' || access === 'admin' || access === 'developer') {
    return response.json({ allowed: true })
  } else {
    return response.json({ allowed: false })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 乌桑多`Array.includes()`

```
function get(request, response) {
  const access = request.access

  if (['maintainer', 'admin', 'developer'].includes(access)) {
    return response.json({ allowed: true })
  } else {
    return response.json({ allowed: false })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## Funciona com `NaN`

```
NaN === NaN // false
[1, 2, 3, NaN].includes(NaN) // true 
```

Enter fullscreen mode Exit fullscreen mode