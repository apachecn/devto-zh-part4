# 一个写干净的 Redux 连通分支的模式

> 原文：<https://dev.to/blnkspace/a-pattern-to-write-clean-redux-connected-components-ce0>

一些组件有很多副作用。我的一些项目使用 Redux 和 redux-saga 进行所有副作用管理。我在 Redux web 应用程序上遇到了很多代码，其中 mapDispatchToProps 函数看起来非常庞大和怪异。而且，我见过很多人手动调用

```
 dispatch(someActionCreator); 
```

贯穿其组成部分；这又是一件看起来不太好的事情。

我认为当你的组件只使用了很少的选择器和动作时，useSelector 和 useDispatch 是很棒的；但是除此之外，我没有看到自己在 React 组件的前 20 行使用 useSelectors 然后必须将每个 actionCreator 包装在一个调度中。我是这样做的:

```
// makeActionCreatorsToBind.js

import { pick } from 'ramda';

export default ({ actionCreators, keysToPick }) => pick(keysToPick, actionCreators); 
```

```
import React from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import { compose, assoc } from 'ramda';
import { createStructuredSelector } from 'reselect';

import CheckoutPage from 'modules/Checkout/';

import * as checkoutActionCreators from 'modules/Checkout/redux/actions';

import { makeSelectCart } from 'modules/Cart/redux/selectors';

import makeActionCreatorsToBind from './makeActionCreatorsToBind';

function Checkout(props) {
  return (
    <CheckoutPage
      {...props}
    />
  );
}

/* add some other action creators from different modules,
* I'm using Ramda here just so people reading this can
* pick up composition patterns.
*/
const actionCreators = compose(
  assoc('getCreditBalanceRequest', getCreditBalanceRequest),
  assoc('reinitializeCart', reinitializeCart),
))(checkoutActionCreators);

// another way of doing the same thing:
const actionCreatorsMadeWithoutRamda = {
  ...checkoutActionCreators,
  getCreditBalanceRequest,
  reinitializeCart,
};

const actionCreatorsToBind = makeActionCreatorsToBind({
  actionCreators,
  keysToPick: [
    'reinitializeCart',
    'getCreditBalanceRequest',
    'shippingRequest',
    'createOrderRequest',
    'sendOrderDetails',
    'getTotalsRequest',
    'confirmOrderRequest',
    'reinitializeCheckout',
    'applyAddressData',
    'applyCouponCodeRequest',
  ]
});

const mapDispatchToProps = dispatch => bindActionCreators(actionCreatorsToBind, dispatch);

const mapStateToProps = createStructuredSelector({
  cart: makeSelectCart(),
});

export default connect(mapStateToProps, mapDispatchToProps)(Checkout); 
```

我将解释一些代码。

## bindActionCreators

Redux 公开了这个函数，它基本上可以接受一个带有键值对的对象，其中的值是 action creators 用 dispatch 修饰所有的动作创建者，这样你就可以调用键，也就是那些动作创建者的名字，就像它们是普通的函数一样。

## 拉姆达比特

### assoc = >基本上是执行 my object[' property name ']= someActionCreator 的函数方式

### 【compose =>从最后一个函数开始，返回一个函数，该函数根据所提供的参数从右到左(从下到上)应用所有函数。

ramda 位基本上创建了一个对象`actionCreators`，它接受一个参数(在本例中，checkoutActionCreators 来自一个描述动作创建者的文件，这里有许多*这样的动作创建者)，并将来自其他文件的其他动作创建者添加到该对象中。*

compose 函数将关键字`reinitializeCart`和`getCreditBalanceRequest`与同名动作创建者的值相加；从其他模块导入。

然后，我们使用这个新对象调用 makeActionCreatorsToBind 函数，并将结果赋给一个名为 ActionCreatorsToBind 的变量。该函数只取出组件需要的动作，并返回一个带有指定键的对象，忽略 checkoutActionCreators 中我们不需要的动作。

现在我们可以将这个新对象(名为 actionCreatorsToBind)传递给 bindActionCreators，瞧，一切看起来都很整洁，运行良好。