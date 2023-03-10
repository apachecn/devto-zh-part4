# assembly script *不是* type script 的子集

> 原文：<https://dev.to/jtenner/assemblyscript-is-not-a-subset-of-typescript-4ka3>

AssemblyScript *肯定*不是 Web 程序集编译器的类型脚本。事实上，TypeScript 中使用的语法之间有很多差异，与 AssemblyScript 不匹配。

这篇博客文章将列出所有使 AssemblyScript 与众不同的顶级事物，我保证当我发现新的不同之处时会及时更新。

## 数字类型

JavaScript 只有两种数字类型`BigInt`和`number`。

AssemblyScript 可以区分许多数字类型，包括:

*   i8(整数！)
*   u8
*   i16
*   u16
*   i32
*   u32
*   i64
*   u64
*   f32(浮动！)
*   f64
*   isize(对于 wasm64，有符号指针编译为 i32 或 i64)
*   usize(对于 wasm64，无符号指针编译为 u32 或 u64)

目前，`i32`是支持`isize`和`usize`的数字类型。这是因为浏览器中的 2g 数据限制和 Web 程序集的 32 位运行时。在某个时候，这两种限制都将被取消。当使用指针时，如果你不得不使用`usize`或`isize`，因为后面的支持类型可能会不同。

## 操作员超载和顶级装饰者

```
class Vec3 {
  constructor(
    public x: f64 = 0.0,
    public y: f64 = 0.0,
    public z: f64 = 0.0,
  ) {}

  /**
   * This overloads the `==` operator. You can still use `===` for
   * comparing reference pointers exactly.
   */
  @operator("==")
  protected __equals(ref: Vec3): bool {
    return this.x == ref.x && this.y == ref.y && this.z == ref.z;
  }

  /**
   * This overloads the `+` operator.
   */
  @operatr("+")
  protected __add(ref: Vec3): Vec3 {
    return new Vec3(this.x + ref.x, this.y + ref.y, this.z + ref.z);
  }
} 
```

这在 AssemblyScript 中现在是有效的，但是在 JavaScript 环境中显然会失败。

```
// @ts-ignore: Add two vectors
let result = new Vec3(1, 2, 3) + new Vec3(4, 5, 6); 
```

TypeScript 不喜欢这个说法的原因是`Vec3`不是数字。从技术上讲，`Vec3.prototype.valueOf()`在 TypeScript 中有可能被覆盖，但是该函数的返回值将变成一个数字而不是一个引用。因此，运算符重载不被视为 AssemblyScript 的可移植特性。

开发人员创建内联函数也很常见。TypeScript 工具不喜欢以下语法。

```
// @ts-ignore: Top level decorators are supported in AssemblyScript
@inline
export function add(left: i32, right: i32): i32 {
  return left + right;
} 
```

这将导致 AssemblyScript 函数在执行时被内联编译*，尽管 TypeScript 不喜欢这样。*

 *## 非空断言

在 TypeScript 中使用后缀`!`运算符用于描述(在编译时)已知不为空的断言。因此，后置表达会导致一些真实的东西。

```
const result = myMap.get("key")!; 
```

在 AssemblyScript 中，使用这个后缀运算符实际上相当于:

```
let result = myMap.get("key");
assert(result, "value is null! Assertion fails!"); 
```

AssemblyScript 将 postscript `!`操作符转换为运行时非空断言。

## 指针和等式比较

通常，在 TypeScript 中，比较事物时使用`===`是很常见的，这是因为 JavaScript 的工作方式。在 AssemblyScript 中，默认的比较操作符应该是`==`，除非比较指针*正好是*。例如，这是有效的 JavaScript。

```
assert("CatDog" === "CatDog", "CatDog should be CatDog"); 
```

在 AssemblyScript 中，这个断言将失败，因为`===`操作符将进行指针比较，而不是像 JavaScript 那样进行字符串比较。测试字符串内容需要以下代码片段。

```
assert("CatDog" == "CatDog"); 
```

## Web 汇编内部函数

Web Assembly 使用的所有特殊函数都通过 AssemblyScript std 库公开。例如，使用`load<T>()`函数从堆分配中读取字节非常有用。

```
// strings are utf16 in AssemblyScript
let buffer = changeType<ArrayBuffer>(string.toUTF8());
let len = buffer.byteLength - 1; // null terminated...
for (let i = 0; i < len; i++) {
  // load the byte at ptr + i
  let char = <i32>load<u8>(changetype<usize>(buffer) + i);
} 
```

有关所有特殊内部函数的更多信息，您可以在这里查看文档。

## 声明关键字

通常在 TypeScript 中，使用`declare`关键字，开发人员可以有意义地定义一组环境模块和函数来描述软件运行环境的性质。在 AssemblyScript 中，这种命名空间将导致 Web 程序集导入，这些导入必须传递给模块实例化器。这样，AssemblyScript 并没有以“环境”的方式对待`declare`。

```
declare namespace externalNamespace {
  // only types allowed here are i32, f32 and f64
  function add(left: f64, right: f64): f64;
} 
```

这个声明的函数必须用 JavaScript 创建，如下所示:

```
import { instantiateStreaming } from "assemblyscript/lib/loader";

const wasm = await instantiateStreaming(fetch("../build/output.wasm"), {
  externalNamespace : { // put the namespace here
    add(left, right) { return left + right; } // put the function here
  }
}); 
```

## 没有任何类型

AssemblyScript 中不允许使用`any`(作为一个特性！)

## 未定义且无效的运算符

就像`any`一样，AssemblyScript 中没有`undefined`或`void`操作符，这些也应该避免。

## Null 实际上是 0

JavaScript 的有趣之处在于我们可以将`null`强制转换为`0`值。例如，开发人员使用`number`属性的一个常见缺陷是`null`很容易变成`0`。这是 AssemblyScript *和* TypeScript 中的一个传递断言。

```
assert(null == 0); // number coercion in JavaScript 
```

这里唯一的区别是数字*不能为空。例如，下面的例子会导致编译时错误。* 

```
function nullableNumber(value: i32 | null): void {
} 
```

目前还不支持多值返回，`i32`数字无法与`null`区分，因为`null`本身就是 0。除非处理引用类型，否则不要使用`null`。

## 树摇和条件编译

AssemblyScript 编译器在处理编译时常量时可以执行分支内联。比如说。如果`T`是一个`Array`，接下来的 if 分支将被内联，`return`之后的任何内容都将被编译器忽略。

```
function length<T>(value: T): i32 {
  if (isArray<T>()) {
    return value.length;
  }
  return 0;
} 
```

具体来说，当`T`为 array 时，`if(true)`分支将被内联，`value.length`之后的任何内容都将被忽略。

```
function length<Array<i32>>(value: Array<i32>): i32 {
  return value.length;
} 
```

此外，来自不同模块的未使用的导入将被编译器完全忽略，因为编译器是为在编译时执行“树摇动”而构建的。

## 数组排序差异

当处理数字数组时，调用`Array#sort`将按数字而不是字母顺序对数字进行排序(通过 toString()强制)。)

在 TypeScript 中，以下是正确的。

```
[140000, 104, 99].sort();
(3) [104, 140000, 99] 
```

在 AssemblyScript 中，这是预期的输出。

```
[140000, 104, 99].sort()
(3) [99, 104, 140000] 
```

在 AssemblyScript 中，排序算法避免使用`.toString()`进行数字强制，并产生一个正确排序的数组。

## 工会类型

在 AssemblyScript 中，不支持联合类型。最好简单地使用`instanceof`运行时检查。

```
function log<T extends string | ArrayBuffer>(value: T): void {

} 
```

由于`string | ArrayBuffer`联合，这将导致编译时错误。

```
function log<T>(value: T): void {
  if (isReference<T>()) {
    // useful for finding strings
    if (value instanceof string) logString(value);
  } else {
    // definitely a number type!
  }
} 
```

并非所有的`instanceof`检查都会导致运行时比较，而是会导致编译时检查。

## 我漏了一个吗？

请在下面评论！我很高兴更新这篇文章，并把它作为每个想学习 AssemblyScript 的人的范例。

干杯，
[@jtenner](https://dev.to/jtenner)*