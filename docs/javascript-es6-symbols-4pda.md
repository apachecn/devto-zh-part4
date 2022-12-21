# Javascript - ES6。符号。

> 原文：<https://dev.to/victordeandres/javascript-es6-symbols-4pda>

## Javascript - ES6。符号。

es 6–ecmascript 2015 的新增功能之一是添加了一种新的数据类型 symbol。诚然，自 2015 年 6 月在那里推出这一新的 javascript 标准以来已有相当长的时间了，但与我们通常使用的 es-6 的其他功能一样。例如，箭头函数、模板字符串或使用 let 和 const 来定义变量，仅举几个例子，symbol 的用法即使不存在，也是残馀的。

## Qué es 符号？

我们首先要知道符号是什么。Symbol 是包含在原始类型(即不可更改的数据类型)中的新 JavaScript 数据类型。请记住，在 javascript 中，不可变类型是:boolean、null、undefined、number、string，当然还有 symbol，这是我们刚刚添加到类型列表中的类型。

这种新型的另一个特点是，除了一成不变之外，它始终是一种独特的类型。

定义新的 symbol 类型值时，我们可以关联 string 或 undefined 类型的值，这些值仅用于说明。

```
const variable = Symbol(‘test’); 
```

Symbol()函数不是构造函数，因此使用以下语法创建新的 symbol 类型变量会导致错误。

```
const variable = new Symbol(‘test’);  // output: TypeErrpor 
```

## 一个简单的例子。

如果定义了两个具有相同值的变量并对其进行比较，则结果为 true。

```
const valor_1 = ‘test’
const valor_2 = ‘test’
console.log(valor_1 === valor_2 );          // output: true 
```

相反，如果将变量设置为 symbol 类型，则结果为 false。

```
const valor_1s = Symbol(‘test’)
const valor_2s = Symbol(‘test’)
console.log(valor_1s === valor_2s);     // output: false 
```

请记住，我之前说过，可以将 string 类型与 symbol 类型相关联作为说明，在本示例中，∞是两个变量的说明。

如果我们通过控制台显示我们存储在 value _ 1s 变量中的值，我们将得到如下结果:

```
console.log(valor_1s);                  // output: Symbol(test) 
```

## 符号如何帮助我们。

我们已经描述了新的符号数据类型及其特征。以下是 symbol 可以帮助我们的一些情况。

## 避免重复。

由于 symbol 类型变量是唯一且不可更改的，因此扩展对象和类的功能可以帮助我们避免重复。

让我们举个例子。假设我们有以下对象。

```
const vehiculo = {
                motor: ‘combustion’,
                cv: 150,
                puertas: 3
} 
```

如果我们想给我们的车辆增加一个新的电动马达，使它混合起来。

```
vehiculo.motor = ‘electrico’; 
```

当我们去阅读我们的汽车的属性时。我们认为这辆车是电动的不是混合动力的。

```
console.log(vehiculo);
// output: { motor: ‘electrico’, 
//           cv: ‘150’,
//           puertas: 3 } 
```

但是，如果我们从原始对象开始创建一个带有 motor id 的新 symbol 类型属性，并将其添加到我们的车辆对象中。

```
const motor = Symbol(‘motor’);
vehiculo[motor] = ‘electrico’; 
```

查看对象属性时，我们将得到以下答案。

```
Console.log(vehiculo);
// output: { 
//            motor: ‘combustion’,
//            cv: ‘150’,
//            puertas: 3,
//            Symbol(motor): ‘electrico’ } 
```

## 模拟私有财产/方法。

符号的另一个特性是它们不可枚举，因此当我们循环、for ... in 或使用 object . getownpropertynames()方法时，将无法访问它们。

如果我们循环查看我们对象的属性，我们得到的结果如下:

```
for ( características in vehiculo ) {
    console.log(`${caracteristicas }: ${ vehículo[caracteristicas]} `);
}
// output
// motor: combustion
// cv: 150
// puertas: 3 
```

我们只能看到它的特性，引擎，cv 和门。我们看不到 Symbol(motor)属性的值。

从以上结果来看，由于它是不可枚举的，因此您可能认为可以在对象或类中创建私有属性或方法。但事实并非如此。

我们可以将这些属性或方法定义为半私有，因为如果我们可以直接访问它们。

```
console.log(vehiculo[motor]); // output: electrico 
```

或者，如果使用 object . getownpropertysymbols()方法，我们可以列出对象中的所有 symbol 类型属性

```
console.log(Object.getOwnPropertySymbols(vehiculo)); // [Symbol(motor)] 
```

此外，如果要获取我们类中对象或方法的所有属性，而不管它们是否为 symbol 类型，而不同时连接 object . getownpropertynames()和 object . getownpropertysymbols()。我们可以使用 Reflect 对象的 ownkeys 方法，该方法将返回包含所有值的数组。

```
console.log(Reflect.ownKeys(vehiculo)); 
// output: [motor, cv, puertas, Symbol(‘motor’)] 
```

## 结论。

此 Javascript 功能的使用可能尚未广泛使用，因为您可以使用此类数据的场合非常具体。但是，我理解，了解这类数据的特点和用途对于更深入地了解这种语言非常重要，并充分利用它为我们提供的所有功能，使代码更好、更易读。