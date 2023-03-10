# ES6 析构对象和数组

> 原文：<https://dev.to/wllfaria_/es6-destructuring-objects-and-arrays-dc>

ES6 引入了一个很棒的特性，那就是析构。例如，当您使用 react 时，这非常有用，但是它的使用案例几乎是无止境的。

你可以进行数组和对象的析构，甚至可以析构数组中的对象，反之亦然。让我们从简单的开始，因为我发现自己使用对象析构的次数最多，我要先解释一下，它实际上非常简单。

* * *

## **物体被破坏**

例如，假设您有一个包含名字、姓氏、年龄和爱好的 person 对象，如下所示:

```
const person = {
  firstName: 'John',
  lastName: 'Moore',
  age: 32,
  hobbies: [
    'coding',
    'drawing',
    'fortnite'
  ]
} 
```

假设你想在你的应用程序上显示名字+姓氏和问候信息，你可以这样做:

```
let greeting = `Hello ${person.firstName}  ${person.lastName}, welcome back!` 
```

但是有了对象析构，你可以用一种实际上不需要做那种`object.property`语法的方式来实现它，一旦你深入到析构的更深层次，你可能会发现这很有用。要实现它，代码应该是这样的:

```
const { firstName, lastName } = person
let greeting = `Hello ${firstName}  ${lastName}, welcome back!` 
```

您将想要析构的属性的确切名称放在花括号内，然后告诉 javascript 这些变量来自 person 对象！

## 很好读吧？但是你可能会问自己，我在哪里使用它？

如果你是像我一样的 react 开发者，你可以用它来访问函数/类组件的状态属性或属性，甚至可以用钩子！下面的代码摘自我在 GitHub 上的一个项目，只是为了展示我是如何使用它的。

```
export default class IntroSection extends React.Component {
  state = {
    animateIntro: false,
  };

  render() {
    const { animateIntro } = this.state;
    const { lightMode } = this.props;
    return (
      //...some code here...
    )
  }
} 
```

但是让我们继续，因为对象不是你唯一能做的。

## **破阵**

你可以用同样的方法析构对象，也可以对数组进行析构，但是开始会有点混乱。
现在让我们假设你有一个行星阵列，如下所示:

```
const planets = ["Mercur", "Venus", "Earth", "Mars"] 
```

如果你想用一种方式来破坏它，你可以把每个行星标为行星 1-4，你可以这样做:

```
const [ planet1, planet2, planet3, planet4 ] = planets 
```

如你所见，左边的每个变量都相当于数组的一个索引，这很有趣，但也给了我们一些问题。

如果变量的数量大于数组中元素的数量呢？如果我有嵌套数组呢？如果我不知道数组的确切大小，该怎么办？

## 别担心，我现在就回答这些问题。

让我们从第一个开始，如果传递的变量多于数组中的实际项数，那么输出将返回 undefined。就这么简单。

```
const [ planet1, planet2, planet3, planet4, planet5 ] = planets
// output would be: "Mercur", "Venus", "Earth", "Mars", undefined 
```

## **嵌套数组**

你也可以在嵌套数组上使用析构，所以假设你现在有一个数字数组。

```
const numbers = [5, [1, 2, 3], 6, 7] 
```

你想得到 5，1 和 6 作为输出。在这种情况下，您的代码将如下所示:

```
let [ a, [ b ], c ] = numbers
// Output: 5, 1, 6 
```

实际上，你可以想走多深就走多深，不需要担心这个。这就给我们留下了最后一个问题。

## **通过数组映射**

假设你不知道数组中到底有多少项，但是你想把它们都析构掉。你可以用一个简单的方法，使用 Spread 操作符。

```
let [ planet1, planet2, ...others ] = planets 
```

> (…)是扩展操作符语法，这是我即将展示的 ES6 的另一个特性。

希望你喜欢阅读本指南。

你可以在 twitter 上找到我: [@wllfaria_](https://twitter.com/wllfaria_) 我在那里发布每周的编码技巧。

我很想和你谈谈。