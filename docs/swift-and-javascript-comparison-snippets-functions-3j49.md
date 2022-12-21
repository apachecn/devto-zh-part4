# Swift 和 JavaScript 比较片段-功能

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-functions-3j49>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 功能

斯威夫特

```
// Defining and Calling Functions
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
print(greet(person: "Anna"))
// Prints "Hello, Anna!"
print(greet(person: "Brian"))
// Prints "Hello, Brian!"

func greetAgain(person: String) -> String {
    return "Hello again, " + person + "!"
}
print(greetAgain(person: "Anna"))
// Prints "Hello again, Anna!"

// Function Parameters and Return Values
// Functions Without Parameters
func sayHelloWorld() -> String {
    return "hello, world"
}
print(sayHelloWorld())
// Prints "hello, world"

// Functions With Multiple Parameters
func greet(person: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return greetAgain(person: person)
    } else {
        return greet(person: person)
    }
}
print(greet(person: "Tim", alreadyGreeted: true))
// Prints "Hello again, Tim!"

// Functions Without Return Values
func greet(person: String) {
    print("Hello, \(person)!")
}
greet(person: "Dave")
// Prints "Hello, Dave!"

// Functions with Multiple Return Values
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
// Prints "min is -6 and max is 109"

// Nested Functions
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero! 
```

JavaScript

```
// Defining and Calling Functions
function greet(person) {
    const greeting = "Hello, " + person + "!"
    return greeting
}
console.log(greet("Anna"))
// Prints "Hello, Anna!"
console.log(greet("Brian"))
// Prints "Hello, Brian!"

function greetAgain(person) {
    return "Hello again, " + person + "!"
}
print(greetAgain("Anna"))
// Prints "Hello again, Anna!"

// Function Parameters and Return Values
// Functions Without Parameters
function sayHelloWorld() {
    return "hello, world"
}
console.log(sayHelloWorld())
// Prints "hello, world"

// Functions With Multiple Parameters
function greet(person, alreadyGreeted) {
    if (alreadyGreeted) {
        return greetAgain(person)
    } else {
        return greet(person)
    }
}
console.log(greet("Tim", true))
// Prints "Hello again, Tim!"

// Functions Without Return Values
function greet(person) {
    console.log(`Hello, ${person}!`)
}
greet("Dave")
// Prints "Hello, Dave!"

// Functions with Multiple Return Values
function minMax(array) {
    let currentMin = array[0]
    let currentMax = array[0]
    array.forEach(value => {
        if (value < currentMin) {
            currentMin = value
        } else if (value > currentMax) {
            currentMax = value
        }
    })
    return {currentMin, currentMax}
}
const bounds = minMax([8, -6, 2, 109, 3, 71])
console.log(`min is ${bounds.min} and max is ${bounds.max}`)
// Prints "min is -6 and max is 109"

// Nested Functions
function chooseStepFunction(backward) {
    function stepForward(input) { return input + 1 }
    function stepBackward(input) { return input - 1 }
    return backward ? stepBackward : stepForward
}
let currentValue = -4
const moveNearerToZero = chooseStepFunction(currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while (currentValue != 0) {
    console.log(`${currentValue}... `)
    currentValue = moveNearerToZero(currentValue)
}
console.log("zero!")
// -4...
// -3...
// -2...
// -1...
// zero! 
```