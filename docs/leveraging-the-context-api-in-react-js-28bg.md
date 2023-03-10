# 如何使用 React.js 中的上下文 API 来发布订阅

> 原文：<https://dev.to/itnext/leveraging-the-context-api-in-react-js-28bg>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> React 上下文的存在使您不必在每一级手工传递数据。上下文是关于向许多组件共享数据的。所以本质上它是关于发布和订阅或简称为 Pubsub。

在本文中，我们将了解以下主题:

*   **上下文 API** ，它是什么，为什么使用它
*   **构建模块**，我们将使用上下文来了解不同的构建模块以及它们如何协同工作
*   **在动作**中，我们将看几个创建上下文的例子，我们将确保了解动态上下文，以及更高阶的组件如何帮助清理我们的上下文代码

如果您曾经对本文中的代码示例感到困惑，请查看包含以下所有代码的演示:

[https://github.com/softchris/react-context-demo](https://github.com/softchris/react-context-demo)

### 为什么说上下文 API，它是什么？

React 上下文的存在使您不必在每一级手工传递数据。上下文是关于向许多组件共享数据的。我们需要上下文 API 的原因是，如果有许多组件需要相同的数据，那么通过 props 将数据从父组件传递到子组件会很麻烦。通过使用上下文 API，我们不再用 props 传递这种共享数据。

#### 何时使用/不使用

属于上下文的东西是被认为是全局的数据，比如一个*用户*或者一个*购物车*等等。因此，让我们试着列出使用上下文的不同原因:

*   **许多地方需要的数据**，需要被许多组件使用的数据，如主题、用户或购物车
*   **通过许多组件传递道具**，有时当你想通过许多组件传递一个`props`值时，最好使用组合而不是上下文

#### 构建模块和 API

上下文 API 由一些构造块组成，重要的是我们要知道它们的名称以及它们的作用:

*   **context** ，context 对象是保存当前上下文值的对象，可以订阅
*   **provider** ，这是一个提供相关值的 React 组件，它从上下文对象中获取该值
*   **消费者**，这是一个 React 组件，它能够消费`Provider`提供的值，并且能够显示该值

这都是理论上的，听起来可能有点混乱，所以让我们深入一个例子来消除任何混乱。

### 动作中的上下文 API

要在 React 中利用上下文，我们需要做以下工作:

*   **创建一个上下文**，我们通过调用 React.createContext()来实现，这将返回一个上下文对象，该对象公开一个`Provider Component`以及一个`Consumer Component`
*   **声明一个提供者**，这是我们获取刚刚创建的上下文对象中可用的`Provider`组件引用
*   **声明一个消费者**，这也是一个依赖于上下文对象的组件，我们用它来向用户显示值

#### 创建上下文对象

让我们从使用 Create React App 创建一个 React 项目开始，CRA:

```
npx create-react-app context-demo
cd context-demo
npm start 
```

很好，我们有一个项目。现在让我们创建一个名为`theme.js`的文件，它将保存我们的`Context`对象。

创建上下文对象非常简单。为此，我们像这样使用`React.createContext()`方法:

```
// theme.js

import React from 'react';

const ThemeContext = React.createContext('light');

export default ThemeContext; 
```

上面我们调用了`createContext()`,并给了它一个输入参数，这个参数就是我们希望上下文拥有的默认值。我们还导出对象本身，这样我们就可以在其他地方使用它。

要使用像`Context`这样强大的东西，只需编写很少的代码。我们还没有看到任何东西，所以好戏才刚刚开始:

#### 声明提供者

好了，我们有了一个`Context`对象，接下来让我们获取一个对`Provider`的引用。为此，我们将首先创建一个组件文件`Sample.js`，你可以随便叫它什么，但关键是要有一个 React 组件来演示`Context`对象是如何工作的。让我们创建一个组件:

```
// Sample.js

import React from 'react';

import Theme from './theme';

const Sample = () => (

<Theme.Provider value='dark'>

// declare consumer

</Theme.Provider>

);

export default Sample; 
```

上面我们声明了一个普通的函数式 React 组件，我们还导入了我们的`Theme`，我们的`Context`对象。然后，我们通过调用`Theme.Provider`来获取对我们的提供者的引用。在这一点上，没有什么是真正有效的，因为我们缺少一个`Consumer`组件，它可以实际消费这个值，从而向用户显示它。

此外，我们还将 value 属性设置为`dark`。

等等，等等…我们不是刚刚在`theme.js`文件中设置了这个值吗，如果我们无论如何都要在`Provider`中覆盖它，那么做有什么意义呢？非常好的问题，让我们把它保留一点，直到我们声明了一个消费者，然后它就都有意义了。

#### 申报消费

所以接下来是声明一个`Consumer`组件，并展示我们如何向用户显示值。让我们把它添加到我们的代码中:

```
// Sample.js

import React from 'react';

import Theme from './theme';

const Sample = () => (
<Theme.Provider value='dark'>
  <Theme.Consumer>  
  {theme => <div>Our theme is: {theme}</div>}   
  </Theme.Consumer>  
</Theme.Provider>
);

export default Sample; 
```

上面我们添加了我们的`Consumer`，以`Theme.Consumer`组件的形式，我们可以看到我们在里面定义了一个函数，它的参数是我们的主题值。然后我们能够在一个 div 中显示`theme`值。

好了，让我们回到我们的问题，如果我们已经在`theme.js`文件中设置了一个*默认值*，为什么还要在`Theme.Provider`组件中设置 value 属性，这里:

```
// theme.js

import React from 'react';

const ThemeContext = React.createContext('light');

export default ThemeContext; 
```

嗯，如果我们声明一个`Provider`，上面的默认值就不会被使用。然而，如果我们缺少一个`Provider`组件，它将使用*默认值*作为后备。所以下面的代码将输出`dark`作为值，这是我们给`Provider` :
的值

```
const Sample = () => (
 <Theme.Provider value='dark'>
   <Theme.Consumer>
   {theme => <div>Theme value: {theme}</div>}
   </Theme.Consumer>
 </Theme.Provider>
) 
```

而这段代码将输出`light`作为值，例如*默认值* :

```
// Sample.js with a Provider missing

const Sample = () => (
 <Theme.Consumer>
 {theme => <div>Theme value: {theme}</div>}
 </Theme.Consumer>
); 
```

#### 用法

让我们的`Context`旋转意味着我们需要创建一个`Provider`和一个`Consumer`，就像我们在上一节所做的那样，然而，最有可能的是`Consumer`部分被烘焙成一个组件，就像这样:

```
// ThemedButton.js
import Theme from 'theme.js';

const ThemedButton = (props) => (

  <Theme.Consumer>

  {theme => <button { ...props }>button with them: {theme}</button>}

</Theme.Consumer>

);
export default ThemedButton 
```

这意味着上一节中的代码可以稍微整理一下，如下所示:

```
// Sample.js

import React from 'react';

import Theme from './theme';
import ThemedButton from './ThemedButton';

const Sample = () => (

<Theme.Provider value='dark'>
  <ThemedButton />
</Theme.Provider>
);

export default Sample; 
```

如你所见，`Provider`的值通过`props`传递，我们可以在`ThemedButton`组件内部通过`Consumer`访问`theme`属性。

### 动态语境

如果我们想改变提供者的价值呢？这样做的一个方法是通过拥有一个动态上下文。我们可以通过将我们的`Provider`放在一个组件中，并让它的值依赖于组件的状态，如下所示:

```
// AnyComponent.js
 import React from 'react';

class AnyComponent extends React.Component {
  state = {  
    theme: 'dark'  
  };

 render() {
   return (
   <ThemeContext.Provider value={ this.state.theme }>
     <ThemedButton />
   </ThemeContext.Provider>
   );
 }
} 
```

现在我们很容易改变状态，因此我们可以改变`Provider`提供给任何`Consumer`的值。

#### 改变状态示例

下面我们正在创建一个组件，它包含一个具有两个不同值`light`和`dark`的下拉列表，当我们在值之间切换时，状态会改变，因为状态与`Provider`相关，所以提供的值也会随之改变。

让我们来看看代码:

```
// AnyComponent.js

import React from 'react';
import Theme from './theme';
import ThemedButton from './ThemedButton';

class AnyComponent extends React.Component {
  state = {  
    theme: 'dark',  
    themes: ['light', 'dark']  
  };

  handleSelect = (evt) => {
    console.log('Changing value to ' + evt.target.value);

    this.setState({  
      theme: evt.target.value  
    });  
  };

  render() {
    return (
     <React.Fragment>
      <h2>Any component</h2>
      <select value = {this.state.theme} 
       onChange ={this.handleSelect}> 
      { this.state.themes.map(t => 
      <option value = {t} >{t}</option>)
      }
      </select>
      <div>
      Selected theme: {this.state.theme} 
      </div>
      <Theme.Provider value ={this.state.theme}>
        <ThemedButton theme={this.state.theme} />
      </Theme.Provider>
      </React.Fragment>
    );

  }
}

export default AnyComponent; 
```

从上面的代码中我们可以看到，当`onChange`事件被触发时，我们调用了`handleSelect()`方法，这导致状态属性`theme`被更新。同样的属性主题就是`Theme.Provider`分配给它的`value`属性。因此，下拉列表中的变化导致`Provider`组件提供新值。这是一个相当简单的代码流，但它确实显示了我们应该在哪里进行更改，以使消费者组件显示一个新值。

#### 第二个例子——一辆手推车

我们的下一个例子有点不同。我们已经看到了如何从组件状态中公开一个值，并将其作为`Provider`组件的值，从而影响提供者提供的内容。这可以更进一步。此外，在这个意义上，我们不仅可以向消费者展示提供者的价值，还可以展示允许我们改变所提供的价值的方法。让我们看看这个的一些代码:

```
// cart.js

import React from 'react';

const CartContext = React.createContext({
 cart: void 0,
 addItem: () => {}

});

export default CartContext; 
```

我们从创建我们的`Context`对象开始，这次我们给它一个比字符串或数字更复杂的数据类型。`createContext()`方法的输入参数是一个具有属性`cart`的对象`{}`。

一个音符`void 0`就跟`undefined`一样。

接下来，我们将创建两个不同的组件:

*   **CartPage** ，这将包含我们的消费者组件，从而显示来自我们的`Context`对象的值
*   **CartProvider** ，这将是一个组件，它不仅提供来自`Context`对象的值，还公开了一个方法，我们可以用它来更改所提供的值

先说`CartPage`组件:

```
// CartPage.js

import React from 'react';
import CartContext from './cart';

const products = [{
 id: 1,
 title: 'Fortnite'
}, {
 id: 2,
 title: 'Doom'
}, {
 id: 3,
 title: 'Quake'
}]

const CartPage = () => (
 <CartContext.Consumer>
 {({ cart, addItem }) => (
 <React.Fragment>
   <div>
   <h2>Product list</h2>
   {products.map(p => <button onClick={() => addItem(p)} value={p}>{p.title} . </button>)}
   </div>
   <div>
     <h2>Cart</h2>
     {cart.map(item => <div> {item.title} </div>)}
   </div>
 </React.Fragment>
 )}
 </CartContext.Consumer>
);

export default CartPage; 
```

我们在上面看到，我们使用了`CartContext`组件，并且我们定义和显示了我们的购物车值，但是还有一个以`addItem()`方法形式的附加组件。这个方法将允许我们改变购物车中的商品，但是你怎么问呢？让我们看看我们的`CartProvider`组件下一步找出答案:

```
import React from 'react';
import CartPage from './CartPage';
import CartContext from './cart';

class CartProvider extends React.Component {
  constructor() {
    super();

    this.state = {
      cart: [],
      addItem: (item) => {  
        this.setState({  
          cart: [...this.state.cart, { ...item }]  
        })  
      }  
    }
  }

  render() {
    return (
      <CartContext.Provider value = {this.state} >
        <CartPage />
      </CartContext.Provider>
    );
  }

}

export default CartProvider; 
```

我们可以看到，状态对象由属性`cart`和`addItem`组成，传递到`CartContext.Provider`的值属性中的是 this.state，例如`cart`和`addItem()`。这意味着我们可以很容易地用一个`removeItem()`函数或我们需要的任何东西来扩展它，这就是我们如何不仅仅获得一个暴露给`Consumer`组件的值。

### 高阶分量

有时需要在许多地方提供上下文。在上面的例子中，想象一下在一个标题中使用了`cart`,这个标题想要显示购物车中有多少商品。也可能有专门的购物车页面，你可以看到更详细的购物车内容。将所有这些组件内容包装在一个`Consumer`标签中可能会很乏味。在这些情况下，最好使用特设的高阶元件。这意味着我们可以创建一个函数，在这个函数中，我们使用组件作为输入，并扩充上下文数据。

它可能如下所示:

```
// withCart.js
import CartContext from './cart';
import React from 'react';

export const withCart = (Component) => {
 return function fn(props) {
   return (
     <CartContext.Consumer>
     {(context) => <Component {...props} {...context} />}
     </CartContext.Consumer>
   );
 };
}; 
```

正如您在上面看到的，我们使用了一个`Consumer`来实现这一点，但是我们也使用了 spread 参数`{ ...context}`来将上下文对象中的内容传递给底层组件。现在我们可以很容易地使用这个函数来装饰我们的组件，就像这样:

```
// Header.js
import React from 'react';
import withCart from './withCart';

class Header extends React.Component {
  render() {
    const { cart } = this.props;

    return (
      {cart.length === ?
      <div>Empty cart</div> :
      <div>Items in cart: ({cart.length})</div>
      }
    );
  }

}

const HeaderWithCart = withCart(Header);
export default HeaderWithCart; 
```

### 总结

在本文中，我们已经涵盖了相当多的内容。我们已经解释了什么是`Context` API 以及何时使用它。我们还谈到了它的构建模块`Provider`和`Consumer`。此外，我们还讨论了如何更新所提供的值，最后，我们如何使用一个更高阶的组件来清理位。希望您发现这很有用。:)

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris