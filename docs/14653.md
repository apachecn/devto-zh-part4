# 对象条目和来自条目

> 原文：<https://dev.to/harbolaez/object-entries-fromentrie-19ba>

了解`Object.entries()`和`Object.fromEntries()`对象方法。

* * *

```
/**
 * Object.entries()
 *
 * @return method returns a two-dimensional array of a given object's.
*/

/**
 * Object.fromEntries()
 *
 * @return methods return a object given a two-dimensional array.
*/ 
```

Enter fullscreen mode Exit fullscreen mode

使用`entries()` :

```
const person = { firstName: "henry", lastName: "arbolaez" }
const personArray = Object.entries(person)
/**
 * @return [["firstName", "henry"], ["lastName", "arbolaez"]]
 * 
*/

for(let [key, value] of personArray) {
  console.log(`${key}: ${value}`);
}

/**
 * @return
 *   firstName: henry
 *   lastName: arbolaez
*/ 
```

Enter fullscreen mode Exit fullscreen mode

使用`fromEntries()` :

```
/**
 * If we ever want to turn that 2D array back into a object 
 * we could use now Object.fromEntries(2dArray)
*/

const personArray = [["firstName", "henry"], ["lastName", "arbolaez"]];
Object.fromEntries(personArray);

/**
 * @return { firstName: "henry", lastName: "arbolaez" } 
*/ 
```

Enter fullscreen mode Exit fullscreen mode