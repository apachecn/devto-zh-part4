# 类型脚本泛型的用例

> 原文：<https://dev.to/juliang/a-use-case-for-generics-in-typescript-20j7>

对某些人来说，以下内容可能有点显而易见。但是我突然想起了一些事情，我想我应该把它写下来。

## 当泛型是个好主意时

假设我们写了一个函数，返回集合/数组中“最老的”项:

```
function getOldest(items: Array<{ age: number }>) {
  return items.sort((a, b) => b.age - a.age)[0];
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数可以用任何类型的对象数组来调用，只要它们包含类型为`number`的`age`属性。

为了帮助推理，我们给它起个名字:

```
type HasAge = { age: number }; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的函数可以这样注释:

```
function getOldest(items: HasAge[]): HasAge {
  return items.sort((a, b) => b.age - a.age)[0];
} 
```

Enter fullscreen mode Exit fullscreen mode

**太好了！**现在我们可以对任何符合`HasAge`接口的对象使用这个函数:

```
const things = [{ age: 10 }, { age: 20 }, { age: 15 }];
const oldestThing = getOldest(things);

console.log(oldestThing.age); // 20 ✅ 
```

Enter fullscreen mode Exit fullscreen mode

因为`oldestThing`的类型被推断为`HasAge`，所以我们可以访问它的`.age`属性。

但是如果我们有更复杂的类型呢？

```
type Person = { name: string, age: number};

const people: Person[] = [
  { name: 'Amir', age: 10 }, 
  { name: 'Betty', age: 20 }, 
  { name: 'Cecile', age: 15 }
 ];

const oldestPerson = getOldest(people); // 🙂 no type errors 
```

Enter fullscreen mode Exit fullscreen mode

**这个工作**，但是现在`oldestPerson`的推断类型是`HasAge`。我们已经失去了`Person`型。因此，我们无法(安全地)访问它的`.name`属性。

```
console.log(oldestPerson.name); // ❌ type error: Property 'name' does not exist on type 'HasAge'. 
```

Enter fullscreen mode Exit fullscreen mode

注释`oldestPerson:Person`不起作用:

```
const oldestPerson: Person = getOldest(people); // ❌ type error
// Property 'name' is missing in type 'HasAge' but required in type 'Person'. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用[类型断言](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions)，但这不是一个好主意。([为什么？](https://basarat.gitbooks.io/typescript/docs/types/type-assertion.html#assertion-considered-harmful) )

```
const oldestPerson = getOldest(people) as Person; // 🚩
console.log(oldestPerson.name); // no type error 
```

Enter fullscreen mode Exit fullscreen mode

[你可以在 TypeScript Playground](https://www.typescriptlang.org/play/#code/PTAEHUEsBcAsHsCu1QHECmA7dAnSBjAZwChjoBPAB3VAAkBDQgQQHMaBeUAb1HrYC5QmRAFsARrlABfANykAZokz5okeJlBtoAeQA2AE3SFoAChjoRhQQ2ZsA2gF0AlNcasaXYqFA500RDga5pYAdITwOKYm9AA0oGJOoOwAfPEhfDQAtLzpbE52AAwOclKk+OrGoHCQmCyESaB2PBmCAIwF0nHNAqAATB1SXbw9rQCs0sXE5ZiV8AZG0AAqsDUsDVp6hsYm1bWETnJTFXPoIbrwLCZzW0srtbnoB6AgfR2AoOSkFNSgAAq44RpODxMPQROhBMY8LUZMNwUJRBIcNJDtNKtR4JRdHC-jgAY4GnYvNwhKC4QAiJgiSA4MlxFqgdqdInA0mCMkAIT8FFpsME-SZ3hZYLZAGF0PhIFiefSxtJiJMjjMUNcFus-JsFiZ0ZjHjCXoBeDcAQntCeBVKg0XA4CL1MTIEL20gvVEnM4XK7zYwhEFgp4vQAy5L8rdRIuRQAByb3oMOgfTwIwmlDoAAekFmGi+NDD7noYixYZCjrAqOVHugOIBgnL6jVOlLWvQGKxvrAAYzoEtEWILx+QdwFHDkejqdAVMIhFWoBqZu+YZs7mjtpQvgAjohqeh9JP0+bw1XMPnC6AAKrj2rTmiMQh9tQzMoVEs3Pc1jXbbVN3j1Pd6sD6wCVewneE0eBN0oHB6BUAh0EVcIsVdS4VWMPcvVJZsTXPdscCtHA4kXNCWHgYDQFA8DVHwC0YFgXBiCAA) 试试这个。

## 使用泛型

我们可以做得更好。我们可以把它变成一个[通用](https://www.typescriptlang.org/docs/handbook/generics.html)函数。

```
function getOldest<T extends HasAge>(items: T[]): T {
  return items.sort((a, b) => b.age - a.age)[0];
}

const oldestPerson = getOldest(people); // ✅ type Person 
```

Enter fullscreen mode Exit fullscreen mode

**成功！！**现在`oldestPerson`的推断类型是`Person`！

因此我们可以访问它的`.name`属性。

这是另一个有两种不同类型的例子:

```
type Person = {name: string, age: number};
const people: Person[] = [
  { name: 'Amir', age: 10 }, 
  { name: 'Betty', age: 20 }, 
  { name: 'Cecile', age: 15 }
 ];

type Bridge = {name: string, length: number, age: number};
const bridges = [
{ name: 'London Bridge', length: 269, age: 48 },
{ name: 'Tower Bridge', length: 244, age: 125 },
{ name: 'Westminster Bridge', length: 250, age: 269 }
]

const oldestPerson = getOldest(people); // type Person
const oldestBridge = getOldest(bridges); // type Bridge

console.log(oldestPerson.name); // 'Betty' ✅
console.log(oldestBridge.length); // '250' ✅ 
```

Enter fullscreen mode Exit fullscreen mode

[你可以在 TypeScript Playground](https://www.typescriptlang.org/play/#code/PTAEFUGcEsDsHNQHECmsUCdoGNICg8AXATwAcVQAJAQ0gEF4KBeUAb1GsYC5RYBXALYAjTKAC+AbgIAzPrGyFoAe1ihGhAPIAbACYpIhADwAVUCgAehNDshVaDFAD4AFNCsDIPYwG0AugEovNjxQUAwUQj4MVTcUDwA6SCUMQmdnagAaUCF-UCZHbPjOCgBaDiLGf28ABl8pMQISclAABUwk1RZ2WGoBFB4DLAQJDm5eQREMcSlsFQNQciVSLX7W9pU-PNBvELZeXtWAIjoBaAxDrOKeAEZq8Qzd7oOeQ4AhCJIL0dWAJjuxB6hJ59F4AYRQ2GgKy+V1A1wArOI8HVGmQKK8sDpGFtgatBnB4CMVghCAALHj8YSYEawymTaZ4WaweZCTGMWwsHZA-Yg0CHAAyKh0KlAGOgWJQX2J8DJPB+ADYAJyXMYAFgAHPdHjyjsYlAB3URiiVStAy8mgH6q1Uq1bXH6IgHanq8w4AdX0hFOzKsU2NjFNJItDuqtrlSqRKMZc0IoCUuk9bQwHS26m0egMzkWyxQ-hGIFATQoSY60Z9cYTBn9zDUEXTnucrPF7LzoALRdFbJQBCZSRW8S0SngznjGcIJZU8RdufzYAA5O9CCQ56BAKDkZb7KAHQ5HlcI1YHZrJrYLc5DK-XQA) 试试这个

## 不需要泛型时

即使您的函数接受符合`HasAge`接口的对象；只要你不想返回相同的类型，你就不需要泛型。

```
function isFirstOlder<T extends HasAge>(a: T, b: T) {
  return a.age > b.age;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数不需要是通用的。我们可以简单地写:

```
function isFirstOlder(a: HasAge, b: HasAge) {
  return a.age > b.age;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

*   [泛型](https://www.typescriptlang.org/docs/handbook/generics.html)(打字手册)
*   类型断言(类型脚本手册)
*   [类型断言](https://basarat.gitbooks.io/typescript/docs/types/type-assertion.html) (Basarat 的 TypeScript 深潜)
*   [打字操场](https://www.typescriptlang.org/play/)

## 反馈？

我很想听听你的想法。你还有其他的用例吗？例子？

* * *

Joshua Coleman 在 [Unsplash](https://unsplash.com/) 上拍摄的照片