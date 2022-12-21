# component 将在使用 Props 时收到 props 警告。

> 原文：<https://dev.to/vinay2477/componentwillreceiveprops-warnings-while-using-props-415h>

//子组件

从“react”导入 React，{ Component }；
从‘react-native’导入{
样式表、
视图、
文本、
文本输入、
touchable opacity
}；

导出默认类表单扩展组件{
render(){
return(

underlineColorAndroid = ' rgba(0，0，0，0)'
placeholder = " Email "
placeholder text color = " # ffffff "
/>
underlineColorAndroid = ' rgba(0，0，0，0)'
placeholder = " Password "
securetexentry = { true }
placeholder text color = " # 1

```
 <TouchableOpacity style={styles.button}>
                <Text style={styles.buttonText}> {this.props.type} </Text>
            </TouchableOpacity>
        </View>
    )
} 
```

}

const styles = style sheet . create({
container:{
flex grow:1，
justifyContent: 'center '，
alignItems: 'center'
}，

```
inputBox: {
    width: 300,
    backgroundColor: 'rgba(255, 255,255,0.2)',
    borderRadius: 25,
    paddingHorizontal: 16,
    fontSize: 16,
    color: '#ffffff',
    marginVertical: 10
},
button: {
    width: 300,
    backgroundColor: '#1c313a',
    borderRadius: 25,
    marginVertical: 10,
    paddingVertical: 13
},
buttonText: {
    fontSize: 16,
    fontWeight: '500',
    color: '#ffffff',
    textAlign: 'center'
} 
```

});

//父组件

从“react”导入 React，{ Component }；
从‘react-native’导入{
safearaveview，
样式表，
ScrollView，
View，
Text，
StatusBar，
}；

从'导入徽标'../组件/徽标'；
从'导入表单../组件/表单'；

导出默认类 LoginPage 扩展组件{

render(){
return(

创建账户
注册

)
}
}

const styles = style sheet . create({
container:{
background color:' # b0be C5 '，
flexGrow: 1，
alignItems: 'center '，
justifyContent: 'flex-end '，
}，
signupTextCont:{
flex grow:1，
alignItems: 'flex-end '，
justifyContent: 'center '，
paddingVertical: 16，
flexDirection: 'row' 【T12
paddingVertical: 13
}，
button text:{
font size:16，
fontWeight: '500 '，
color: '#ffffff '，
textAlign: 'center'
}，
})；

当我试图访问{this.props.type}时，我得到很多警告。

//警告:

警告:componentWillReceiveProps 已被弃用，将在下一个主要版本中删除。请改用静态 getDerivedStateFromProps。

警告:componentWillMount 已被弃用，将在下一个主要版本中删除。请改用 componentDidMount。作为一种临时解决方法，您可以重命名为 UNSAFE_componentWillMount。

在模拟器中，所有的组件都消失了，我将被留在一个空屏幕上。请让我知道我哪里做错了。提前感谢。