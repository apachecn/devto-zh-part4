# Swift 和 JavaScript 比较片段-字符串和字符

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-the-basic-2ekl>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 字符串和字符

### 字符串文字

斯威夫特

```
let someString = "Some string literal value"

// Multiline String Literals with three double quotation marks (""")
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""

// String Mutability
var variableString = "Horse"
variableString += " and carriage"
// variableString is now "Horse and carriage"

let constantString = "Highlander"
constantString += " and another Highlander"
// this reports a compile-time error - a constant string cannot be modified 
```

JavaScript

```
const someString = "Some string literal value"

// Multiline String Literals back-tick (` `) 
const quotation = `
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
`

// String Mutability
let variableString = "Horse"
variableString += " and carriage"
// variableString is now "Horse and carriage"

const constantString = "Highlander"
constantString += " and another Highlander"
// this reports a compile-time error - a constant string cannot be modified 
```

### 字符串插值

斯威夫特

```
// insert values into the string literal is wrapped in a pair of parentheses, prefixed by a backslash (\)
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message is "3 times 2.5 is 7.5" 
```

JavaScript

```
const multiplier = 3
const message = `${multiplier} times 2.5 is ${multiplier * 2.5}`
// message is "3 times 2.5 is 7.5" 
```

### 访问和修改字符串

斯威夫特

```
// String Indices
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u

// Inserting and Removing
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome now equals "hello!"

welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there!"

welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"

let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello" 
```

JavaScript

```
const greeting = "Guten Tag!"
greeting.slice(0, 1)
// G
greeting.slice(-1)
// !
greeting.substr(1, 1)
// u

// Inserting and Removing
let welcome = "hello"
welcome += "!"
// welcome now equals "hello!"

welcome = welcome.substr(0, welcome.length - 1) + " there" + welcome.slice(-1)
// welcome now equals "hello there!"

welcome.substr(0, welcome.length - 1)
// welcome now equals "hello there"

let index = welcome.indexOf('  ');
welcome.slice(0, index)
// welcome now equals "hello" 
```