# React with Redux 和 Material UI 中的全局 Snackbars

> 原文：<https://dev.to/cpow/global-snackbars-in-react-with-redux-and-material-ui-3pej>

我正在做一个关于 Redux 和 Material UI 的项目。我喜欢 snackbars 的外观，我想创建一个简单的系统来在我的应用程序中显示它们。如果你曾经使用过 Rails，想象一下你显示`flash`消息的方式。我想要一些简单的东西，就像那个惊人的 Rails 特性。

### 关于零食吧

来自[材料设计指南](https://material.io/components/snackbars/):“snack bars 在屏幕底部提供关于 app 进程的简短消息”。

☝ ️guidelines 还指定了你应该实现 snackbars 的方式:“Snackbars 通知用户一个应用已经执行或者将要执行的进程。它们暂时出现在屏幕底部。它们不应该中断用户体验，也不需要用户输入消失”。这是向用户展示他们在你的应用程序上完成一个成功动作的完美用例！

### 材质界面中的零食条

[Material UI](https://material-ui.com) 是基于 Material IO 准则的 React 的组件框架。他们有一个[优秀的 snackbar 组件](https://material-ui.com/components/snackbars/)，我们可以利用它向用户显示 snackbar 通知。

### 我的申请中的问题

我想在整个应用程序中使用 snackbars。用户可以在很多地方采取导致 snackbar 出现的操作。我不想为每个需要显示的组件实例化一个新的 Snackbar 组件。下面是我的应用程序中一个零食条的快速示例:

### 解决方案:Redux！

谢天谢地，我用的是 React，带 Redux。我喜欢尽可能多地使用 Redux 来解决我的状态问题。我喜欢 redux 的一个模式，我称之为“redux ui”模式。基本上我使用 redux 来存储至少一些我在整个应用程序中的 UI 状态。这使得全局 UI 更改(想想用户在他们的应用程序中指定黑暗模式)很容易存储并在整个应用程序中传播。

*   **让我们从创建我们的 reducers 开始，这将给出我们显示 snackbars 的状态的形状:**

```
// reducers/uiReducer.js
const uiReducer = (state = {}, action) => {
  switch (action.type) {
    case "SNACKBAR_SUCCESS":
      return {
        ...state,
        successSnackbarOpen: true,
        successSnackbarMessage: action.message
      };
    case "SNACKBAR_CLEAR":
      return {
        ...state,
        successSnackbarOpen: false,
        errorSnackbarOpen: false,
        infoSnackbarOpen: false
      };
    default:
      return state;
  }
};

export default uiReducer; 
```

☝️将为我们提供一些结构来显示一个“成功”的 snackbar，同时清除我们可能想要创建的所有可能的 snack bar(包括错误和信息...以防万一)。

*   让我们创建一些动作创建者来运行 reducers。

```
// actions/snackbarActions.js
export const showSuccessSnackbar = message => {
  return dispatch => {
    dispatch({ type: "SNACKBAR_SUCCESS", message });
  };
};

export const clearSnackbar = () => {
  return dispatch => {
    dispatch({ type: "SNACKBAR_CLEAR" });
  };
}; 
```

*   **创建一个使用`clearSnackbar()`函数清除自身的 snackbar】**

```
// components/SuccessSnackbar.js or whatever you wanna call it
import { useDispatch, useSelector } from "react-redux";
import Snackbar from "@material-ui/core/Snackbar";
import IconButton from "@material-ui/core/IconButton";
import { Icon } from "@material-ui/core";
import { clearSnackbar } from "../../store/actions/snackbarActions";

export default function SuccessSnackbar() {
  const dispatch = useDispatch();

  const { successSnackbarMessage, successSnackbarOpen } = useSelector(
    state => state.ui
  );

  function handleClose() {
    dispatch(clearSnackbar());
  }

  return (
    <Snackbar
      anchorOrigin={{
        vertical: "bottom",
        horizontal: "left"
      }}
      open={successSnackbarOpen}
      autoHideDuration={4000}
      onClose={handleClose}
      aria-describedby="client-snackbar"
      message={
        <span id="client-snackbar">
          <Icon>check_circle</Icon>
          {successSnackbarMessage}
        </span>
      }
      action={[
        <IconButton
          key="close"
          aria-label="close"
          color="inherit"
          onClick={handleClose}
        >
          <Icon>close</Icon>
        </IconButton>
      ]}
    />
  );
} 
```

在☝️，注意当 snackbar 调用`handleClose`时，我们使用的是`clearSnackbar()`函数。注意，`handleClose`是在指定的超时之后调用的，所以 snackbar 会在一定的时间后自动调用这个函数。

*   **将这个新的 snackbar 组件添加到您的`App.js`文件中。这允许你使用 redux:** 在应用程序的任何地方显示一个 snackbar

```
return (
  <div>
    <SuccessSnackbar />
    <Router>
      //App stuff goes in here
    </Router>
  </div> ); 
```

*   **每当您想要显示一个成功的 snackbar 时，用您的消息发送`showSuccessSnackbar()`函数。**

```
dispatch(showSuccessSnackbar("Success!")); 
```

### 结论

成功！现在，您可以使用 Redux 在 React 应用程序的任何位置显示 snackbar。你真是个聪明的开发者，不是吗？😄

我发现当您想要在 React 应用程序中全局显示 snackbars、alerts 或任何内容时，这是一个有趣的模式。它足够简单，任何开发人员都可以理解，并且可以扩展以添加任何东西:警告、snackbars、消息等...