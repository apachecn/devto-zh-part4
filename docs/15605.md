# 冗余和单一用途功能

> 原文：<https://dev.to/ivanalejandro0/redux-and-single-purpose-functions-100i>

## 序言

在这篇文章中，我将分享我在使用[蜘蛛橡树信号](https://spideroak.com/semaphor/)时学到的一些经验，在这里我们使用:

*   Javascript 和[反应](https://reactjs.org/)来构建我们的 UI 组件。
*   Jest 编写我们的测试。
*   [Redux](https://redux.js.org/) 作为我们数据的单一真实来源。

尽管这篇文章展示了 Redux 环境中的函数，但是您可以将这个想法推广到任何 javascript 函数。我将使用 Jest 展示测试，但是你也可以使用任何你喜欢的工具。Jest 测试本身可读性很好，即使你以前没有见过 jest 测试。

即使你不知道 redux/jest，你也应该能从这篇文章中得到一些想法。

## TL；博士？

*   使用单一用途的函数来简化您的代码。
*   向组合中添加测试，您将拥有可以信任的代码。
*   将你的减速器的逻辑分解成更易管理的功能，而不是一个巨大的`switch`。
*   您现在有了一个更易维护和更值得信赖的代码库。

更多详情，请继续阅读:)

## Redux 的还原器

> Reducers 指定应用程序的状态如何改变，以响应发送到存储的操作。请记住，动作只描述发生了什么，而不描述应用程序的状态如何变化。
> (来自:[https://redux.js.org/basics/reducers](https://redux.js.org/basics/reducers))

Reducers(几乎总是)以大函数的形式出现，带有一个`switch`来决定根据接收到的动作做什么。

让我们看看为什么这是一个问题，以及我们如何使用更好的方法。

## 例题和问题

假设我们想在 Redux 上存储一些用户。我们需要添加和删除用户的操作。

这通常是我们在教程中看到的减速器:

```
// e.g. src/actions/users.js
export const USER_ADD = "USER_ADD";
export const USER_REMOVE = "USER_REMOVE";

// e.g. src/reducers/usersById.js
export function usersById(state = {}, action) {
  let newState;
  switch (action.type) {
    case USER_ADD:
      return {
        ...state,
        [action.user.id]: action.user,
      };
    case USER_REMOVE:
      newState = { ...state };
      delete newState[action.id];
      return newState;
    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有几个问题:

*   这个函数变得很长，嵌套太多，这使得它更难阅读，更难测试，也更容易出错。
*   该功能有几个责任，这使得它更难记录。您将添加一个大的 docstring 来解释这个函数做的所有事情。
*   关于`switch`的词法范围分享，在这里阅读更多:[https://eslint.org/docs/rules/no-case-declarations](https://eslint.org/docs/rules/no-case-declarations)。
*   不清楚您期望从`action`中获得什么数据。

请注意，随着代码库的增长，这些问题会变得非常明显，但对于这种人为的例子来说就不是这样了。

## 添加测试

我们将通过做一些重构来解决我们的问题，但是在此之前，我们将为我们的缩减器写一些测试。

免责声明:真实世界的代码会更复杂，添加测试会比这个虚构的例子更有意义。

我建议您在开始对工作代码进行更改之前编写测试，这样您就可以确保在更改之后它仍然可以工作。

这里有一个例子(使用 [jest](https://jestjs.io/) )展示了这个用例的测试是如何进行的:

```
// e.g. src/reducers/usersById.test.js
import { userAdd } from 'actions/users';
import { usersById } from 'reducers/usersById';

test('on USER_ADD, with empty state', () => {
  // define your initial state
  const initialState = {};

  // define payload for the action we'll use to test the reducer
  const user = { id: 'user-id-1', username: 'john-doe-01' };

  // define expected state
  const expectedState = {
    [user.id]: user,
  };

  // call our reducer
  const result = usersById(initialState, userAdd(user));

  // check that the result we got is what we need
  expect(result).toEqual(expectedState);

  // check that we don't mutate the state
  expect(result).not.toBe(initialState);
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，为了测试我们的 reducers，我们甚至不需要 Redux，它们只是函数。

我们不(直接)测试我们的动作创建者，因为他们应该简单到:

```
// e.g. src/actions/users.js
export const USER_ADD = "USER_ADD";

export function userAdd(user) {
  return {
    type: USER_ADD,
    user,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们通过在 reducer 的测试中调用它们来间接测试它们，这就足够了。

## 单一用途功能

这是一个很简单的概念，没什么特别的，只是一个用途单一而不是多个的函数。

我们的减速器是一个带有巨大`switch`的“单片”功能。它有几个问题，如我们上面所列。

我们将把它分成更小的、单一目的的、更易管理的功能。让我们深入研究一下。

## 重构

现在我们已经对我们的缩减器进行了测试，我们可以放心地重构它们，或者用不同的实现交换实现，而不会破坏其余的代码。

让我们来解决我们为示例列出的问题。

使用 redux 文档上提出的策略[，我们可以对我们的 reducers 做一些重构，使它们看起来像这样:](https://redux.js.org/recipes/structuringreducers/refactoringreducersexample) 

```
// e.g. src/actions/users.js
export const USER_ADD = "USER_ADD";
export const USER_REMOVE = "USER_REMOVE";

// e.g. src/reducers/usersById.js
export function usersById(state = {}, action) {
  switch (action.type) {
    case USER_ADD: return addUser(state, action.user);
    case USER_REMOVE: return removeUser(state, action.id);

    default: return state;
  }
}

function addUser(state, user) {
  return {
    ...state,
    [user.id]: user,
  };
}

function removeUser(state, id) {
  const newState = { ...state };
  delete newState[id];
  return newState;
} 
```

Enter fullscreen mode Exit fullscreen mode

重构应该非常简单，只需将每个`case`的常规 reducer 逻辑提取到函数中。

让我们将这种方法与我们在前面的例子中看到的问题进行对比:

*   每个函数都非常简短，这使得它们更容易阅读和测试。
*   每个函数都有自己的作用域。
*   您可以为每个函数添加一个简单的 docstring。它应该很容易保持简短，因为它不应该做太多，特别是与之前的“全部完成”方法相比。
*   对于每个函数，您期望从`action`中获得什么数据是显而易见的。

## 运行一些代码

如果你想看看我们的例子，有更多的测试和几个额外的行动，去[这个报告](https://github.com/ivanalejandro0/test-refactor-redux-reducers)。

或者，由于 CodeSandbox，您可以现场使用它:

[![Edit test-refactor-redux-reducers](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/github/ivanalejandro0/test-refactor-redux-reducers/tree/master/?module=%2Fsrc%2Freducers%2FusersById.test.js&previewwindow=tests)

## 免责声明

我为 SpiderOak 工程博客写了这篇文章，它于 2019 年 1 月 18 日发表。
[https://engineering . spider oak . com/redux-and-single-purpose-functions/](https://engineering.spideroak.com/redux-and-single-purpose-functions/)

原帖授权为: [Creative Commons BY-NC-ND](https://creativecommons.org/licenses/by-nc-nd/4.0/)