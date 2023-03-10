# React 🥰中处理警告对话框的最佳方式

> 原文：<https://dev.to/dmtrkovalenko/the-neatest-way-to-handle-alert-dialogs-in-react-1aoe>

*阅读时间— 5 分钟* ☕️

你好。懒惰的开发人员在这里，我们将讨论如何在 react without tears 中处理对话框警告😢。如果你厌倦了大量的复制粘贴，只是为了创建一个新的怪异的一个问题的模态对话框——准备你的咖啡，我们开始了。

## 目标

我们希望为显示警报提供最简洁的解决方案。非常类似于我们在浏览器中使用的本地`alert`功能。

```
const isConfirmed = alert("Are you sure you want to remove this burrito?");

if (isConfirmed) {
  await api.deleteThisAwfulBurrito();
} 
```

Enter fullscreen mode Exit fullscreen mode

## [先睹为快](#sneak-peek)

最终我们会得到这样的东西。

```
const YourAwesomeComponent = () => {
  const confirm = useConfirmation()

  confirm({
    variant: "danger",
    title: "Are you sure you want to remove this burrito?",
    description: "If you will remove this burrito you will regret it 😡!!"
  }).then(() => {
    api.deleteThisAwfulBurrito();
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

感兴趣吗？让我们写一些代码。

首先，我们需要从实际创建模态对话框开始。这只是一个用❤️和 [material-ui](https://material-ui.com/components/dialogs/#dialogs)
构建的简单警告对话框

```
import {
  Button,
  Dialog,
  DialogTitle,
  DialogContent,
  DialogContentText,
  DialogActions,
} from "@material-ui/core";

export const ConfirmationDialog = ({
  open,
  title,
  variant,
  description,
  onSubmit,
  onClose
}) => {
  return (
    <Dialog open={open}>
      <DialogTitle>{title}</DialogTitle>
      <DialogContent>
        <DialogContentText>{description}</DialogContentText>
      </DialogContent>
      <DialogActions>
        <Button color="primary" onClick={onSubmit}>
          YES, I AGREE
        </Button>
        <Button color="primary" onClick={onClose} autoFocus>
          CANCEL
        </Button>
      </DialogActions>
    </Dialog>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

好的，但是我们将如何采用它来动态地工作呢？这是一件有趣的事情。如果用户只能同时看到**一个**警告对话框，为什么我们需要**很多对话框**给每个组件？

> 如果您在另一个报警对话框上显示一个报警对话框...你可能需要重新考虑你申请中的 UX 部分。
> [![No god please no image](img/f76a0fb8e5fdd4c51af95d4ea6567aac.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--c8n99gLd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/dmtrKovalenimg/blob/master/no_god_pls_no.gif%3Fraw%3Dtrue)

所以我们开始吧。我们所需要的只是在应用程序的根处只呈现 **1 顶级**模态，并在需要时显示它。我们将使用 react 钩子的能力来使它看起来更优雅。

## 包裹上下文

让我们创建一个新的上下文实例，并用它包装组件树。此外，创建一个简单的状态，它将保存警报的当前显示选项(如标题、描述和您需要的一切)。

```
interface ConfirmationOptions {
  title: string;
  description: string;
}

const ConfirmationServiceContext = React.createContext<
  // we will pass the openning dialog function directly to consumers
  (options: ConfirmationOptions) => Promise<void>
>(Promise.reject);

export const ConfirmationServiceProvider= ({ children }) => {
  const [
    confirmationState,
    setConfirmationState
  ] = React.useState<ConfirmationOptions | null>(null);

  const openConfirmation = (options: ConfirmationOptions) => {
    setConfirmationState(options);
    return Promise.resolve()
  };

  return (
    <>
      <ConfirmationServiceContext.Provider
        value={openConfirmation}
        children={children}
      />

      <Dialog open={Boolean(confirmationState)} {...confirmationState} />
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，一旦我们连接任何消费者并调用提供的函数，我们的对话框就会打开。

## 解决确认

现在我们需要以某种方式处理关闭对话并从消费者那里获得回调。这里使用的是基于`Promise`的 API，但是使用回调方式也是可行的。在本例中，一旦用户接受或取消提醒，您的等待承诺将被解决或拒绝。

为此，我们需要保存`Promise`的解析函数，并在适当的用户操作中调用它们。React 的`ref`是最好的地方。

```
 const awaitingPromiseRef = React.useRef<{
    resolve: () => void;
    reject: () => void;
  }>();

  const openConfirmation = (options: ConfirmationOptions) => {
    setConfirmationState(options);
    return new Promise((resolve, reject) => {
      // save the promise result to the ref
      awaitingPromiseRef.current = { resolve, reject };
    });
  };

  const handleClose = () => {
    // Mostly always you don't need to handle canceling of alert dialog
    // So shutting up the unhandledPromiseRejection errors 
    if (confirmationState.catchOnCancel && awaitingPromiseRef.current) {
      awaitingPromiseRef.current.reject();
    }

    setConfirmationState(null);
  };

  const handleSubmit = () => {
    if (awaitingPromiseRef.current) {
      awaitingPromiseRef.current.resolve();
    }

    setConfirmationState(null);
  }; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们的对话机快好了！还有一件事——创建一个自定义钩子以获得更好的可读性

```
export const useConfirmationService = () =>
  React.useContext(ConfirmationServiceContext); 
```

Enter fullscreen mode Exit fullscreen mode

### 定制

您可以通过传递附加的`variant`属性来轻松定制对话框内容。只要把它加到`ConfirmationOptions`

```
export interface ConfirmationOptions {
  variant: "danger" | "info";
  title: string;
  description: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

并根据您的意愿呈现不同的对话内容。

```
 <DialogActions>
    {variant === "danger" && (
      <>
        <Button color="primary" onClick={onSubmit}>
          Yes, I agree
        </Button>
        <Button color="primary" onClick={onClose} autoFocus>
          CANCEL
        </Button>
      </>
    )}

    {variant === "info" && (
      <Button color="primary" onClick={onSubmit}>
        OK
      </Button>
    )}
  </DialogActions> 
```

Enter fullscreen mode Exit fullscreen mode

## 准备好了吗？！

[![Are you ready kids?!](img/652ef6f1a3652a0d94533fcb8d3cb63f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PNbrn7b_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/dmtrKovalenimg/blob/master/hey%2520children.jpg%3Fraw%3Dtrue)

这是最后一个工作示例。如果你愿意，可以随意窃取`ConfirmationService.tsx`文件的实现。这是我们谈论的非常独立和孤立的逻辑。

[https://codesandbox.io/embed/neat-dialogs-3h5ou](https://codesandbox.io/embed/neat-dialogs-3h5ou)

> 附注:在这篇文章的制作过程中，没有墨西哥卷饼受到伤害