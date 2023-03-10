# 理解打字稿中有差别的联合

> 原文：<https://dev.to/codewithahsan/understanding-discriminated-unions-in-typescript-5cd>

您可能已经知道，Typescript 是 Javascript 的超集；简直棒极了。关于 Typescript，我们每天都喜欢和听到许多特性。例如，我们可以看看接口、枚举、联合、交集、类型保护等等。

在这篇文章中，我们将关注*受歧视的工会*。我们将研究它们是什么，到本文结束时，您将会很好地理解在哪里可以使用它们。

### 受歧视的工会

区别联合，也称为*代数数据类型*或*标记联合*，是三种情况的组合:

*   判别式
*   联邦
*   防护类型

以上每一条我们都来理解一下，一个一个的举例。

#### 判别式

判别式是一个*单例类型*属性，在 union 的每个元素中都很常见。你可以在[这篇文章](https://medium.com/@tar.viturawong/using-typescripts-singleton-types-in-practice-f8b20b1ec3a6)中阅读更多关于 Typescript Singleton 类型的内容。

参见下面的例子:

```
enum CarTransmission {
  Automatic = 200,
  Manual = 300
}

interface IMotorcycle {
  vType: "motorcycle"; // discriminant
  make: number; // year
}

interface ICar {
  vType: "car"; // discriminant
  transmission: CarTransmission
}

interface ITruck {
  vType: "truck"; // discriminant
  capacity: number; // in tons
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到接口中的`vType`属性是*判别式*或*标签*。其他属性特定于相应的接口。

#### 工会

接口的并集可以简单地创建如下:

```
type Vehicle = IMotorcycle | ICar | ITruck; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以在代码中使用这种联合(类型),在这种情况下，变量中可以有多种类型的车辆。

#### 护卫型

考虑以下基于我们上面定义的接口的例子:

```
const evaluationFactor = Math.PI; // some global factor
function evaluatePrice(vehicle: Vehicle) {
  return vehicle.capacity * evaluationFactor;
}

const myTruck: ITruck = {vType: "truck", capacity: 9.5};
evaluatePrice(myTruck); 
```

Enter fullscreen mode Exit fullscreen mode

上述代码将导致 typescript 编译器抛出以下错误:

```
Property 'capacity' does not exist on type 'Vehicle'.
  Property 'capacity' does not exist on type 'IMotorcycle'. 
```

Enter fullscreen mode Exit fullscreen mode

原因是接口`IMotorCycle`上不存在属性`capacity`。嗯，其实在`ICar`也不存在，但是已经破了检查`IMotorCycle`，在`ICar`之前声明，所以没到检查`ICar`。

好吧，我们怎么解决这个问题？当然是使用*型防护装置*。请看下面的例子:

```
function evaluatePrice(vehicle: Vehicle) {
  switch(vehicle.vType) {
    case "car":
      return vehicle.transmission * evaluationFactor;
    case "truck":
      return vehicle.capacity * evaluationFactor;
    case "motorcycle":
      return vehicle.make * evaluationFactor;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`switch` & `case`操作符通过充当*类型的守卫*来为我们解决问题，确保我们访问的是在`evaluatePrice`方法中得到的`vehicle`的正确属性。

如果你使用的是像 [VSCode](https://code.visualstudio.com/) 这样的编辑器，你会注意到在使用这些*类型的守卫*之前，智能感知可能只在你输入`vehicle.`的时候把`vType`显示为一个属性。但是如果你现在在任何一个`case`语句中输入`vehicle.`，你会看到智能感知的适当属性会从适当的接口中显示出来。

#### 检查穷尽性

如果我们想给 union `Vehicle`引入一个新的类型/接口会怎么样？您可能认为`evaluatePrice`函数没有为此处理案例。这是准确的。但是我们需要编译器在构建时让我们知道(或者使用`tslint`等)我们需要覆盖类型`Vehicle`的所有变体。这被称为*穷尽性检查*。确保我们涵盖联合的所有变体的方法之一是使用`never`，typescript 编译器使用它来实现穷举。

假设我们向`Vehicle`联合中添加了一个新类型`IBicycle`，如下所示:

```
interface IBicycle {
  vType: "bicycle";
  make: number;
}

type Vehicle = IMotorcycle | ICar | ITruck | IBicycle; 
```

Enter fullscreen mode Exit fullscreen mode

我们将能够使用`never`进行如下的穷尽性检查:

```
function evaluatePrice(vehicle: Vehicle) {
  switch(vehicle.vType) {
    case "car":
      return vehicle.transmission * evaluationFactor;
    case "truck":
      return vehicle.capacity * evaluationFactor;
    case "motorcycle":
      return vehicle.make * evaluationFactor;
    default:
      const invalidVehicle: never = vehicle;
      return throw new Error(`Unknown vehicle: ${invalidVehicle}`);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面应该显示编辑器中的错误(使用 lint 工具)或编译时的错误，如下所示:

```
Type 'IBicycle' is not assignable to type 'never'. 
```

Enter fullscreen mode Exit fullscreen mode

上面显示我们也需要处理`IBicycle`。一旦我们在下面的`evaluatePrice`方法中为`IBicycle`添加了`case`，这个错误就应该消失了。

```
function evaluatePrice(vehicle: Vehicle) {
  switch(vehicle.vType) {
    case "car":
      return vehicle.transmission * evaluationFactor;
    case "truck":
      return vehicle.capacity * evaluationFactor;
    case "motorcycle":
      return vehicle.make * evaluationFactor;
    case "bicycle":
      return vehicle.make * evaluationFactor;
    default:
      const invalidVehicle: never = vehicle;
      return throw new Error(`Unknown vehicle: ${invalidVehicle}`);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 [Stackblitz](https://stackblitz.com/edit/discriminated-unions-typescript) 上找到一个工作示例。

### 结论

有区别的联合非常强大，结合了 Typescript 基于区别符/标记来区分类型的能力。如果使用得当，这可以为代码带来显著的可读性，并且在编写具有函数的可靠动态类型时非常有用。

#### 进一步阅读

使用 TypeScript 进行彻底的类型检查！
[高级类型——打字稿](https://www.typescriptlang.org/docs/handbook/advanced-types.html)
[CodingBlast 打字稿系列](https://codingblast.com/series/typescript/)

如果你从这篇文章中学到了一些新东西，别忘了把它展示给你的朋友和同事。编码快乐！