# 如何将 Angular 和 NgRx 应用程序升级到 v8

> 原文：<https://dev.to/angular/how-to-upgrade-your-angular-and-ngrx-apps-to-v8-4iip>

# 概述

你有一个用 Angular v7 和 NgRx v7 编写的很棒的应用程序，但是一直感觉被忽略了吗？嗯，你很幸运！今天，我们将一起探讨如何使用 Angular CLI 工具升级我们的应用程序以使用 Angular v8。我们还将探索升级到 NgRx v8。这将允许我们利用 NgRx v8 中提供的新特性。NgRx v8 包含了一组闪亮的创建器，或类型安全的工厂函数，用于动作、效果和减少器。

本文改编自[终极课程](https://ultimatecourses.com/blog/how-to-upgrade-your-angular-and-ngrx-apps-to-v8)上的一篇原始帖子。

# 升级依赖关系

## 升级棱角分明

> Angular 团队提供了一个很棒的网站，该网站可以深入了解升级过程。这个网站可以在[角度更新工具](https://update.angular.io/)找到。我们今天将谈到一些信息。

流程的第一步是将我们的应用程序升级到 Angular v8。我们将使用 Angular CLI 来管理这个过程。

这是首选的方法，因为 Angular 提供了内置的迁移脚本或示意图，以减轻我们只需在`package.json`中更新版本所涉及的一些手动过程。

让我们从在终端中运行以下命令开始:

更新全球角度 CLI 版本

```
npm install -g @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

将核心框架和本地 CLI 更新到 v8

```
ng update @angular/cli @angular/core 
```

Enter fullscreen mode Exit fullscreen mode

> 在整个过程中，我们可能会遇到第三方库的问题。在这种情况下，最好访问 GitHub 问题和这些库的库来解决问题。

## 升级 NgRx

现在我们已经将应用程序升级到使用 Angular v8，让我们继续将 NgRx 升级到 v8。这里我们也将使用 Angular CLI。

将 NgRx 更新到 v8

```
ng update @ngrx/store 
```

Enter fullscreen mode Exit fullscreen mode

prior 命令应该更新我们的`package.json`依赖项，并运行任何 NgRx 提供的迁移，以保持我们的应用程序正常工作。

根据您的设置，`ng update @ngrx/store`可能不会自动更新您已安装的附加`@ngrx/*`库。如果发生这种情况，最好的办法是为 NgRx 使用的每个附加模块手动运行`npm install`。

例子如下:

```
npm install @ngrx/entity@latest
npm install @ngrx/effects@latest
npm install @ngrx/data@latest
npm install @ngrx/router-store@latest 
```

Enter fullscreen mode Exit fullscreen mode

### NgRx 迁移指南

NgRx 团队提供了更新到 NgRx v8 的详细迁移指南。关于升级到 NgRx 的 v8 的更多信息可以在这里找到: [V8 更新指南](https://ngrx.io/guide/migration/v8)

# 以身作则——一家水果店(NgRx v7)

学习新方法最流行的方式之一是通过代码示例。让我们来看看下面这个简化的 NgRx 存储的例子，它保存了一个包含`Fruit`个对象的`array`。

每个`Fruit`对象由三个属性`fruitId`、`fruitClass`和`fruitName`组成。

```
interface Fruit {
    fruitId: number;
    fruitType: string;
    fruitName: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我们有一个`orange`，它可能看起来像这样:

```
const orange: Fruit = {
    fruitId: 1,
    fruitType: 'citrus',
    fruitName: 'orange'
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 状态

进一步探索，NgRx 存储中的`State`对象将包含类似`fruits`、`isLoading`和`errorMessage`的属性。

*   `fruits`被定义为一个`array`用于`Fruit`对象
*   `isLoading`是一个`boolean`，用于跟踪商店何时从外部 API 加载数据。
*   `errorMessage`是一个`string`属性，它是`null`，除非从外部 API 请求数据时出现错误。

一个例子`State` `interface`可能如下:

```
interface State {
    fruits: Fruit[];
    isLoading: boolean;
    errorMessage: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

加载了`fruits`的示例商店可能如下所示:

```
const state: State = {
    fruits: [
        {
            fruitId: 1,
            fruitType: 'citrus',
            fruitName: 'orange'
        }
    ],
    isLoading: false,
    errorMessage: null
} 
```

Enter fullscreen mode Exit fullscreen mode

## 动作

遵循适当的 redux 模式指导，我们不能直接更新状态，所以我们需要定义一组动作，通过一个 reducer 来处理我们的状态。假设本例中有 3 个动作:

*   `[App Init] Load Request` -该动作旨在从我们的 UI 层发出，以表明我们正在请求将`Fruit`对象加载到我们的存储中。该动作没有有效载荷或`props`。

*   `[Fruits API] Load Success` -当已经调度了一个`[App Init] Load Request`，已经调用了一个 API，并且从 API 接收到成功的响应时，这个动作将从我们的效果中被调度。这个动作包含一个有效载荷或`props`对象，它包含了要加载到我们商店的`Fruit`对象的`array`。

*   `[Fruits API] Load Failure` -当`[App Init] Load Request`已经被调度，API 已经被调用，并且从 API 接收到失败响应时，这个动作打算从我们的效果中被调度。这个动作包含一个有效载荷或`props`对象，它包含了我们的 API 请求的错误消息，因此它可以被加载到我们的存储中。

### NgRx v7 实现

我们动作的实际 NgRx v7 实现可能如下所示:

```
import { Action } from '@ngrx/store';
import { Fruit } from '../../models';

export enum ActionTypes {
  LOAD_REQUEST = '[App Init] Load Request',
  LOAD_FAILURE = '[Fruits API] Load Failure',
  LOAD_SUCCESS = '[Fruits API] Load Success'
}

export class LoadRequestAction implements Action {
  readonly type = ActionTypes.LOAD_REQUEST;
}

export class LoadFailureAction implements Action {
  readonly type = ActionTypes.LOAD_FAILURE;
  constructor(public payload: { error: string }) {}
}

export class LoadSuccessAction implements Action {
  readonly type = ActionTypes.LOAD_SUCCESS;
  constructor(public payload: { fruits: Fruit[] }) {}
}

export type ActionsUnion = LoadRequestAction | LoadFailureAction | LoadSuccessAction; 
```

Enter fullscreen mode Exit fullscreen mode

### NgRx v8 -升级到 createAction

> 值得注意的是，虽然`createAction`是 NgRx 中定义`Action`的热门新方法，但是现有的定义`enum`、`class`和导出类型联合的方法在 NgRx v8 中仍然可以很好地工作。

从 NgRx 版本 8 开始，可以使用新的`createAction`方法来声明动作。这个方法是一个`factory function`，或者是一个返回`function`的`function`。

根据[官方 NgRx 文档](https://ngrx.io/guide/store/actions#writing-actions)，`createAction`函数返回一个函数，当被调用时返回一个`Action`接口形状的对象。`props`方法用于定义处理动作所需的任何附加元数据。动作创建者提供了一种一致的、类型安全的方式来构造正在被调度的动作。

为了更新到`createAction`，我们需要做以下步骤:

1.  为我们的行动创建一个新的`export const`。如果我们的动作有一个有效负载，我们也需要迁移到使用`props`方法来定义我们的有效负载为`props`。

`[App Init] Load Request`的例子为

```
// before
export class LoadRequestAction implements Action {
  readonly type = ActionTypes.LOAD_REQUEST;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// after
export const loadRequest = createAction('[App Init] Load Request'); 
```

Enter fullscreen mode Exit fullscreen mode

`[Fruits API] Load Success`的例子为

```
// before
export class LoadSuccessAction implements Action {
  readonly type = ActionTypes.LOAD_SUCCESS;
  constructor(public payload: { fruits: Fruit[] }) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// after
export const loadSuccess = createAction('[Fruits API] Load Success', props<{fruits: Fruit[]}>()); 
```

Enter fullscreen mode Exit fullscreen mode

1.  从`ActionTypes`中删除旧动作`enum`

2.  从`ActionsUnion`中删除旧动作

我们最终迁移的动作文件可能看起来像这样:

```
import { Action, props } from '@ngrx/store';
import { Fruit } from '../../models';

export const loadRequest = createAction('[App Init] Load Request');
export const loadFailure = createAction('[Fruits API] Load Failure', props<{errorMessage: string}>());
export const loadSuccess = createAction('[Fruits API] Load Success', props<{fruits: Fruit[]}>()); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，这是一个巨大的代码缩减，我们已经从 24 行代码减少到 6 行代码。

### NgRx v8 -调度 createAction 动作

最后要注意的是，我们需要更新我们分派行动的方式。这是因为我们不再需要创建`class`实例，而是调用返回动作对象的`factory`函数。

我们的前后会是这样的:

```
// before 
this.store.dispatch(new featureActions.LoadSuccessAction({ fruits }))

// after
this.store.dispatch(featureActions.loadSuccess({ fruits })) 
```

Enter fullscreen mode Exit fullscreen mode

## 减速器

继续我们的例子，我们需要一个 reducer 设置来代理我们对商店的更新。回想一下 redux 模式，我们不能直接更新状态。我们必须通过一个纯函数，接受当前状态，一个动作，并返回一个应用了动作的新的更新状态。典型地，reducers 是大型的`switch`语句，主要针对输入的动作。

让我们想象我们的减速器处理以下场景:

*   在`[App Init] Load Request`上，我们希望状态反映以下值:

    *   `state.isLoading: true`
    *   `state.errorMessage: null`
*   在`[Fruits API] Load Success`上，我们希望状态反映以下值:

    *   `state.isLoading: false`
    *   `state.errorMessage: null`
    *   `state.fruits: action.payload.fruits`
*   在`[Fruits API] Load Failure`上，我们希望状态反映以下值:

    *   `state.isLoading: false`
    *   `state.errorMessage: action.payload.errorMessage`

### NgRx v7 实现

我们的缩减器的实际 NgRx v7 实现可能如下所示:

```
import { ActionsUnion, ActionTypes } from './actions';
import { initialState, State } from './state';

export function featureReducer(state = initialState, action: ActionsUnion): State {
  switch (action.type) {
    case ActionTypes.LOAD_REQUEST: {
      return {
        ...state,
        isLoading: true,
        errorMessage: null
      };
    }
    case ActionTypes.LOAD_SUCCESS: {
      return {
        ...state,
        isLoading: false,
        errorMessage: null,
        fruits: action.payload.fruits
      };
    }
    case ActionTypes.LOAD_FAILURE: {
      return {
        ...state,
        isLoading: false,
        errorMessage: action.payload.errorMessage
      };
    }
    default: {
      return state;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### NgRx v8 -升级到 createReducer

> 值得注意的是，虽然`createReducer`是 NgRx 中定义缩减器的热门新方法，但是用`switch`语句定义`function`的现有方法在 NgRx v8 中仍然可以很好地工作。

从 NgRx 版本 8 开始，可以使用新的`createReducer`方法来声明 reducers。

根据[官方 NgRx 文档](https://ngrx.io/guide/store/reducers#creating-the-reducer-function)，“reducer 函数的职责是以不可变的方式处理状态转换。使用`createReducer`函数创建一个 reducer 函数来处理管理状态的操作。

为了更新到`createReducer`，我们需要做以下步骤:

1.  为我们的减速器创建一个新的`const reducer = createReducer`。
2.  将我们的`switch` `case`语句转换成`on`方法调用。请注意，`default`案例是自动为我们处理的。`on`方法的第一个参数是要触发的动作，第二个参数是接收`state`并返回新版本`state`的处理程序。如果动作提供了`props`，那么可以提供第二个可选的输入参数。在下面的例子中，我们将使用析构从`props`对象中提取必要的属性。
3.  创建一个新的`export function reducer`来包装我们的`const reducer`以获得`AOT`的支持。

一旦完成，我们更新后的`featureReducer`将如下所示:

```
import { createReducer, on } from '@ngrx/store';
import * as featureActions from './actions';
import { initialState, State } from './state';
...
const featureReducer = createReducer(
  initialState,
  on(featureActions.loadRequest, state => ({ ...state, isLoading: true, errorMessage: null })),
  on(featureActions.loadSuccess, (state, { fruits }) => ({ ...state, isLoading: false, errorMessage: null, fruits })),
  on(featureActions.loadFailure, (state, { errorMessage }) => ({ ...state, isLoading: false, errorMessage: errorMessage })),
);

export function reducer(state: State | undefined, action: Action) {
  return featureReducer(state, action);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 效果

因为我们想保持我们的 reducer 是一个纯粹的函数，所以将 API 请求放在`side-effects`中通常是可取的。在 NgRx 中，这些被称为`Effects`,并提供了一种反应式的、基于 RxJS 的方法来将动作链接到可观察的流。

在我们的例子中，我们将有一个用于`[App Init] Load Request`动作的`Effect`和一个对我们假想的`Fruits API`后端的 HTTP 请求。

*   当来自`Fruits API`的结果成功时，响应被映射到一个`[Fruits API] Load Success`动作，将`fruits`的有效载荷设置到成功响应的主体。

*   根据来自`Fruits API`的失败结果，错误消息被映射到`[Fruits API] Load Failure`动作，将`errorMessage`的有效载荷设置为来自失败响应的错误。

### NgRx v7 实现

我们效果的实际 NgRx v7 实现可能如下所示:

```
@Effect()
  loadRequestEffect$: Observable<Action> = this.actions$.pipe(
    ofType<featureActions.LoadRequestAction>(
      featureActions.ActionTypes.LOAD_REQUEST
    ),
    concatMap(action =>
      this.dataService
        .getFruits()
        .pipe(
          map(
            fruits =>
              new featureActions.LoadSuccessAction({
                fruits
              })
          ),
          catchError(error =>
            observableOf(new featureActions.LoadFailureAction({ errorMessage: error.message }))
          )
        )
    )
  ); 
```

Enter fullscreen mode Exit fullscreen mode

### NgRx v8 -升级创建效果

> 值得注意的是，虽然`createEffect`是 NgRx 中定义缩减器的热门新方法，但是用`@Effect()`装饰器定义类属性的现有方法在 NgRx v8 中仍然可以很好地工作。

根据[官方 NgRx 文档](https://ngrx.io/guide/effects#writing-effects)，从 NgRx 第 8 版开始，特效可以使用新的`createEffect`方法来声明。

为了更新到`createEffect`，我们需要做以下步骤:

1.  从`@ngrx/effects`导入`createEffect`
2.  移除`@Effect()`装饰器
3.  移除`Observable<Action>`类型注释
4.  用`createEffect(() => ...)`包裹`this.actions$.pipe(...)`
5.  从`ofType`中移除`<featureActions.LoadRequestAction>`类型注释
6.  将`ofType`输入参数从`featureActions.ActionTypes.LOAD_REQUEST`更改为`featureActions.loadRequest`
7.  更新动作调用以删除`new`并使用创建者而不是`class`实例。比如`new featureActions.LoadSuccessAction({fruits})`变成了`featureActions.loadSuccess({fruits})`。

一旦完成，我们更新后的`loadRequestEffect`将如下所示:

```
 loadRequestEffect$ = createEffect(() => this.actions$.pipe(
        ofType(featureActions.loadRequest),
        concatMap(action =>
        this.dataService
            .getFruits()
            .pipe(
                map(fruits => featureActions.loadSuccess({fruits})),
                catchError(error =>
                    observableOf(featureActions.loadFailure({ errorMessage: error.message }))
                )
            )
        )
    )
  ); 
```

Enter fullscreen mode Exit fullscreen mode

# 全视频演练

如果你想看完整的视频，请点击这里。

[https://www.youtube.com/embed/0LK73MdhED4](https://www.youtube.com/embed/0LK73MdhED4)

# 结论

这就把我们带到了本指南的结尾。希望您已经了解了如何将您的应用程序升级到 Angular v8 和 NgRx v8。此外，您应该有信心利用 NgRx v8 中的一些新特性来减少可能被称为样板文件的东西的出现。更新升级快乐！