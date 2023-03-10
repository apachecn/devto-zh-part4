# 前端类比:闭包的咖啡—部分应用

> 原文：<https://dev.to/recss/front-end-analogies-closure-s-coffee-partial-application-155p>

### 点咖啡

#### 局部应用

乔有咖啡瘾。DSM-5 里没有，所以谁在乎？是时候获利了。

我们对这个笨蛋了解多少？他的名字叫乔。他总是得到两杯。一种是牛奶咖啡，但我们会用法语说——caféau lait——以收取更多费用。第二个，他喜欢混淆。什么都有可能。

```
 "use strict";

customerName = (_personName) => {
    return `${_personName} wants `;
}

buyTwoDrinks = (_firstCoffeeName) => {
    const secondDrink = (_secondCoffeeName) => {
        return `${_firstCoffeeName} and ${_secondCoffeeName}.`;
    }

    return secondDrink;
}

let Joe = customerName('Joe');
// What kinda clown's name is this?
// Prolly doesn't know his order yet cuz he spent too much time honkin' his nose.

let JoesUsual = buyTwoDrinks('Cafe au Lait');
// Usual baristas [functions] are like,
// "I want both these drink orders [parameters] right damn now."
// This bro is chill about it. We've got the first one on-deck,
// but we know we have to wait a little bit.

// When Joe comes in over the course of the week to order,
// it might look a little like this:

console.log( Joe + JoesUsual('Espresso') );
// Joe wants Cafe au Lait and Espresso.

console.log( Joe + JoesUsual('Nitro Cold Brew') );
// Joe wants Cafe au Lait and Nitro Cold Brew.

// This is a lot easier and less error-prone than writing:

console.log( customerName('Joe') + buyTwoDrinks('Cafe au lait')('Chai latte') );
// Joe wants Cafe au Lait and Chai latte.

console.log( customerName('Joe') + buyTwoDrinks('Cafe au lait')('Flat white') );
// Joe wants Cafe au Lait and Flat white.

// Gotta save time. You ever seen Joe without his caffeine for too long?
// It's less clown college and more Dark Knight Returns. 
```