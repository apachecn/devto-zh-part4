# Swift 和 JavaScript 比较片段-集合类型

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-collection-types-548p>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 收藏类型

### 集合的可变性:数组

斯威夫特

```
// creata an empty array
var someInts = [Int]()
print("someInts is of type [Int] with \(someInts.count) items.")
// Prints "someInts is of type [Int] with 0 items."

// Creating an Array with a Default Value
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]
var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
// anotherThreeDoubles is of type [Double], and equals [2.5, 2.5, 2.5]

var sixDoubles = threeDoubles + anotherThreeDoubles
// sixDoubles is inferred as [Double], and equals [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]

// Creating an Array with an Array Literal
var shoppingList: [String] = ["Eggs", "Milk"]
// shoppingList has been initialized with two initial items

// Accessing and Modifying an Array
if shoppingList.isEmpty {
    print("The shopping list is empty.")
} else {
    print("The shopping list is not empty.")
}
// Prints "The shopping list is not empty."
shoppingList.append("Flour")
// shoppingList now contains 3 items, and someone is making pancakes
shoppingList += ["Baking Powder"]
// shoppingList now contains 4 items
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// shoppingList now contains 7 items
var firstItem = shoppingList[0]

// firstItem is equal to "Eggs"
shoppingList[0] = "Six eggs"
// the first item in the list is now equal to "Six eggs" rather than "Eggs"

shoppingList[4...6] = ["Bananas", "Apples"]
// shoppingList now contains 6 items

shoppingList.insert("Maple Syrup", at: 0)
// shoppingList now contains 7 items
// "Maple Syrup" is now the first item in the list

let mapleSyrup = shoppingList.remove(at: 0)
// the item that was at index 0 has just been removed
// shoppingList now contains 6 items, and no Maple Syrup
// the mapleSyrup constant is now equal to the removed "Maple Syrup" string

let apples = shoppingList.removeLast()
// the last item in the array has just been removed
// shoppingList now contains 5 items, and no apples
// the apples constant is now equal to the removed "Apples" string

// Iterating Over an Array
for item in shoppingList {
    print(item)
}
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas

for (index, value) in shoppingList.enumerated() {
    print("Item \(index + 1): \(value)")
}
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas 
```

JavaScript

```
// creata an empty array
let someInts = new Array()
console.log(`someInts is an array with ${someInts.length} items.`)
// Prints "someInts is an array with 0 items."

// Creating an Array with a Default Value
let threeDoubles = [0.0, 0.0, 0.0]
// threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]
let anotherThreeDoubles = [2.5, 2.5, 2.5]
// anotherThreeDoubles is of type [Double], and equals [2.5, 2.5, 2.5]

let sixDoubles = [...threeDoubles, ...anotherThreeDoubles]
// sixDoubles is inferred as [Double], and equals [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]

// Creating an Array with an Array Literal
let shoppingList = ["Eggs", "Milk"]
// shoppingList has been initialized with two initial items

// Accessing and Modifying an Array
if (shoppingList.length === 0) {
    console.log("The shopping list is empty.")
} else {
    console.log("The shopping list is not empty.")
}
// Prints "The shopping list is not empty."
shoppingList.push("Flour")
// shoppingList now contains 3 items, and someone is making pancakes
shoppingList = shoppingList.concat(["Baking Powder"])
// shoppingList now contains 4 items
shoppingList = [...shoppingList, "Chocolate Spread", "Cheese", "Butter"]
// shoppingList now contains 7 items
let firstItem = shoppingList[0]
// firstItem is equal to "Eggs"
shoppingList[0] = "Six eggs"
// the first item in the list is now equal to "Six eggs" rather than "Eggs"

shoppingList.splice(4, 3, "Bananas", "Apples")
// shoppingList now contains 6 items

shoppingList.unshift("Maple Syrup")
// shoppingList now contains 7 items
// "Maple Syrup" is now the first item in the list

let mapleSyrup = shoppingList.shift()
// the item that was at index 0 has just been removed
// shoppingList now contains 6 items, and no Maple Syrup
// the mapleSyrup constant is now equal to the removed "Maple Syrup" string

let apples = shoppingList.pop()
// the last item in the array has just been removed
// shoppingList now contains 5 items, and no apples
// the apples constant is now equal to the removed "Apples" string

// Iterating Over an Array
shoppingList.forEach((item => {
    console.log(item)
})
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas

shoppingList.forEach(((item, index) => {
    console.log(`Item ${index + 1}: ${value}`)
})
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas 
```

### 集

斯威夫特

```
// Creating and Initializing an Empty Set
var letters = Set<Character>()
print("letters is of type Set<Character> with \(letters.count) items.")
// Prints "letters is of type Set<Character> with 0 items."
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
// favoriteGenres has been initialized with three initial items

// Accessing and Modifying a Set
print("I have \(favoriteGenres.count) favorite music genres.")
// Prints "I have 3 favorite music genres."

if favoriteGenres.isEmpty {
    print("As far as music goes, I'm not picky.")
} else {
    print("I have particular music preferences.")
}
// Prints "I have particular music preferences."

favoriteGenres.insert("Jazz")
// favoriteGenres now contains 4 items

if let removedGenre = favoriteGenres.remove("Rock") {
    print("\(removedGenre)? I'm over it.")
} else {
    print("I never much cared for that.")
}
// Prints "Rock? I'm over it."

if favoriteGenres.contains("Funk") {
    print("I get up on the good foot.")
} else {
    print("It's too funky in here.")
}
// Prints "It's too funky in here."

// Iterating Over a Set
for genre in favoriteGenres {
    print("\(genre)")
}
// Classical
// Jazz
// Hip hop 
```

JavaScript

```
// Creating and Initializing an Empty Set
let letters = new Set()
console.log(`letters is of type Set with ${letters.size} items.`)
// Prints "letters is of type Set with 0 items."
let favoriteGenres = new set(["Rock", "Classical", "Hip hop"])
// favoriteGenres has been initialized with three initial items

// Accessing and Modifying a Set
console.log(`I have ${favoriteGenres.size} favorite music genres.`)
// Prints "I have 3 favorite music genres."

if (favoriteGenres.size == 0) {
    console.log("As far as music goes, I'm not picky.")
} else {
    console.log("I have particular music preferences.")
}
// Prints "I have particular music preferences."

favoriteGenres.add("Jazz")
// favoriteGenres now contains 4 items
const removedGenre = favoriteGenres.delete("Rock")
if (removedGenre) {
    console.log(`${removedGenre}? I'm over it.`)
} else {
    console.log("I never much cared for that.")
}
// Prints "Rock? I'm over it."

if (favoriteGenres.has("Funk")) {
    console.log("I get up on the good foot.")
} else {
    console.log("It's too funky in here.")
}
// Prints "It's too funky in here."

// Iterating Over a Set
favoriteGenres.forEach(genre => {
    console.log(genre);
})
// Classical
// Jazz
// Hip hop 
```

### 字典

斯威夫特

```
// Creating an Empty Dictionary
var namesOfIntegers = [Int: String]()
// namesOfIntegers is an empty [Int: String] dictionary
namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = [:]
// namesOfIntegers is once again an empty dictionary of type [Int: String]

// Creating a Dictionary with a Dictionary Literal
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]

// Accessing and Modifying a Dictionary
print("The airports dictionary contains \(airports.count) items.")
// Prints "The airports dictionary contains 2 items."

if airports.isEmpty {
    print("The airports dictionary is empty.")
} else {
    print("The airports dictionary is not empty.")
}
// Prints "The airports dictionary is not empty."

airports["LHR"] = "London"
// the airports dictionary now contains 3 items

airports["LHR"] = "London Heathrow"
// the value for "LHR" has been changed to "London Heathrow"

if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
    print("The old value for DUB was \(oldValue).")
}
// Prints "The old value for DUB was Dublin."

if let airportName = airports["DUB"] {
    print("The name of the airport is \(airportName).")
} else {
    print("That airport is not in the airports dictionary.")
}
// Prints "The name of the airport is Dublin Airport."

airports["APL"] = "Apple International"
// "Apple International" is not the real airport for APL, so delete it
airports["APL"] = nil
// APL has now been removed from the dictionary

if let removedValue = airports.removeValue(forKey: "DUB") {
    print("The removed airport's name is \(removedValue).")
} else {
    print("The airports dictionary does not contain a value for DUB.")
}
// Prints "The removed airport's name is Dublin Airport."

// Iterating Over a Dictionary
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}
// YYZ: Toronto Pearson
// LHR: London Heathrow

for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}
// Airport code: YYZ
// Airport code: LHR

for airportName in airports.values {
    print("Airport name: \(airportName)")
}
// Airport name: Toronto Pearson
// Airport name: London Heathrow

let airportCodes = [String](airports.keys)
// airportCodes is ["YYZ", "LHR"]

let airportNames = [String](airports.values)
// airportNames is ["Toronto Pearson", "London Heathrow"] 
```

JavaScript

```
// Creating an Empty Dictionary
let namesOfIntegers = {}
// namesOfIntegers is an empty dictionary
namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = {}
// namesOfIntegers is once again an empty dictionary of type

// Creating a Dictionary with a Dictionary Literal
let airports = {"YYZ": "Toronto Pearson", "DUB": "Dublin"}

// Accessing and Modifying a Dictionary
console.log(`The airports dictionary contains ${Object.keys(airports).length} items.`)
// Prints "The airports dictionary contains 2 items."

if (Object.keys(airports).length == 0) {
    console.log("The airports dictionary is empty.")
} else {
    console.log("The airports dictionary is not empty.")
}
// Prints "The airports dictionary is not empty."

airports["LHR"] = "London"
// the airports dictionary now contains 3 items

airports["LHR"] = "London Heathrow"
// the value for "LHR" has been changed to "London Heathrow"

const oldValue = airports["DUB"]
airports["DUB"] = "Dublin Airport"
if (oldValue)  {
    console.log(`The old value for DUB was ${oldValue}.`)
}
// Prints "The old value for DUB was Dublin."
const airportName = airports["DUB"]
if (airportName) {
    console.log(`The name of the airport is ${airportName}.`)
} else {
    console.log("That airport is not in the airports dictionary.")
}
// Prints "The name of the airport is Dublin Airport."

airports["APL"] = "Apple International"
// "Apple International" is not the real airport for APL, so delete it
airports["APL"] = null
delete airports["APL"]
// APL has now been removed from the dictionary

// Iterating Over a Dictionary
Object.keys(airports).forEach(airportCode => {
    const airportName = airports[airportCode]
    console.log(`${airportCode}: ${airportName}`)
})
// YYZ: Toronto Pearson
// LHR: London Heathrow

let airportCodes = Object.keys(airports)
// airportCodes is ["YYZ", "LHR"] 
```