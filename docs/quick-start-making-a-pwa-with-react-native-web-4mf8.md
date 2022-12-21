# react-native-web 教程快速入门

> 原文：<https://dev.to/regalstreak/quick-start-making-a-pwa-with-react-native-web-4mf8>

所以在没有找到更新的或合适的 react-native-web 入门指南后，我决定创建这个。

# **TLDR**

为了让事情进展顺利，

`create-react-app my-app`
T1】

现在在`src/index.web.js`(如果不存在就创建)

```
// index.web.js

import App from './App';
import { AppRegistry } from 'react-native';
import * as serviceWorker from './serviceWorker';

// register the app
AppRegistry.registerComponent('App', () => App);

AppRegistry.runApplication('App', {
    initialProps: {},
    rootTag: document.getElementById('root')
});

serviceWorker.unregister(); 
```

然后删除`src/App.js`并再次创建`src/App.js`

```
// App.js
import React from 'react';
import { Text, View, StyleSheet } from 'react-native';

// react-native-web is aliased to react-native automatically by create-react-app

export default class App extends React.Component {
    render() {
        return (
            <View style={styles.container}>
                <Text style={styles.text}>
                    Welcome to react-native-web
                </Text>
            </View>
        )
    }
}

const styles = StyleSheet.create({
    container: {
        alignItems: "center",
        height: "100vh",
        backgroundColor: "black",
        justifyContent: "center"
    },
    text: {
        fontSize: 40,
        color: "silver"
    }
}) 
```

然后只需`yarn start`，你应该在`localhost:3000`的浏览器上查看

# **但是，react-native-web 是什么？**

根据`necolas`的 github，

**React Native for Web** 使得使用 **React DOM** 在 Web 上运行 React 本地组件和 API 成为可能。查看运行在 web 上的 React 本地示例的现场演示。

**一次编写，随处呈现**:与现有的 React DOM 组件互操作，并与大多数 React 原生 API 兼容。您可以为 native 和 web 开发新组件，而无需重写现有代码。React Native for Web 还可以使用 Node.js 在服务器上呈现 HTML 和关键 CSS。

谁在生产 web 应用中使用 React Native？`Twitter, Major League Soccer, Flipkart, Uber, The Times, DataCamp`。