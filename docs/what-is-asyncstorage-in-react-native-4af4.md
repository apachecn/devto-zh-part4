# React Native 中的 AsyncStorage 是什么？

> 原文：<https://dev.to/amanhimself/what-is-asyncstorage-in-react-native-4af4>

[封面图片归功于 Unsplash 和 Tom Pumford](https://unsplash.com/photos/v8ppsvUTwGE)

`AsyncStorage`是 React 本机应用程序中使用的一个简单的异步键值对。它用于各种场景，但主要是在您的应用程序不使用任何云服务时，或者当您希望在应用程序中实现需要数据存储的功能时，存储数据。

它在 React 本地应用程序中全局运行，并有自己的限制。作为一名 React 本地开发人员，您必须知道这些限制是什么。`AsyncStorage` API 的一个限制是，在 Android 上，数据库的大小被默认设置为 6MB 的限制。另外，`AsyncStorage`存储基于 SQLite。

因此，记住 [SQLite 的限制](https://www.sqlite.org/limits.html)也很重要。此外，很难以键值对的形式存储复杂和嵌套的数据结构。了解这些限制将有助于您在开发移动应用程序时选择最佳解决方案。

根据 [React Native 的官方文档](https://facebook.github.io/react-native/docs/asyncstorage):

> 在 iOS 上，AsyncStorage 由本机代码支持，它将小值存储在序列化的字典中，将较大的值存储在单独的文件中。在 Android 上，AsyncStorage 将根据可用的内容使用 RocksDB 或 SQLite。

## 创建世博 app

首先，您需要生成一个新的 Expo 项目。这可以通过打开一个终端窗口，导航到开发项目的合适位置，并按描述的顺序运行以下命令来完成。

```
expo init offline-app

# navigate inside the app folder
cd offline-app 
```

Enter fullscreen mode Exit fullscreen mode

注意:yarn 目前被用作包管理器。也可以用 npm 代替纱线。

## 利用异步存储 API

在本节中，让我们构建一个应用程序，它将一个值保存到`AsyncStorage`中，并从客户端 React 本机应用程序的存储中获取该值。这将帮助您学习如何使用存储 API 编写基本操作。最后，您将了解如何彻底清除存储。

打开`App.js`文件并添加下面的代码片段。首先从 React 本地 API 导入必要的组件。这里最重要的是`AsyncStorage`。之后，定义一个名为`STORAGE_KEY`的变量。该变量将用于使用`AsyncStorage` API 存储和检索存储的数据。

可以把它想象成被存储的值的标识符或者键-值对中的键的名称。因为您现在只存储一个值，所以只需要一个键。

```
import React from 'react'
import { StyleSheet, Text, View, TextInput, AsyncStorage, TouchableOpacity } from 'react-native'

const STORAGE_KEY = '@save_name' 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们用两个空字符串定义一个初始状态。它们将用于保存用户输入的值，然后检索该值以将其显示在应用程序屏幕上。在定义初始状态之后，将会有一个生命周期方法，当应用程序第一次启动时，或者当应用程序组件呈现时，该方法将加载数据。

```
class App extends React.Component {
  state = {
    text: '',
    name: ''
  }

  componentDidMount() {
    this.retrieveData()
  }

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，请注意,`App`组件实际上是一个类组件，而不是样板 Expo 应用程序附带的默认功能组件。

## 读取数据

有三种异步方法有助于存储数据、检索数据和清除存储的应用程序数据。这些方法中的每一个都将利用来自`AsyncStorage` API 的适当的 API 方法。AsyncStorage API 中的每个方法都是基于承诺的；因此，让我们使用`async/await`语法来遵循最佳实践。

```
retrieveData = async () => {
    try {
      const name = await AsyncStorage.getItem(STORAGE_KEY)

      if (name !== null) {
        this.setState({ name })
      }
    } catch (e) {
      alert('Failed to load name.')
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，方法的名称暗示了它将在应用程序中做什么。`retrieveData`方法是从存储中获取数据(如果存在的话)。它使用您先前在类函数组件之外定义的相同标识符。它利用状态对象名称中的参数。

注意，这个方法中有一个 if 条件。该条件确保仅当 name 变量的值存在时才提取数据。这个方法也使用了`try/catch`，因为它们是用现代`async/await`语法编写函数的一部分。最后，这个方法在生命周期方法中被调用。

## 保存数据

下一个函数将保存数据。在下面的代码片段中，您会发现它使用了一个参数名，如果成功，它就是存储的值。保存输入数据时，将显示一条警告消息。

```
save = async name => {
    try {
      await AsyncStorage.setItem(STORAGE_KEY, name)
      alert('Data successfully saved!')
      this.setState({ name })
    } catch (e) {
      alert('Failed to save name.')
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 清除一切

您将从`AsyncStorage` API 中使用的最后一个方法叫做`clear()`。这将删除之前保存的所有内容。如果您只想从存储中删除特定项目，不建议直接使用此方法。为此，API 提供了像`removeItem`或`multiRemove`这样的方法。你可以在官方文档[这里](https://facebook.github.io/react-native/docs/asyncstorage#clear)或者以后构建 Todolist 应用程序时读到更多关于它们的内容。

```
removeEverything = async () => {
    try {
      await AsyncStorage.clear()
      alert('Storage successfully cleared!')
    } catch (e) {
      alert('Failed to clear the async storage.')
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

当所有内容都从存储中清除时，这个代码片段将在设备屏幕上抛出一个警告框。

## 完成 App

最后两种方法将用于创建受控输入。

```
onChangeText = text => this.setState({ text })

onSubmitEditing = () => {
    const onSave = this.save
    const { text } = this.state

    if (!text) return

    onSave(text)
    this.setState({ text: '' })
} 
```

Enter fullscreen mode Exit fullscreen mode

之后，添加`render`方法的代码片段，然后是每个 UI 组件的样式。最后，不要忘记导出`App`组件，以便它可以在模拟器或真实设备上运行。

```
render() {
        const { text, name } = this.state
        return (
            <View style={styles.container}>
                <TextInput
                    style={styles.input}
                    value={text}
                    placeholder='Type your name, hit enter, and refresh'
                    onChangeText={this.onChangeText}
                    onSubmitEditing={this.onSubmitEditing}
                />
                <Text style={styles.text}>Hello {name}!</Text>
                <TouchableOpacity onPress={this.removeEverything} style={styles.button}>
                    <Text style={styles.buttonText}>Clear Storage</Text>
                </TouchableOpacity>
            </View>
        )
    }
} // class component App ends here

const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'center'
    },
    text: {
        fontSize: 20,
        padding: 10,
        backgroundColor: '#00ADCF'
    },
    input: {
        padding: 15,
        height: 50,
        borderBottomWidth: 1,
        borderBottomColor: '#333',
        margin: 10
    },
    button: {
        margin: 10,
        padding: 10,
        backgroundColor: '#FF851B'
    },
    buttonText: {
        fontSize: 14,
        color: '#fff'
    }
})

export default App 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 App

现在运行应用程序，进入终端窗口并执行命令`expo start`。之后，您将在模拟器上看到以下屏幕。

[![ss1](img/051f8cf625c32cc018570c1f9bc2dbc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XNlKmle2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/350/1%2A92vU3B3f_FmUCXk8xKwtSg.png)

因为现在没有存储数据，所以单词`Hello`后面的文本是空的。使用输入字段保存字符串或名称或您想要的任何内容，然后按 enter 键。您将获得以下输出。无论您输入了什么，它都会显示在单词`Hello`的旁边。

[![ss2](img/6dd06b93355bbd8510bb1d6eecf17765.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bqiulkj1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/350/1%2AtwgilQew9SwvBq2YnEbIqg.png)

即使您刷新 Expo 客户端，存储的值也不会消失。只有当按下显示`Clear Storage`的`Hello`语句下方的按钮时，存储的值才会被删除。

[![ss3](img/561b0d526cc0eaf5ae5695a3b084d16c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6sLLnEWH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/350/1%2A6yeaeFLns1JwU0wyeqMptQ.png)

在清空存储之后，刷新 Expo 客户端以获得以下输出。

[![ss4](img/5e7067417a0891be0be8262dd814e83a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--thxhbEUn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/350/1%2ACRhOwbo1d-iTUOEb0v-MAQ.png)

## 结论

这就完成了关于使用`AsyncStorage` API 保存和获取数据的部分。

如果你想了解更多关于在实时 React 本地应用中使用`AsynStorage API`的信息，请**点击** 了解如何构建一个完整的 Todolist 应用[。在这篇文章中，你将使用](https://heartbeat.fritz.ai/building-offline-react-native-apps-with-asyncstorage-dcb4b0657f93)[原生基础](https://nativebase.io/) UI 组件库。

以下是 **Todolist post** 的简称 **TLDR** :

*   添加导航
*   创建浮动动作按钮(FAB)
*   在两个屏幕之间导航
*   自定义标题组件
*   使用 FlatList 呈现项目列表
*   使用 AsyncStorage API 读取数据
*   添加待办事项列表项目
*   删除待办事项列表项目
*   完成后将项目标记为选中或取消选中
*   使用导航在不同屏幕之间传递数据
*   显示每个待办事项列表项目
*   奖金部分:添加细分市场

这是一个你将在 Todolist 帖子中创建的小演示:

[![ss5](img/e65616b082c14ea7291ebe651de0edbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i9iy9D0c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://miro.medium.com/max/313/1%2AFBBSWT3Xztc0G9wAGnz1yA.gif)

* * *

我经常在 Nodejs、Reactjs 和 React Native 上写。你可以通过[aman his . dev](https://amanhimself.dev/blog)来访问我，或者你可以订阅[我的每周简讯](https://tinyletter.com/amanhimself)，直接在你的收件箱中接收所有关于新帖子和内容的更新💌。