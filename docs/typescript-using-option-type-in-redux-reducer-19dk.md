# Typescript -在 Redux Reducer 中使用选项数据类型

> 原文：<https://dev.to/piq9117/typescript-using-option-type-in-redux-reducer-19dk>

我对 redux 中的 reducers 的一个担心是它可能会变得无限大。我注意到这种情况正在我们的减速器上发生。

因为我不能展示我们的代码，所以这里有一个虚构的例子。

```
 const listLens = Lens.fromProp<State, "list">("list");
  const newUserLens = Lens.fromProp<State, "newUser">("newUser");
  const initUserLens = Lens.fromProp<NewUserState, "initial">("initial");
  const confrmUserLens = Lens.fromProp<NewUserState, "confirmed">("confirmed");

  const isBlankUser = (u: User) => u.firstName !== undefined;

  const setList = (state: State, action: RAction<Response>) =>
    listLens.modify(l =>
      filter(isBlankUser)(concat(action.payload.items, l))
    )(state);

  const setInitial = (state: State, action: RAction<User>) =>
    newUserLens
      .compose(initUserLens)
      .modify(x => merge(x, action.payload))(state);

  const setConfirmed = (state: State) =>
    newUserLens
      .compose(confrmUserLens)
      .modify(x =>
        merge(x, newUserLens.compose(initUserLens).get(state))
      )(state);

  const setEdited = (state: State, action: RAction<Res<User>>) =>
    state;

  const resetNewUser = (state: State) =>
    newUserLens.set(newUserLens.get(initialState))(state);

  const resetState = (state: State) => initialState;

  export const userList = (state: State = initialState, action: Action) => {
    switch (action.type) {
      case SET_USER_LIST:
        return setList(state, action);

      case SET_INITIAL_NEW_USER:
        return setInitial(state, action);

      case SET_CONFIRMED_NEW_USER:
        return setConfirmed(state, action);

      case SET_EDITED_USER:
        return setEditedUser(state, action);

      case RESET_NEW_USER_STATE:
        return resetNewUser(state, action);

      case RESET_STATE:
        return resetState(state);

      // imagine more case statements here. Maybe 50 more...

      default:
        return state;
    }
  }; 
```

正如你所看到的，它可以增长到有更多的线条！

幸运的是，我找到了 Vinicius Gomes 的这篇文章。它讲述了如何通过使用`Maybe`类型来减少缩减器中的样板文件。它将摆脱一个典型的写有`switch`语句的减速器中不断增长的`case` s 的大小。

上面的代码片段可以变成这样。

```
 import { fromNullable } from "fp-ts/lib/Option";
  import { filter, concat, merge } from "ramda";
  import { Lens } from "monocle-ts";
  import { State, Action, RAction, User, Res, NewUserState } from "./types";
  import { initialUser, initialNewUser } from "./initial-values";

  const initialState: State = {
    list: [initialUser],
    newUser: {
      initial: initialNewUser,
      confirmed: initialNewUser
    },
    selectedUser: initialUser
  };

  type Response = Res<ReadonlyArray<User>>;

  interface Handlers {
    [type: string]: (s: State, a: Action) => State;
  }

  const listLens = Lens.fromProp<State, "list">("list");
  const newUserLens = Lens.fromProp<State, "newUser">("newUser");
  const initUserLens = Lens.fromProp<NewUserState, "initial">("initial");
  const confrmUserLens = Lens.fromProp<NewUserState, "confirmed">("confirmed");

  const isBlankUser = (u: User) => u.firstName !== undefined;

  const SET_USER_LIST = (state: State, action: RAction<Response>) =>
    listLens.modify(l =>
      filter(isBlankUser)(concat(action.payload.items, l))
    )(state);

  const SET_INITIAL_NEW_USER = (state: State, action: RAction<User>) =>
    newUserLens
      .compose(initUserLens)
      .modify(x => merge(x, action.payload))(state);

  const SET_CONFIRMED_NEW_USER = (state: State) =>
    newUserLens
      .compose(confrmUserLens)
      .modify(x =>
        merge(x, newUserLens.compose(initUserLens).get(state))
      )(state);

  const SET_EDITED_USER = (state: State, action: RAction<Res<User>>) =>
    state;

  const RESET_NEW_USER = (state: State) =>
    newUserLens.modify(() => newUserLens.get(initialState))(state);

  const RESET_STATE = (state: State) => initialState;

  const actionHandlers: Handlers = {
    SET_USER_LIST,
    SET_INITIAL_NEW_USER,
    SET_CONFIRMED_NEW_USER,
    SET_EDITED_USER,
    RESET_NEW_USER,
    RESET_STATE
  };

  export const userList = (state: State = initialState, action: Action) =>
    fromNullable(actionHandlers[action.type])
      .map(f => f(state, action))
      .getOrElseValue(state); 
```

我没有使用`Maybe`型，而是使用了 [fp-ts](https://github.com/gcanti/fp-ts) 的`Option`型。`Option`和`Maybe`类型是同义词。

根据 [fp-ts](https://gcanti.github.io/fp-ts/modules/Option.ts.html)

#### fromNullable

```
<A>(a: A | null | undefined): Option<A> 
```

在这个上下文中，如果`actionHandlers[action.type]`出现在`undefined`上，它将返回数据构造器`None`，如果链中有`None`的话，底部的`getOrElse`将返回`state`。

这里是`getOrElse`的类型签名

#### getOrElse

```
(a: A): A 
```

当一个输入的`type`与我在`actionHandlers`中的一个函数匹配时，`map`会将那个函数应用到`state`。

最后，我更改了我的 reducer 函数的名称，并删除了一长串导入的常量。

## 结论

我已经改变了减速器主体，减少了运动部件。它现在只有 3 个链接的函数调用，而不是许多`case`语句。我还避免了导入 action-creator 常量(即`SET_USER_LIST`)。