# Swift 和 JavaScript 比较片段-控制流

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-control-flow-c6a>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 控制流程

### For-In 循环

斯威夫特

```
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!

let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}
// ants have 6 legs
// cats have 4 legs
// spiders have 8 legs 
```

JavaScript

```
const names = ["Anna", "Alex", "Brian", "Jack"]
for (let i = 0; i < names.length; i++) {
    console.log(`Hello, ${names[i]}!`)
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!

const numberOfLegs = {"spider": 8, "ant": 6, "cat": 4}
Object.keys(numberOfLegs).forEach(animalName => {
    const legCount = numberOfLegs[animalName];
    console.log(`${animalName}s have ${legCount} legs`)
})
// ants have 6 legs
// cats have 4 legs
// spiders have 8 legs 
```

### While Loops

斯威夫特

```
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
while square < finalSquare {
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
    if square < board.count {
        // if we're still on the board, move up or down for a snake or a ladder
        square += board[square]
    }
}
print("Game over!") 
```

JavaScript

```
let finalSquare = 25
let board = []
for (let i = 0; i <= finalSquare; i++) {
    board[i] = 0;
}
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
let square = 0
let diceRoll = 0
while (square < finalSquare) {
    // roll the dice
    diceRoll += 1
    if (diceRoll == 7) { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
    if (square < board.length) {
        // if we're still on the board, move up or down for a snake or a ladder
        square += board[square]
    }
}
console.log("Game over!") 
```

### 条件语句

斯威夫特

```
var temperatureInFahrenheit = 30
temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's really warm. Don't forget to wear sunscreen." 
```

JavaScript

```
let temperatureInFahrenheit = 30
temperatureInFahrenheit = 90
if (temperatureInFahrenheit <= 32) {
    console.log("It's very cold. Consider wearing a scarf.")
} else if (temperatureInFahrenheit >= 86) {
    console.log("It's really warm. Don't forget to wear sunscreen.")
} else {
    console.log("It's not that cold. Wear a t-shirt.")
}
// Prints "It's really warm. Don't forget to wear sunscreen." 
```

### 开关

斯威夫特

```
let someCharacter: Character = "z"
switch someCharacter {
case "a":
    print("The first letter of the alphabet")
case "z":
    print("The last letter of the alphabet")
default:
    print("Some other character")
}
// Prints "The last letter of the alphabet" 
```

JavaScript

```
const someCharacter = "z"
switch (someCharacter) {
case "a":
    console.log("The first letter of the alphabet")
    break;
case "z":
    console.log("The last letter of the alphabet")
    break;
default:
    console.log("Some other character")
}
// Prints "The last letter of the alphabet" 
```