# 我的第一个白板挑战

> 原文：<https://dev.to/heymarkkop/my-first-whiteboard-challenge-c1d>

昨天，我有机会参加了一个工作面试过程，这个过程提出了一个需要在白板上解决的挑战。

这是我第一次在白板上编码的经历，起初看起来很简单，实际上有几个细节需要注意。

## 挑战

给定以下输入，预期输出应该是:

```
// Input
[
  {
    id: "futebol",
    parent: "null"
  },
  {
    id: "bola",
    parent: "futebol"
  }
]

// Output
{
  futebol: {
    bola: {}
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

作为一个数组输入，我立刻想到了使用 [map](https://www.w3schools.com/jsref/jsref_map.asp) 高阶函数，但是后来意识到输出应该是一个单独的对象，所以我把它改成了 [reduce](https://www.w3schools.com/jsref/jsref_reduce.asp) HoF。

当我弄清楚如何在**父**的值为空时创建一个新对象时，我已经陷入了 reduce HoF 给出的这种抽象中，这使我将策略改为一个更简单的 javascript 循环，用[处理](https://www.w3schools.com/js/js_loop_for.asp)。

完成代码后，面试官通过给出一个额外的输入对象`{ id: "chuteira", parent: "futebol"}`增加了另一个挑战，并询问代码是否仍能按预期工作。
那一刻我觉得不会，需要某种对象[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来不覆盖一个对象，但我们还是跟着做了。

## 解

我从这次经历中学到了一些很酷的 Javascript 技巧和概念，比如[动态属性键](https://www.samanthaming.com/tidbits/37-dynamic-property-name-with-es6) (ES6)和访问对象中的嵌套属性。我还练习了如何在白板上展示想法和逻辑以及交流。

下面是最终的代码和我最初打算使用的 ES6 方法。请注意，第一种方法更长，但更具声明性。

```
// Using Reduce, Spread Operator and Dinamic Property Keys
const reducedCategories = products.reduce((prevProduct, product) => {
  if (product["parent"] === "null") {
    return {
      ...prevProduct,
      [product["id"]]: {}
    };
  } else {
    return {
      ...prevProduct,
      [product["parent"]]: {
        ...prevProduct[product["parent"]],
        [product["id"]]: {}
      }
    };
  }
}, {});

// Using classic javascript
var classicCategories = {};
for (var i = 0; i < products.length; i++) {
  if (products[i]["parent"] === "null") {
    classicCategories[products[i]["id"]] = {};
  } else {
    var parent = products[i]["parent"];
    classicCategories[parent][products[i]["id"]] = {};
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你喜欢哪一个？