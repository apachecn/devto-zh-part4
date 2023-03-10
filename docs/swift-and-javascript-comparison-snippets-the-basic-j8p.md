# Swift 和 JavaScript 比较片段-基本

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-the-basic-j8p>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 基础知识

### 常量和变量

斯威夫特

```
// declare a constant 
let maximumNumberOfLoginAttempts = 10

// declare a variable
var currentLoginAttempt = 0

// declare multiple constants or multiple variables on a single line, separated by commas
var x = 0.0, y = 0.0, z = 0.0 
```

JavaScript

```
// declare a constant 
const maximumNumberOfLoginAttempts = 10

// declare a variable
var currentLoginAttempt = 0 
// or 
let currentLoginAttempt = 0

// declare multiple constants or multiple variables on a single line, separated by commas
var x = 0.0, y = 0.0, z = 0.0 
```

### 注释

斯威夫特

```
// This is a comment.

/* This is also a comment
but is written over multiple lines. */ 
```

JavaScript

```
// This is a comment.

/* This is also a comment
but is written over multiple lines. */ 
```

### 数值型转换

斯威夫特

```
let pi = 3.14159
// Integer and Floating-Point Conversion
let integerPi = Int(pi) 
```

JavaScript

```
const pi = 3.14159
// Integer and Floating-Point Conversion
const integerPi = parseInt(pi) 
```

### 布尔

斯威夫特

```
let orangesAreOrange = true
let turnipsAreDelicious = false

if turnipsAreDelicious {
    print("Mmm, tasty turnips!")
} else {
    print("Eww, turnips are horrible.")
} 
```

JavaScript

```
const orangesAreOrange = true
const turnipsAreDelicious = false

if (turnipsAreDelicious) {
    console.log("Mmm, tasty turnips!")
} else {
    console.log("Eww, turnips are horrible.")
} 
```

### 错误处理

斯威夫特

```
func canThrowAnError() throws {
    // this function may or may not throw an error
}
do {
    try canThrowAnError()
    // no error was thrown
} catch {
    // an error was thrown
} 
```

JavaScript

```
function canThrowAnError() {
    // this function may or may not throw an error
}
try {
    canThrowAnError()
    // no error was thrown
} catch (e) {
    // an error was thrown
} 
```

## 基本运算符

### 赋值运算符

斯威夫特

```
let b = 10
var a = 5
a = b

// decomposed tuple with multiple values into multiple variables
let (x, y) = (1, 2)
print("x = \(x), y = \(y)") // x = 1, y = 2 
```

JavaScript

```
let b = 10
var a = 5
a = b

// object matching with destructuring assignment
const {x, y} = {x:1, y:2}
console.log(`x = ${x}, y = ${y}`) // x = 1, y = 2
// or array matching with destructuring assignment
const [x, y] = [1, 2]
console.log(`x = ${x}, y = ${y}`) // x = 1, y = 2 
```

### 算术运算符

*   加法(+)
*   减法(-)
*   乘法(*)
*   除法(/)

斯威夫特

```
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4.0
"hello, " + "world"  // equals "hello, world" 
```

JavaScript

```
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4
"hello, " + "world"  // equals "hello, world" 
```

### 余数运算符

斯威夫特

```
9 % 4    // equals 1
-9 % 4   // equals -1 
```

JavaScript

```
9 % 4    // equals 1
-9 % 4   // equals -1 
```

### 一元减/加运算符

斯威夫特

```
let three = 3
let minusThree = -three       // minusThree equals -3
let plusThree = -minusThree   // plusThree equals 3, or "minus minus three"

let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix equals -6 
```

JavaScript

```
const three = 3
const minusThree = -three       // minusThree equals -3
const plusThree = -minusThree   // plusThree equals 3, or "minus minus three"

const minusSix = -6
const alsoMinusSix = +minusSix  // alsoMinusSix equals -6 
```

### 复合赋值运算符

斯威夫特

```
var a = 1
a += 2 // a is now equal to 3 
```

JavaScript

```
let a = 1
a += 2 // a is now equal to 3 
```

### 比较运算符

*   等于(a == b)
*   不等于(a！= b)
*   大于(a > b)
*   小于(a < b)
*   大于或等于(a >= b)
*   小于或等于(a <= b)
*   标识运算符，引用同一个对象实例(a === b)
*   标识运算符，而不是引用同一个对象实例(a！== b)

斯威夫特

```
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 is not equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 is not less than or equal to 1

let name = "world"
if name == "world" {
    print("hello, world")
} else {
    print("I'm sorry \(name), but I don't recognize you")
}
// Prints "hello, world", because name is indeed equal to "world".

let p1 = Person();
let p2 = Person();
p1 === p2 // false
p1 !== p2 // true 
```

JavaScript

```
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 is not equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 is not less than or equal to 1

const name = "world"
if (name == "world") {
    console.log("hello, world")
} else {
    console.log(`I'm sorry ${name}, but I don't recognize you`)
}
// Prints "hello, world", because name is indeed equal to "world".

const p1 = new Person();
const p2 = new Person();
p1 === p2 // false
p1 !== p2 // true 
```

### 三元条件运算符

斯威夫特

```
let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90 
```

JavaScript

```
const contentHeight = 40
const hasHeader = true
const rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90 
```

### 逻辑运算符

*   逻辑 NOT(！答
*   逻辑与(a 和 b)
*   逻辑或(a || b)

斯威夫特

```
let allowedEntry = false
if !allowedEntry {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"

let hasDoorKey = false
let knowsOverridePassword = true
let hasDoorKey = false
let knowsOverridePassword = true
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!" 
```

JavaScript

```
const allowedEntry = false
if (!allowedEntry) {
    console.log("ACCESS DENIED")
}
// Prints "ACCESS DENIED"

const hasDoorKey = false
const knowsOverridePassword = true
const hasDoorKey = false
const knowsOverridePassword = true
if (enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword) {
    console.log("Welcome!")
} else {
    console.log("ACCESS DENIED")
}
// Prints "Welcome!" 
```