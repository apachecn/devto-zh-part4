# TypeScript:类型与接口

> 原文：<https://dev.to/stereobooster/typescript-type-vs-interface-2n0c>

在我最近的一次 pr 中，我将所有的`interface`都改成了`type`，因为`type`已经比`interface`多了。在评估中，我被要求恢复更改。我做到了，但我也想知道`interface`和`type`之间的实际区别。让我们来解决这个问题。我在这篇文章中使用了最新的 TS (v3.5.1)作为例子。

## 相似之处

### 记录

```
interface IAnimal {
  name: string;
}

type Animal = {
  name: string;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 仿制药

```
interface IAnimal<P = string> {
  name: P;
}

type Animal<P = string> = {
  name: P;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 路口

```
type Robot = {
  power: number;
};

interface IRobot {
  name: string;
}

interface IRoboAnimal1 extends IAnimal, IRobot {}
interface IRoboAnimal2 extends IAnimal, Robot {}
interface IRoboAnimal3 extends Animal, IRobot {}
interface IRoboAnimal4 extends Animal, Robot {}

type RoboAnimal1 = Animal & Robot;
type RoboAnimal2 = Animal & IRobot;
type RoboAnimal3 = IAnimal & Robot;
type RoboAnimal4 = IAnimal & IRobot; 
```

Enter fullscreen mode Exit fullscreen mode

### `implements`

```
class Dog implements IAnimal {
  name: string = "good dog";
}

class Cat implements Animal {
  name: string = "Where is my food, human?";
} 
```

Enter fullscreen mode Exit fullscreen mode

### 扩展类

```
class Control {
  private state: any;
}

interface ISelectableControl extends Control {
  select(): void;
}

type SelectableControl = Control & {
  select: () => void;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 功能

```
type Bark = (x: Animal) => void;

interface iBark {
  (x: Animal): void;
} 
```

Enter fullscreen mode Exit fullscreen mode

和泛型:

```
type Bark = <P = Animal>(x: P) => void;

interface iBark {
  <P = Animal>(x: P): void;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 递归声明

```
type Tree<P> = {
  node: P;
  leafs: Tree<P>[];
};

interface ITree<P> {
  node: P;
  leafs: ITree<P>[];
} 
```

Enter fullscreen mode Exit fullscreen mode

### 精确

```
type Close = { a: string };
const x: Close = { a: "a", b: "b", c: "c" };
// Type '{ a: string; b: string; c: string; }' is not assignable to type 'Close'.

interface IClose {
  a: string;
}
const y: IClose = { a: "a", b: "b", c: "c" };
// Type '{ a: string; b: string; c: string; }' is not assignable to type 'IClose'. 
```

Enter fullscreen mode Exit fullscreen mode

### 可转位

```
type StringRecord = {
  [index: string]: number;
};

interface IStringRecord {
  [index: string]: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 差异

### 原始类型

您只能使用类型来别名基本类型

```
type NewNumber = number;

interface INewNumber extends number {}
// 'number' only refers to a type, but is being used as a value here.

// this works
interface INewNumber extends Number {}
// but don't forget that 1 instanceof Number === false; 
```

Enter fullscreen mode Exit fullscreen mode

### 元组

不能用接口
声明元组

```
type Tuple = [number, number];

interface ITuple {
  0: number;
  1: number;
}

[1, 2, 3] as Tuple; // Conversion of type '[number, number, number]' to type '[number, number]' may be a mistake

[1, 2, 3] as ITuple; // Ok 
```

Enter fullscreen mode Exit fullscreen mode

### 不相交的并集

分离联合仅对类型有效:

```
type DomesticAnimals = { type: "Dog" } | { type: "Cat" }; 
```

Enter fullscreen mode Exit fullscreen mode

并且不能将不相交的联合类型与`extends`
一起使用

```
interface IDomesticAnimals extends DomesticAnimals {}
// An interface can only extend an object type or intersection of object types with statically known members 
```

Enter fullscreen mode Exit fullscreen mode

### `new`

你可以声明`new`的类型

```
interface IClassyAnimal {
  new (name: string);
} 
```

Enter fullscreen mode Exit fullscreen mode

它不像你预期的那样工作

```
class Parrot implements IClassyAnimal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}
// Class 'Parrot' incorrectly implements interface 'IClassyAnimal'.
//  Type 'Parrot' provides no match for the signature 'new (name: string): void'. 
```

Enter fullscreen mode Exit fullscreen mode

`constructor`好像也不行

```
interface IClassyAnimal {
  constructor(name: string): void;
}

class Parrot implements IClassyAnimal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}
// Class 'Parrot' incorrectly implements interface 'IClassyAnimal'.
//  Types of property 'constructor' are incompatible.
//    Type 'Function' is not assignable to type '(name: string) => void'.
//      Type 'Function' provides no match for the signature '(name: string): void'. 
```

Enter fullscreen mode Exit fullscreen mode

### 每个作用域只有一个声明

每个作用域只能声明一次类型

```
type Once = { a: string };
type Once = { b: string };
// Duplicate identifier 'Once'. 
```

Enter fullscreen mode Exit fullscreen mode

您可以在每个作用域中多次声明接口(最终结果将是所有声明的总和)

```
interface IOnce {
  a: string;
}
interface IOnce {
  b: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 效用类型

大多数时候你会使用类型而不是接口来创建实用程序类型，例如:

```
export type NonUndefined<A> = A extends undefined ? never : A; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在 TS 的早期版本中，并非所有这些都是可能的，所以人们习惯了接口。但在 TS 的最新版本中，似乎类型更有能力，我们可以随时使用它们🤔。还是我错过了什么？

TS 中有许多细微的差别——对于一个小例子(我已经展示过了)可能有用，但是对于大例子就不行了。 **[如有遗漏请指正](https://github.com/stereobooster/stereobooster.com/edit/master/content/posts/typescript-type-vs-interface/index.md)。**

[献给@thekitze](https://twitter.com/thekitze/status/1150793200510148614) 。