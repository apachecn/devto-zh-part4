# 下一个输入集中在反应本地

> 原文：<https://dev.to/rvznkmrllh/next-input-focus-on-react-native-2cb9>

浏览网页时，您必须熟悉使用 Tab 键聚焦到下一个元素。这是一个简单的技巧，让你的浏览体验更容易。最近，我正在用 React Native 制作一个应用程序，它有一个一次性密码(OTP)表单。我打算给同样的体验，当用户插入一个数字，应用程序将集中到下一个`TextInput`。

这可以通过使用`ref`属性来完成。Ref 允许我们访问将要修改的 React 组件或 DOM 节点。基本上，我们要做的是

1.  创建一个由`React.createRef`组成的数组
2.  向输入添加`ref`属性
3.  创建一个函数来处理来自输入的`onChange`
4.  聚焦找到的下一个输入

```
class OtpForm extends React.Component {
    constructor(props){
        super(props)
        this.inputRefs = [
            React.createRef(),
            React.createRef(),
            React.createRef(),
            React.createRef()
        ]
        // or 
        // this.inputRefs = Array(4).fill(React.createRef())
    }
} 
```

现在上面我们已经有了一个数组`React.createRef`。我们只需要 4 位数的动态口令和每个输入的一位数。所以我们要将这些元素添加到我们的`render`方法中。

```
...
   render(){
     return (
       <View>
         <Text>Enter OTP</Text>
         <View style={{ flexDirection: 'row' }}>
           <TextInput maxLength="1" />
           <TextInput maxLength="1" />
           <TextInput maxLength="1" />
           <TextInput maxLength="1" />
         </View>
       </View>
     )
   }
... 
```

请注意，我们没有将`ref`属性添加到任何`TextInput`中。为了更容易，我们将重写那些代码，根据我们的`this.inputRefs`长度来渲染`TextInput`，然后我们添加`ref`属性。

```
...
   render(){
     return (
       <View>
         <Text>Enter OTP</Text>
         <View style={{ flexDirection: 'row' }}>
          {
            this.inputRefs.map((k, idx) => (
              <TextInput ref={r => this.inputRefs[idx] =  r} maxLength="1" />
            ))
          }
         </View>
       </View>
     )
   }
... 
```

所以基本上，我们使用`.map`操作符遍历`this.inputRefs`，然后根据索引为每个`TextInput`定义`ref`属性。

现在我们已经有了连接到每个`TextInput`的`this.inputRefs`数组元素。下一件事是处理用户做出的更改。因为我们的`TextInput`只包含 1 位数，所以我们只需要处理`onChange`事件。我们将创建一个函数`_goNextAfterEdit`,将焦点放在下一个输入元素上。

```
class OtpForm extends React.Component {
    constructor(props){
        super(props)
        this.inputRefs = [
            React.createRef(),
            React.createRef(),
            React.createRef(),
            React.createRef()
        ]
    }

    // Pass the index of the current TextInput
    // then plus that with +1 
    _goNextAfterEdit(index){
        this.inputRefs[index+1].focus()
    }

    render(){
     return (
       <View>
         <Text>Enter OTP</Text>
         <View style={{ flexDirection: 'row' }}>
          {
            this.inputRefs.map((k, idx) => (
              <TextInput onChange={() => this._goNextAfterEdit(idx)} ref={r => this.inputRefs[idx] =  r} maxLength="1" />
            ))
          }
         </View>
       </View>
     )
   }
} 
```

就是这样！

基本上，这些代码满足了我们目的。但是您可以添加额外的代码来避免错误或改进功能，例如:

#### 验证最后一个`TextInput`

因为我们基于`this.inputRefs`的长度来呈现我们的`TextInput`，所以当用户编辑表单中提供的最后一个`TextInput`时会有一个 bug。

```
...
   _goNextAfterEdit(index){
     if(index < this.inputRefs.length) {
       this.inputRefs[index+1].focus()
     }
   }
... 
```

#### 处理> 1 位数字文本输入

当有接收字的`TextInput`或 1 位字符的>时，您可能不想使用`onChange`事件。我建议使用`onSubmitEditing`,当用户从键盘上点击提交按钮时就会触发它。

#### 指定输入类型

在 React Native 中，指定用户编辑`TextInput`时显示的键盘。您可以通过添加`keyboardType`属性来指定它。关于键盘类型的完整列表，[https://Facebook . github . io/react-native/docs/textinput . html # keyboard type](React%20Native%20TextInput%20Keyboard%20Type)