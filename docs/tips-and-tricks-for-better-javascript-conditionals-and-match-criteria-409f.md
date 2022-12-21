# 更好的 JavaScript 条件和匹配标准的技巧和诀窍

> 原文：<https://dev.to/proticm/tips-and-tricks-for-better-javascript-conditionals-and-match-criteria-409f>

> 最初发布于[devinduct.com](https://devinduct.com/blogpost/35/tips-and-tricks-for-better-javascript-conditionals-and-match-criteria)

## 简介

如果你喜欢看干净的代码，像我一样，你会试着用尽可能少的条件语句来写你的代码。一般来说，面向对象编程使我们能够避免条件句，并用多态和继承来代替它们。我认为我们应该尽可能地遵循这些原则。

正如我在另一篇文章中提到的，[JavaScript Clean Code Best Practices](https://devinduct.com/blogpost/22/javascript-clean-code-best-practices)，你不仅仅是在为机器写代码，你是在为*、【未来的自己】、*、【其他人】、*写代码。*

另一方面，由于各种原因，我们可能会在代码中使用条件语句。也许我们有一个紧迫的期限来修复一个 bug，或者不使用条件语句会对我们的代码库造成太大的改变，等等。本文旨在帮助您了解这些情况，并帮助您组织所使用的条件语句。

## 提示

以下是关于如何组织`if...else`陈述以及如何写得更少以获得更多的提示。尽情享受吧！

### 1。首要的事情，琐碎，但不琐碎

不要使用否定条件句(它们可能会令人困惑),对`boolean`变量使用条件简写。我怎么强调这一点都不为过，尤其是关于否定条件句的部分。这是一种不自然的做事方式。

坏的

```
const isEmailNotVerified = (email) => {
  // implementation
}

if (!isEmailNotVerified(email)) {
  // do something...
}

if (isVerified === true) {
  // do something...
} 
```

好的

```
const isEmailVerified = (email) => {
  // implementation
}

if (isEmailVerified(email)) {
  // do something...
}

if (isVerified) {
  // do something...
} 
```

现在，当我们把上面的事情搞清楚了，我们就可以开始了。

### 2。对于多个条件，使用`Array.includes`

假设我们要在函数中检查汽车型号是`renault`还是`peugeot`。代码可能看起来像这样:

```
const checkCarModel = (model) => {
  if(model === 'renault' || model === 'peugeot') { 
    console.log('model valid');
  }
}

checkCarModel('renault'); // outputs 'model valid' 
```

考虑到我们只有两个模型，这样做看起来是可以接受的，但是如果我们想要对另一个模型进行检查呢？或者更多？如果我们添加更多的`or`语句，代码将更难维护，也不那么干净。为了让它更简洁，我们可以将函数重写为这样:

```
const checkCarModel = (model) => {
  if(['peugeot', 'renault'].includes(model)) { 
    console.log('model valid');
  }
}

checkCarModel('renault'); // outputs 'model valid' 
```

上面的代码看起来已经更好了。为了使它更好，我们可以创建一个变量来保存汽车模型:

```
const checkCarModel = (model) => {
  const models = ['peugeot', 'renault'];

  if(models.includes(model)) { 
    console.log('model valid');
  }
}

checkCarModel('renault'); // outputs 'model valid' 
```

现在，如果我们想要对更多的模型进行检查，我们需要做的就是添加一个新的数组项。同样，如果它很重要，我们可以在函数范围之外的某个地方声明`models`变量，并在我们需要它的任何地方重用它。考虑到我们只需要更改代码中的一个地方，这样我们就可以集中管理它，使维护变得轻而易举。

### 3。使用`Array.every`或`Array.find`匹配所有标准

在这个例子中，我们想检查是否每个汽车型号都是传递给我们函数的型号。为了以更加`imperative`的方式实现这一点，我们应该这样做:

```
const cars = [
  { model: 'renault', year: 1956 },
  { model: 'peugeot', year: 1968 },
  { model: 'ford', year: 1977 }
];

const checkEveryModel = (model) => {
  let isValid = true;

  for (let car of cars) {
    if (!isValid) {
      break;
    }
    isValid = car.model === model;
  }

  return isValid;
}

console.log(checkEveryModel('renault')); // outputs false 
```

如果您更喜欢命令式的做事方式，上面的代码可能会很好。另一方面，如果您不关心引擎盖下发生的事情，您可以重写上面的函数，并使用`Array.every`或`Array.find`来达到相同的结果。

```
const checkEveryModel = (model) => {
  return cars.every(car => car.model === model);
}

console.log(checkEveryModel('renault')); // outputs false 
```

通过使用`Array.find`，稍微调整一下，我们可以获得相同的结果，并且性能应该是相同的，因为两个函数都为数组中的每个元素执行回调，并且如果找到一个 *falsy* 项，就立即返回`false`。

```
const checkEveryModel = (model) => {
  return cars.find(car => car.model !== model) === undefined;
}

console.log(checkEveryModel('renault')); // outputs false 
```

### 4。使用`Array.some`匹配部分标准

就像`Array.every`对所有标准所做的那样，这个方法使得检查我们的数组是否包含一个或多个条目变得非常容易。为此，我们需要提供一个回调，并根据标准返回一个布尔值。

我们可以通过写一个类似于上面写的类似的`for...loop`语句来达到同样的结果，但是幸运的是我们有很酷的 JavaScript 函数为我们做事情。

```
const cars = [
  { model: 'renault', year: 1956 },
  { model: 'peugeot', year: 1968 },
  { model: 'ford', year: 1977 }
];

const checkForAnyModel = (model) => {
  return cars.some(car => car.model === model);
}

console.log(checkForAnyModel('renault')); // outputs true 
```

### 5。提前返回而不是分支

当我还是学生的时候，我被教导一个函数应该只有一个返回语句，并且应该只从一个位置返回。如果小心处理，这是一个不错的方法，这意味着我们应该认识到它会导致条件嵌套地狱的情况。如果失去控制，多个分支和`if...else`嵌套会是一件痛苦的事情。

另一方面，如果代码库很大，并且包含许多行，那么在深处的某个地方的 return 语句将是一个问题。现在我们实践关注点分离和坚实的原则，因此，大量的代码行应该是一种罕见的情况。

让我们创建一个例子来说明这一点，假设我们想要显示给定汽车的型号和制造年份。

```
const checkModel = (car) => {
  let result; // first, we need to define a result value

  // check if car exists
  if(car) {

    // check if car model exists
    if (car.model) {

      // check if car year exists
      if(car.year) {
        result = `Car model: ${car.model}; Manufacturing year: ${car.year};`;
      } else {
        result = 'No car year';
      }

    } else {
      result = 'No car model'
    }   

  } else {
    result = 'No car';
  }

  return result; // our single return statement
}

console.log(checkModel()); // outputs 'No car'
console.log(checkModel({ year: 1988 })); // outputs 'No car model'
console.log(checkModel({ model: 'ford' })); // outputs 'No car year'
console.log(checkModel({ model: 'ford', year: 1988 })); // outputs 'Car model: ford; Manufacturing year: 1988;' 
```

如你所见，即使对于我们这个简单的问题，上面的代码也很长。想象一下如果我们有更复杂的逻辑会发生什么。一大堆`if...else`语句。

我们可以在更多的步骤中重构上面的函数，使每一步都更好。例如，使用三元运算符，包含`&&`条件，等等，但是我将直接跳到最后，向您展示如何通过使用现代 JavaScript 特性和多个 return 语句来极大地简化。

```
const checkModel = ({model, year} = {}) => {
  if(!model && !year) return 'No car';
  if(!model) return 'No car model';
  if(!year) return 'No car year';

  // here we are free to do whatever we want with the model or year
  // we made sure that they exist
  // no more checks required

  // doSomething(model);
  // doSomethingElse(year);

  return `Car model: ${model}; Manufacturing year: ${year};`;
}

console.log(checkModel()); // outputs 'No car'
console.log(checkModel({ year: 1988 })); // outputs 'No car model'
console.log(checkModel({ model: 'ford' })); // outputs 'No car year'
console.log(checkModel({ model: 'ford', year: 1988 })); // outputs 'Car model: ford; Manufacturing year: 1988;' 
```

在重构版本中，我们包括了析构和默认参数。默认参数将确保如果我们通过`undefined`，我们有一个值要销毁。注意，如果我们传递一个`null`值，函数将抛出一个错误，这是前面方法的优点，因为在这种情况下，当传递`null`时，输出将是`'No car'`。

对象析构将确保函数只得到它需要的东西。例如，如果我们在给定的汽车对象中包含额外的属性，那么它在我们的函数中就不可用。

根据不同的偏好，开发人员将遵循这些路径之一。实践告诉我，通常，代码是在这两种方法之间的某个地方编写的。许多人认为`if...else`语句更容易理解，这有助于他们更容易地理解程序流程。

### ⑥。使用索引或地图代替`switch`语句

假设我们想要得到基于给定州的汽车模型。

```
const getCarsByState = (state) => {
  switch (state) {
    case 'usa':
      return ['Ford', 'Dodge'];
    case 'france':
      return ['Renault', 'Peugeot'];
    case 'italy':
      return ['Fiat'];
    default:
      return [];
  }
}

console.log(getCarsByState()); // outputs []
console.log(getCarsByState('usa')); // outputs ['Ford', 'Dodge']
console.log(getCarsByState('italy')); // outputs ['Fiat'] 
```

上面的代码可以被重构以完全排除`switch`语句。

```
const cars = new Map()
  .set('usa', ['Ford', 'Dodge'])
  .set('france', ['Renault', 'Peugeot'])
  .set('italy', ['Fiat']);

const getCarsByState = (state) => {
  return cars.get(state) || [];
}

console.log(getCarsByState()); // outputs []
console.log(getCarsByState('usa')); //outputs ['Ford', 'Dodge']
console.log(getCarsByState('italy')); // outputs ['Fiat'] 
```

或者，我们可以为每个州创建一个包含可用汽车列表的类，并在需要时使用它，但这是另一篇文章的主题。这篇文章是关于条件句的。更合适的变化是使用对象文字。

```
const carState = {
  usa: ['Ford', 'Dodge'],
  france: ['Renault', 'Peugeot'],
  italy: ['Fiat']
};

const getCarsByState = (state) => {
  return carState[state] || [];
}

console.log(getCarsByState()); // outputs []
console.log(getCarsByState('usa')); // outputs ['Ford', 'Dodge']
console.log(getCarsByState('france')); // outputs ['Renault', 'Peugeot'] 
```

### 7。使用可选的链接和无效合并

这一节我可以先说，*“终于”*。在我看来，这两个功能是对 JavaScript 语言非常有用的补充。作为一个来自 C#世界的人，我可以说我经常使用这些。

在撰写本文时，这些选项还没有得到完全支持，您需要使用 Babel 来编译以这种方式编写的代码。你可以在这里检查[可选链接，在这里](https://github.com/tc39/proposal-optional-chaining)检查[无效合并。](https://github.com/tc39/proposal-nullish-coalescing)

可选链接使我们能够处理树状结构，而无需显式检查中间节点是否存在，nullish 合并与可选链接结合使用效果很好，它用于确保不存在的节点的默认值。

让我们用一些例子来支持上面的陈述，并从老的做事方式开始。

```
const car = {
  model: 'Fiesta',
  manufacturer: {
    name: 'Ford',
    address: {
      street: 'Some Street Name',
      number: '5555',
      state: 'USA'
    }
  }
}

// to get the car model
const model = car && car.model || 'default model';
// to get the manufacturer street
const street = car && car.manufacturer && car.manufacturer.address && car.manufacturer.address.street || 'default street';
// request an un-existing property
const phoneNumber = car && car.manufacturer && car.manufacturer.address && car.manufacturer.phoneNumber;

console.log(model) // outputs 'Fiesta'
console.log(street) // outputs 'Some Street Name'
console.log(phoneNumber) // outputs undefined 
```

因此，如果我们想打印出汽车制造商是否来自美国，代码应该是这样的:

```
const checkCarManufacturerState = () => {
  if(car && car.manufacturer && car.manufacturer.address && car.manufacturer.address.state === 'USA') {
    console.log('Is from USA');
  }
}

checkCarManufacturerState() // outputs 'Is from USA' 
```

我不需要告诉你，在更复杂的对象结构的情况下，这会变得多么混乱。许多库，比如 lodash，都有自己的函数作为工作区，但是我们不希望这样，我们希望能够用普通的 js 来实现。让我们看看新的做事方法。

```
// to get the car model
const model = car?.model ?? 'default model';
// to get the manufacturer street
const street = car?.manufacturer?.address?.street ?? 'default street';

// to check if the car manufacturer is from the USA
const checkCarManufacturerState = () => {
  if(car?.manufacturer?.address?.state === 'USA') {
    console.log('Is from USA');
  }
} 
```

这个看起来更漂亮更短，对我来说，很合理。如果您想知道为什么应该使用`??`而不是`||`，只要想想哪些值可以评估为`true`或`false`，您就会有一个可能的意外输出。

还有一件事跑题了，非常简洁。可选链接也支持 DOM API，这非常酷，意味着你可以这样做:

```
const value = document.querySelector('input#user-name')?.value; 
```

## 结论

好了，这就是我现在要做的。如果你喜欢这篇文章，请在[devinduct.com](https://devinduct.com/blogpost/35/tips-and-tricks-for-better-javascript-conditionals-and-match-criteria)订阅(每篇文章末尾都有一个小表格:)，或者在[推特](https://twitter.com/protic_milos)上关注我，继续关注。

感谢您的阅读，下一篇文章再见。