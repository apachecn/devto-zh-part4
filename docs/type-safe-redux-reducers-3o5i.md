# 类型安全 Redux 减速器

> 原文：<https://dev.to/domysee/type-safe-redux-reducers-3o5i>

要解决的主要问题是在检查 Redux actions 的`type`属性后，如何向 TypeScript 显示具体的 action 类型是什么。

## 直截了当的解决

最直接的方法是为每个动作创建一个类型，并使用 [type guards](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types) 来区分它们。

类型保护是布尔函数，它检查参数是否是特定的类型，由返回值`parameter is Type`表示。

用在条件中，TS 假设传递给类型守卫的变量是守卫检查的类型。当然只是在真正的分支中。

带有类型保护的示例解决方案:

```
interface Action {
  type: string;
}

interface StartAction extends Action {
  date: Date;
}

interface SuccessAction extends Action {
  data: number;
}

interface ErrorAction extends Action {
  error: string;
}

export const createStart: () => StartAction = () => ({
  type: 'Start',
  date: new Date()
});

export const createSuccess = (data: number): SuccessAction => ({
  type: 'Success',
  data
});

export const createError = (error: string): ErrorAction => ({
  type: 'Error',
  error
});

// TYPE GUARDS
const isStartAction = (action: Action): action is StartAction => 
  action.type === 'Start';

const isSuccessAction = (action: Action): action is SuccessAction => 
  action.type === 'Success';

const isErrorAction = (action: Action): action is ErrorAction => 
  action.type === 'Error';

export interface State {
  lastStarted?: Date;
  data?: number;
  error?: string;
}

const defaultState: State = {
  lastStarted: undefined,
  data: undefined,
  error: undefined
};

const reducer = (state = defaultState, action: Action) => {
  if (isStartAction(action)) {
    return {
      lastStarted: action.date,
      data: undefined,
      error: undefined
    };
  }

  if (isSuccessAction(action)) {
    return {
      data: action.data,
      error: undefined
    };
  }

  if (isErrorAction(action)) {
    return {
      data: undefined,
      error: action.error
    };
  }

  return state;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是相当冗长。

对于每一个动作，有必要定义

*   动作类型
*   动作创建者
*   字体保护

每当一个动作改变，一个属性被添加/删除/改变，它必须在两个地方适应，特定的动作接口和动作创建者。

两者都去掉是有可能的，但那需要先绕一点弯路。

## 从并集推断具体类型

TS 有一个简洁的类型推断特性。在特定情况下，它可以从一个联合中推断出具体的类型。我的意思是这个

```
const x: A | B = someObject;
if (someCondition) {
  // x is of type A here
} 
```

Enter fullscreen mode Exit fullscreen mode

为此，`A`和`B`必须有一个共同的属性。在 Redux actions 中应该是`type`。这是必要的，以便 TS 知道可以在条件中访问该属性。

此外，`type`属性必须是文字类型，例如文字字符串(如`'Start'`)或数字(如`123`)。

此类型为入住条件。由于每个类型的`type`属性只能是一个特定的值，所以 TS 可以根据给定的值来推断类型。

```
interface A {
  type: 'Start';
  propA: number;
}

interface B {
  type: 'End';
  propB: number;
}

if (x.type === 'Start') {
  // x is of type A here
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，`type`属性必须是文字类型。如果它们是一个泛型`string`，那么就没有什么可以进行类型推断的了。

## 利用联合来推断动作类型

有了这些知识，就有可能去掉类型守卫，换来添加`ActionTypes`，它是该 reducer 中处理的所有动作类型的联合。

```
interface StartAction {
  type: 'Start';
  date: Date;
}

interface SuccessAction {
  type: 'Success';
  data: number;
}

interface ErrorAction {
  type: 'Error';
  error: string;
}

type ActionTypes = StartAction | SuccessAction | ErrorAction;

export const createStart = (): StartAction => ({
  type: 'Start',
  date: new Date();
});

export const createSuccess = (data: number): SuccessAction => ({
  type: 'Success',
  data
});

export const createError = (error: string): ErrorAction => ({
  type: 'Error',
  error
});

export interface State {
  lastStarted?: Date;
  data?: number;
  error?: string;
}

const defaultState: State = {
  data: undefined,
  error: undefined
};

export default (state = defaultState, action: ActionTypes) => {
  if (action.type === 'Start') {
    return {
      lastStarted: action.date,
      data: undefined,
      error: undefined
    };
  }

  if (action.type === 'Success') {
    return {
      data: action.data,
      error: undefined
    };
  }

  if (action.type === 'Error') {
    return {
      data: undefined,
      error: action.error
    };
  }

  return state;
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 用`ReturnType`删除动作类型

最后一个改进是根据动作创建者的返回值自动推断动作类型。

这可以通过助手类型`ReturnType`来完成。

顾名思义，它是一个泛型类型，如果传递一个函数，返回返回值的类型。例如

```
type F = (p1: string, p2: number) => boolean;
type T = ReturnType<F>;
// T = boolean 
```

Enter fullscreen mode Exit fullscreen mode

如果你对它的工作原理感兴趣，可以看看我的另一篇博客文章[中 TypeScript](https://www.domysee.com/blogposts/ts-builtin-types-list) 的内置助手类型列表。

通过使用该辅助类型，可以直接从动作创建者的返回类型中创建联合类型。

这里需要注意的一点是，返回的对象是这样定义`type`的:`'Start' as 'Start'`。

这就是为什么推断出的类型是文字字符串(`'Start'`)，因为默认情况下，在给字符串赋值时，TS 推断它的类型是`string`。

```
type ActionTypes =
  | ReturnType<typeof createStart>
  | ReturnType<typeof createSuccess>
  | ReturnType<typeof createError>;

export const createStart = () => ({
  type: 'Start' as 'Start',
  date: new Date();
});

export const createSuccess = (data: number) => ({
  type: 'Success' as 'Success',
  data
});

export const createError = (error: string) => ({
  type: 'Error' as 'Error',
  error
});

export interface State {
  lastStarted?: Date;
  data?: number;
  error?: string;
}

const defaultState: State = {
  data: undefined,
  error: undefined
};

export default (state = defaultState, action: ActionTypes) => {
  if (action.type === 'Start') {
    return {
      lastStarted: action.date,
      data: undefined,
      error: undefined
    };
  }

  if (action.type === 'Success') {
    return {
      data: action.data,
      error: undefined
    };
  }

  if (action.type === 'Error') {
    return {
      data: undefined,
      error: action.error
    };
  }

  return state;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是最后一个例子，尽可能的简洁和最少的输入。

尽管所有的解决方案都是有效的，但是根据代码库的其余部分、团队和您的偏好，您可能想要选择一个更明确的(第一个)方法来实现类型安全的减少器。