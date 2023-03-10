# 在 cypress.io 中获取 API gotcha 以及如何修复。

> 原文：<https://dev.to/matsilva/fetch-api-gotcha-in-cypress-io-and-how-to-fix-it-7ah>

**问题:**
当你在客户端使用 fetch API 时，你无法在 [cypress.io](https://www.cypress.io/) 中存根路由，因为在 cypress atm 中[缺乏支持](https://github.com/cypress-io/cypress/issues/95)。

这是我在实现 graph QL[Apollo-client](https://www.apollographql.com/docs/react/)[Apollo-link-rest](https://www.apollographql.com/docs/link/links/rest/)时遇到的事情，因为它使用开箱即用的获取 api。

下面是我修复它的方法:
首先，我确保使用 [whatwg-fetch](https://github.com/whatwg/fetch) 填充 fetch api，但只限于开发或测试环境，因为我在生产版本中不需要它。

所以在我的应用程序`index.tsx`的主入口点，我在顶部附近添加了这个。

```
//**this will get stripped out of production builds by webpack */
//@ts-ignore
if (!PRODUCTION) {
    require('whatwg-fetch'); //for polyfill cypress test 
} 
```

对于我的`webpack.development.js`配置文件，我添加了这个:

```
 plugins: [
    new webpack.DefinePlugin({
        PRODUCTION: JSON.stringify(false),
    }),
], 
```

在我的`webpack.production.js`配置文件中，我添加了这个:

```
 plugins: [
    new webpack.DefinePlugin({
        PRODUCTION: JSON.stringify(true),
    }),
], 
```

接下来，我给 cypress 添加了一个新的`visitWithDelWinFetch`命令，它将删除`window`对象上的 fetch api。

```
Cypress.Commands.add('visitWithDelWinFetch', (path, opts = {}) => {
    cy.visit(
        path,
        Object.assign(opts, {
            onBeforeLoad(win) {
                delete win.fetch;
            },
        })
    );
}); 
```

这将启动回迁聚合填充。现在你可以像在赛普拉斯一样选择路线了。

```
cy.server();
cy.route('/path/to/api/').as('getApiPath');
cy.visitWithDelWinFetch('/'); 
```

值得称赞的是，这很大程度上是受评论中[这个解决方案](https://github.com/cypress-io/cypress/issues/95#issuecomment-281273126)的启发。

还要注意，如果您不在乎在测试中使用 fetch api，您可以避免使用单独的 visit 命令，将 [this solution](https://github.com/cypress-io/cypress/issues/95#issuecomment-343214638) 与 fetch polyfill 配对使用。