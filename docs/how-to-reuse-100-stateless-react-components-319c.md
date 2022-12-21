# 如何复用 100%无状态的 React 组件？

> 原文：<https://dev.to/wongjiahau/how-to-reuse-100-stateless-react-components-319c>

注意:这篇文章还没有完成。

*这篇文章的灵感来自榆树建筑*

在继续阅读之前，请允许我澄清一些假设:
(1)表演无关紧要
(2)道具只有 2 种，分别是*视角道具*和*动作道具*。
(3)我们还有一堆动作和减速器。

下面是一个例子，说明在处理需要重用的纯无状态组件时，我们如何减少重复的代码和样板文件。

基本上我是这样做的:
(1)将组件的视图道具声明为接口
(2)为视图道具声明一个初始化函数
(3)将组件的动作道具声明为接口(注意:每个动作都要返回各自的视图)
(4)为动作道具声明初始化函数

## 例 1: SignUpDialog

假设我们想要构建一个可以被很多页面重用的注册对话框。

```
export interface SignUpDialogView {
  username: string,
  password: string,
  open: boolean
}

export const initSignUpDialogView = (): SignUpDialogView => ({
  username: '',
  password: '',
  open: false
})

export interface SignUpDialogActions {
  onUsernameChange:    (e: React.ChangeEvent<HTMLInputElement>) => SignUpDialogView,
  onPasswordChange:    (e: React.ChangeEvent<HTMLInputElement>) => SignUpDialogView,
  onSignUpButtonClick: () => SignUpDialogView,
  onCancelButtonClick: () => SignUpDialogView
}

export const initSignUpDialogActions = (
  view: SignUpDialogView, 
  update: (view: SignUpDialogView) => void 
) => ({
  onUsernameChange:    e  => update({...view, username: e.target.value}),
  onPasswordChange:    e  => update({...view, password: e.target.value}),
  onSignUpButtonClick: () => update({...view, open: false}),
  onCancelButtonClick: () => update({...view, open: false})
})

export const SignUpDialog: React.FC<{
  view: SignUpDialogView,
  actions: SignUpDialogActions,
}> = (props) => {
  const {view, actions} = props
  return (view.open &&
    <div>
      Username: 
      <input value={view.username} onChange={actions.onUsernameChange}/>
      Password: 
      <input value={view.password} onChange={actions.onPasswordChange}/>
      <button onClick={actions.onSignUpButtonClick}>Sign Up</button>
      <button onClick={actions.onCancelButtonClick}>Cancel</button>
    </div>
  )
} 
```

假设我们想在 BuyPage 中使用 SignUpDialog(它是有状态的):

```
export class BuyPage extends React.Component<{}, {
  signUpDialogView: SignUpDialogView
}> {
  constructor(props) {
    super(props)
    this.state = {
      signUpDialogView: initSignUpDialogView()
    }
  }
  render() {
    const {signUpDialogView} = this.state
    return (
      <div>
       Buy something
       <SignUpDialog 
         views={signUpDialogView}
         actions={initSignUpDialogActions(
           signUpDialogView, 
           signUpDialogView => this.setState({signUpDialogView})
         )}
         />
      </div>
    )
  }

} 
```

通过这样做，您将拥有 100%的可定制性，这是使用有状态组件不可能实现的。

怎么会？我们可以使用 spread 运算符实现可定制性。

假设我们想要大写用户名:

```
<SignUpDialog 
  views={{
      ...signUpDialogView,
      username: signUpDialogView.username.toUpperCase()
  }}
  actions={initSignUpDialogActions(
    signUpDialogView, 
    signUpDialogView => this.setState({signUpDialogView})
  )}
/> 
```

* * *

## 例 2:日期选择器

现在，让我们看另一个更现实的例子，假设我们想创建一个可供他人使用的 DatePicker。

这一次，我将省略实现细节，因为我只想强调这个概念。

同样，我们将遵循 4 个步骤。

```
// Declare view props
export interface DatePickerView {
  currentDay: number,
  currentMonth: number,
  currentYear: number
}

// Declare action props
export interface DatePickerActions {
  chooseDate: (date: Date) => DatePickerView
  changeMonth: (month: number) => DatePickerView
}

// Declare init view function
export const initDatePickerView = (): DatePickerView => ({
  // implementation . . .
})

// Declare init action props
export interface initDatePickerActions = (
  view: DatePickerView,
  update: (view: DatePickerView) => void
): DatePickerActions => ({
  // implementation . . .
}) 
```

现在，这是组件:

```
export const DatePickerDialog: React.FC<{
  view: DatePickerView,
  actions: DatePickerActions,
  update: (view: DatePickerView) => void
}> = (props) => {
  // implementation detail
} 
```

然后，当你想在 XXXCompnent 中使用它的时候:

```
export class XXXComponent extends React.Component<{}, {
  datePickerView: DatePickerDialogView
}> {
   constructor(props) {
     super(props)
     this.state = {
       datePickerView: initDatePickerView()
     }
   }
   public render() {
     const {datePickerView} = this.state
     return (
       <DatePicker
         view={datePickerView}
         actions={initDatePickerActions(
           datePickerView,
           datePickerView => this.setState({datePickerView})
         )}
         />
     )
   }
} 
```

使用这种方法，`DatePicker`的用户甚至可以定制日历的导航体验，假设我们不想让用户访问七月:

```
export class XXXComponent extends React.Component<{}, {
  datePickerView: DatePickerDialogView
}> {
   constructor(props) {
     super(props)
     this.state = {
       datePickerView: initDatePickerView()
     }
   }
   public render() {
     const {datePickerView} = this.state
     const datePickerActions = initDatePickerActions(
       datePickerView,
       datePickerView => this.setState({datePickerView})
     )
     return (
       <DatePicker
         view={datePickerView}
         actions={{
           ...datePickerActions,
           changeMonth: month => 
             // If's its July, we make it to August
             datePickerActions.changeMonth(month === 7 ? 8 : month)

         }}
         />
     )
   }
} 
```