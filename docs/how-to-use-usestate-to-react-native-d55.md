# 如何在 React Native 中使用 React Hook useState？

> 原文：<https://dev.to/iamitkhatkar/how-to-use-usestate-to-react-native-d55>

我们将通过例子讨论如何在 Reactjs 中使用 useState 钩子。

让我们从一个例子开始，在这个例子中我们将使用老式的 react 状态管理。

```
import React from 'react';
import { Text, View, TextInput } from 'react-native';

export default class App extends React.Component {
  state = {
    name: '',
  }
  render() {
    return (
      <View>
        <TextInput 
          onChangeText={text => this.setState({name: text})}
          value={this.state.name}
          placeholder="enter here"
        />
      </View>
    );
  }
} 
```

所以我们在这里使用状态和

```
this.state.name 
```

并用
更新它

```
this.setState({name: text}) 
```

在继续之前，让我解释一下 useState 是如何工作的。React 的使用状态给了我们两个对象。

```
const [state, action] = useState("") 
```

第一个是实际状态，第二个是更新当前状态的操作，当我们初始化它时，我们在其中提供初始状态，例如，这里我们给出一个空字符串状态。

```
useState(''); 
```

现在让我们用 React 钩子做同样的例子

```
import React, { useState } from 'react';
import { Text, View, TextInput } from 'react-native';

const App = (props) => {
  const [name, setName] = useState("")

    return (
      <View style={styles.container}>
        <TextInput 
          onChangeText={text => setName(text)}
          value={name}
          placeholder="enter here"
        />
      </View>
    );
}

export default App; 
```

我们可以在没有关键字`this`的情况下访问状态，因为在上一个例子中，我们使用了一个函数组件，所以可以在没有关键字`this`的情况下在函数中访问变量。

```
// without React hooks 
value={this.state.name}

// with React hooks
value={name} 
```

为了更新状态，我们可以通过传递新的状态来调用第二个参数。

```
// without React hooks 
 onChangeText={text => this.setState({name: text})}

// with React hooks
onChangeText={text => setName(text)} 
```

这就是我们如何在 Reactjs 和 React Native 中使用 useState。我希望你喜欢它。如果我错过了什么或者你有相关的问题，你可以在评论中提问。