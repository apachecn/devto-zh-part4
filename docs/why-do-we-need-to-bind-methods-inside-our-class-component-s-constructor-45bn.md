# 为什么我们需要在类组件的构造函数中绑定方法？

> 原文：<https://dev.to/aman_singh/why-do-we-need-to-bind-methods-inside-our-class-component-s-constructor-45bn>

如果我们回到[钩子](https://reactjs.org/docs/hooks-intro.html)还没有诞生的时代，我们在创建一个 React 类[组件](https://reactjs.org/docs/components-and-props.html#function-and-class-components)时，将不可避免地遇到这段代码。

```
class MyClassComponent extends React.Component {
  constructor(props) {
    super(props);

    // Assuming that you need to pass this as a prop to one of a child component
    this.someMethod = this.someMethod.bind(this); // But why? 🤔
  }
 ... 
} 
```

这是因为每当在一个类组件内部，当我们需要将一个函数作为道具传递给子组件时，我们必须执行以下操作之一:

*   将其绑定到构造函数中。
*   内联绑定它(这可能会有一些性能问题)。
*   使用 [*箭头函数*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) (与 [*属性初始化器*](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties) 语法相同)。

你有没有想过为什么会这样？为什么我们要做这项额外的工作？

通过这篇文章，我将首先尝试解释构造函数内部的绑定。一旦我们获得了这些知识，我们将试图回答为什么箭头函数不遵循同样的仪式。

我们需要知道的一点是，构造函数中的绑定与 React 无关。纯粹和 JavaScript 如何实现**这个**有关。让我们看看下面的代码:

```
var x = 10;
let foo = {
  x: 90,
  getX: function() {
    return this.x;
  }
};

foo.getX(); // prints 90

let xGetter = foo.getX;

xGetter(); // prints 10; 
```

当我们将 *x* 初始化为全局范围时，它就成为了*窗口*对象的属性(假设是浏览器环境而不是严格模式)。我们可以断言:

```
window.x === 10; // true 
```

**这个**将总是指向调用该方法的对象。所以，在 *foo.getX()* ，**这个**指向 *foo* 对象返回我们的值 90。而在 *xGetter()* ，**的情况下，这个**指向*窗口*对象，返回给我们值 10。

为了检索 *foo.x* 的值，我们可以通过使用 [Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 将 **this** 的值绑定到 *foo* 对象来创建一个新函数。

```
let getFooX = foo.getX.bind(foo);
getFooX(); // prints 90 
```

有了这些知识，让我们试着理解当您将一个函数 prop 传递给子组件时会发生什么。

在下面的代码示例中，我们创建了一个虚拟的*类*组件来模仿 React 组件心理模型。在*渲染*函数中，我们返回一个普通的 JS 对象，它有一个名为*‘onClick’*的功能属性。

> [React 元素](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html#elements-describe-the-tree)只是一个不可变的描述对象，带有
> 两个字段:type: (string | ReactClass)和 props: Object

```
class Component {
  constructor() {
    this.state = 10;
    this.setState = function() {
      console.log('state');
    };
  }

  handleClick() {
    this.setState();
  }

  render() {
    // return a child component.
    return {
      type: 'button',
      props: {
        // pass functional props
        onClick: this.handleClick,
        children: 'Click Me'
      }
    };
  }
}

// 1\. creating a component instance
const componentInstance = new Component();

// 2\. calling a render method on the instance
// ( In reality, React does the same thing for your class components)
const element = componentInstance.render();

// 3\. calling the onClick function, which was passed as a  prop,
// will throw a 'TypeError: this.setState is not a function'.
element.props.onClick(); 
```

这个 TypeError 现在很明显，因为它指向了不知道任何 setState 函数存在的 *props* 对象。 *setState* 函数只是 *componentInstance* 的一个属性。

所以，要解决这个问题，我们必须在构造函数中绑定 handleClick 函数:

```
// inside constructor function
constructor() {
  // bind returns a new function
  this.handleClick = this.handleClick.bind(this);
}

...

// calling onClick will print 'state' this time.
element.props.onClick(); 
```

现在，**这个**的值将始终指向 *componentInstance* ，该实例将 *setState* 作为其属性之一，并且不会抛出任何类型错误。

* * *

这就是我们第一个问题的答案。到目前为止进展不错。接下来，我们将尝试找出第二个问题的答案。

看下面的代码:

```
let bar = { someMethod: function() { return this; } };

bar.someMethod(); // print {someMethod: f}

let foo = { someMethod: () => this};

foo.someMethod(); // prints global 'window' object 
```

显示箭头函数本身没有这个。它总是由创建时 arrow 函数周围的作用域决定。

> 箭头函数没有自己的这个。

当我们在我们的类中使用一个*箭头*函数时(使用[属性初始化器](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties)特性)，它成为实例的方法属性。由于**这个**总是由外部作用域决定，它将指向类的*实例*。让我们看看实际情况:

```
class Component {
  constructor() {
    this.state = 10;
    this.setState = function() {
      console.log('state');
    };
  }

  // using fat arrow function; no binding require inside constructor
  handleClick = () => {
    // this will now point to the instance of Component class which knows about the setState method property
    this.setState();
  };

  render() {
    // return a child component.
    return {
      type: 'button',
      props: {
        // pass functional props
        onClick: this.handleClick,
        children: 'Click Me'
      }
    };
  }
}
// 1\. creating a component instance
const componenttInstance = new Component();

//  2\. calling a render method on the instance ( In reality, React does the same thing for your class components)

const element = componenttInstance.render();

// 3\. calling onClick will now print 'state' to the console.
element.props.onClick(); 
```

我希望你喜欢阅读这篇文章，现在能够自信地回答这个问题。我强烈推荐阅读这篇由[阿克塞尔·劳施迈尔](https://twitter.com/rauschma)博士撰写的[文章](http://2ality.com/2017/12/alternate-this.html)，以获得关于`this`如何工作的更详细的描述。

感谢您的阅读，如果您喜欢这篇文章，❤️一定会让我笑逐颜开😍。