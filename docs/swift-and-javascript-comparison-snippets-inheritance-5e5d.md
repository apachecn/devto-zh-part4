# Swift 和 JavaScript 比较片段-继承

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-inheritance-5e5d>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 继承

斯威夫特

```
// Defining a Base Class
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
let someVehicle = Vehicle()
print("Vehicle: \(someVehicle.description)")
// Vehicle: traveling at 0.0 miles per hour

// Subclassing
class SomeSubclass: SomeSuperclass {
    // subclass definition goes here
}
class Bicycle: Vehicle {
    var hasBasket = false
}
let bicycle = Bicycle()
bicycle.hasBasket = true

bicycle.currentSpeed = 15.0
print("Bicycle: \(bicycle.description)")
// Bicycle: traveling at 15.0 miles per hour

// Subclasses can themselves be subclassed
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour

// Overriding
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
let train = Train()
train.makeNoise()
// Prints "Choo Choo"

// Overriding Property Getters and Setters
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Car: traveling at 25.0 miles per hour in gear 3 
```

JavaScript

```
// Defining a Base Class
class Vehicle {
    constructor() {
        this.currentSpeed = 0.0
    }
    get description() {
        return `traveling at ${currentSpeed} miles per hour`
    }
    function makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
let someVehicle = Vehicle()
console.log(`Vehicle: ${someVehicle.description}`)
// Vehicle: traveling at 0.0 miles per hour

// Subclassing
class SomeSubclass extends SomeSuperclass {
    contructor() {
        super();
    }
    // subclass definition goes here
}
class Bicycle extends Vehicle {
    contructor() {
        super();
        this.hasBasket = false
    }
}
let bicycle = Bicycle()
bicycle.hasBasket = true

bicycle.currentSpeed = 15.0
console.log(`Bicycle: ${bicycle.description}`)
// Bicycle: traveling at 15.0 miles per hour

// Subclasses can themselves be subclassed
class Tandem extends Bicycle {
    contructor() {
        super();
        this.currentNumberOfPassengers = 0
    }
}
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
console.log("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour

// Overriding
class Train extends Vehicle {
    contructor() {
        super();
        this.currentNumberOfPassengers = 0
    }
    function makeNoise() {
        console.log("Choo Choo")
    }
}
let train = Train()
train.makeNoise()
// Prints "Choo Choo"

// Overriding Property Getters and Setters
class Car extends Vehicle {
    contructor() {
        super();
        this.gear = 1
    }
    get description() {
        return `${super.description} in gear ${gear}`
    }
}
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
console.log(`Car: ${car.description}`)
// Car: traveling at 25.0 miles per hour in gear 3 
```