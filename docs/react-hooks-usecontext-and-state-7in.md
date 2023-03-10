# 反应钩子——使用上下文和状态

> 原文：<https://dev.to/rjain15/react-hooks-usecontext-and-state-7in>

我正在使用 useContext react hook，当我从 useContext 中获取上下文时，有办法访问状态吗？

我可能把事情搞混了，

这是我的全局状态组件

```
 <GlobalContext.Provider
      value={{
        loadingProducts: state.loadingProducts,
        products: state.products,
        cart: state.cart,
        searchTerm: state.searchTerm,
        addProductToCart: addProductToCart,
        removeProductFromCart: removeProductFromCart,
        showAllProducts: showAllProducts,
        showFilteredProducts: showFilteredProducts,
        initCatalog: initCatalog,
        catalogLoadSuccess: catalogLoadSuccess,
        catalogLoadFailure: catalogLoadFailure

      }}
    >
      {props.children}
    </GlobalContext.Provider> 
```

这是我的初始状态对象

```
const initialState = {
  initialProducts: [],
  products: [],
  cart: [],
  searchTerm: '',
  loadingProducts: true
}; 
```

我已经初始化了全局状态

```
const GlobalState = props => {
  const [state, dispatch] = useReducer(reducer, initialState); 
```

我想从我的组件中获取状态对象(loadingProducts ),这是我获取它的方式。

```
 const context = useContext(GlobalContext);
  const {loadingProducts} = context.loadingProducts 
```

问题:我能在组件 ie 中直接从上下文中访问状态吗？

const context = useContext(全局上下文)；

//还是有替代方案？const state = context.state