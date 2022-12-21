# 前端类比:闭包的咖啡——curry

> 原文：<https://dev.to/recss/front-end-analogies-closure-s-coffee-currying-5b6j>

### 烘焙咖啡

#### 逢迎

科纳正在和一台咖啡烘焙机较劲。它太旧了，所有的说明书都磨掉了。每次她放进一磅豆子:噗。烧焦了。

最后，管理层买了一个不那么糟糕的烘烤器。可能说这也是他们的主意。一次一磅？没问题，老板。如果你真的很好的话，它甚至会告诉你还剩多少。

```
 "use strict";

// I didn't tell you this, but:
// Currying is the same as unary, accepts one parameter…

const coffeeRoaster_Multiary = (_scoop1, _scoop2, _scoop3, _scoop4, _scoop5, _scoop6, _scoop7) => {
    console.log ("_scoop1, _scoop2, _scoop3, _scoop4, _scoop5, _scoop6, _scoop7");
    return (`Roastin' time.`);
}

let coffeeRoaster_Unary = _scoop1 => {
    console.log(`6 more pounds.`);

    return coffeeRoaster_Unary = _scoop2 => {
        console.log(`5 more pounds.`);

        return coffeeRoaster_Unary = _scoop3 => {
            console.log(`4 more pounds.`);

            return coffeeRoaster_Unary = _scoop4 => {
                console.log(`3 more pounds.`);

                return coffeeRoaster_Unary = _scoop5 => {
                    console.log(`2 more pounds.`);

                    return coffeeRoaster_Unary = _scoop6 => {
                        console.log(`1 more pound.`);

                        return coffeeRoaster_Unary = _scoop7 => {
                            console.log(`All ${_scoop7} pounds loaded.`);
                            return (`Roastin' time.`);
                        }
                    }
                }
            }
        }
    }
}

coffeeRoaster_Multiary("1");
// "1, undefined, undefined, undefined, undefined, undefined, undefined"

// That one was incinerated, like Starbucks…

coffeeRoaster_Multiary("1", "2", "3", "4", "5", "6", "7");
// "1, 2, 3, 4, 5, 6, 7"

// Finally works if you're able to put all 7 pounds in at once.
// That's one big scoop.

coffeeRoaster_Unary("1")("2");
// "6 more pounds"
// "5 more pounds"

coffeeRoaster_Unary("3")("4");
// "4 more pounds"
// "3 more pounds"

coffeeRoaster_Unary("5")("6")("7");
// "2 more pounds."
// "1 more pound."
// "All 7 pounds loaded."

// With this newfangled machine, you can do it at your leisure.
// We were able to put a man on the moon,
// but this is so hard? 
```