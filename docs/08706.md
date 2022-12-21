# Redux 中的中间件登录

> 原文：<https://dev.to/fosteman/middleware-logging-in-redux-opj>

每个分派的动作都流经中间件。在分派的动作到达减速器之前，可以选择特定的功能。

## Redux 记录器

这个库记录开发人员控制台中的操作，给出可跟踪的用户操作栈。

中间件应用于状态初始化阶段，增强器`applyMiddlware()`

```
 import { applyMiddleware, createStore } from 'redux';
    const store = createStore( reducer,
    undefined, applyMiddleware(...)
    ); 
```

要使用 redux-logger，请将实例传递给此函数

```
 import { applyMiddleware, createStore } from 'redux';
    import { createLogger } from 'redux-logger';

    const logger = createLogger();
    const store = createStore(reducer,
        undefined, 
        applyMiddleware(logger)
    ); 
```

现在，每个动作在调度时都应该在开发人员控制台中可见。

请注意，许多中间件都可以传入

```
applyMiddleware(firstMiddleware, secondMiddleware, ...); 
```

这样，动作将在到达减速器阶段之前流过每一个。因此，很明显，在被每个中间件修改后，您会想要记录完整的操作。将`redux-logger`作为后面的参数传递给中间件增强器。