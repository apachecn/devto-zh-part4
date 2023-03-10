# 反应导航-一个简单的概述

> 原文：<https://dev.to/kpose/react-navigation-a-light-overview-fc3>

### **简介**

[![React Navigation](img/3d4f19139a5f45460538d88da350ee6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NumUgW5C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ku1w0hylc6xpyxpflyrl.png)

构建需要导航的 react 原生应用程序时，最重要的任务之一是为您的项目选择完美的导航库。React Navigation 是一个独立的库，允许开发人员轻松实现该功能。

在本教程结束时，您应该对 React Navigation 中的各种导航器以及如何实现它们有了很好的了解。

### **项目设置**

假设您安装了 Node 10+,您可以使用 npm 来安装 Expo CLI 命令行工具:

`npm install -g expo-cli`

然后运行以下命令创建一个名为“NavOptions”的新 React 原生项目:

`expo init NavOptions`

`cd NavOptions`
T1】

这将为您启动一个开发服务器。

下一步是在 react 本地项目中安装 react-navigation 库:

`yarn add react-navigation`

我们将探索三种导航选项:

*   堆栈导航
*   标签导航
*   抽屉导航

### **使用堆栈导航器**

首先让我们在根目录下创建一个新文件夹， **components** 。之后创建两个文件， *Homescreen.js* 和 *Aboutscreen.js* 。我们的项目文件夹应该如下图所示:
[![](img/e291850a1f1dd44e5ed3ac2b918887d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YXL__PtU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g65j9eswgjbrqbz39y0j.png)

将下面的代码块添加到 *Homescreen.js*

```
//With ES7 syntax, you could type 'rcn" to bootstrap a react native component skeleton
import React, { Component } from 'react'
import { Text, View, Button } from 'react-native'
import { createStackNavigator, createAppContainer } from 'react-navigation';

export default class Homescreen extends Component {
    render() {
        return (
            <View style={styles.container}>
                <Text> Welcome To Home Screen </Text>
                <Button
                    title = "Go to About Page"
                    onPress={() => this.props.navigation.navigate('About')}
                    />
            </View>
        )
    }
}

const styles = StyleSheet.create({
    container: {
      flex : 1,
      alignItems: 'center',
      justifyContent: 'center'
    },
  }); 
```

```
//Aboutscreen.js
import React, { Component } from 'react'
import { Text, View, Button } from 'react-native'
import { createStackNavigator, createAppContainer } from 'react-navigation';

export default class Aboutscreen extends Component {
  render() {
    return (
      <View style = {styles.container}>
        <Text> This is the About Screen. </Text>
      </View>
    )
  }
}

const styles = StyleSheet.create({
    container: {
      flex : 1,
      alignItems: 'center',
      justifyContent: 'center'
    },
  }); 
```

现在，我们也对 *App.js* 做一些修改。我们将从 *react-navigation* 中导入我们需要的东西，并在那里实现我们的导航。

在根 **App.js** 文件中实现我们的导航是有用的，因为从 **App.js** 导出的组件是 React 本机应用程序的入口点(或根组件)，其他所有组件都是其后代。

正如您将看到的，我们将在导航功能中封装所有其他组件。

```
//App.js
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { createStackNavigator, createAppContainer } from "react-navigation";

import HomeScreen from './components/HomeScreen';
import AboutScreen from './components/AboutScreen';

export default function App() {
  return (
    <AppContainer />
  );
}

const AppNavigator = createStackNavigator({
  Home : {
    screen : HomeScreen 
  },
  About: {
    screen: AboutScreen
  }
});

const AppContainer = createAppContainer(AppNavigator);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
}); 
```

createStackNavigator 为我们的应用程序提供了一种在屏幕之间转换的方式，其中每个新屏幕都放置在堆栈的顶部。它被配置为具有熟悉的 iOS 和 Android 外观和感觉:在 iOS 上新屏幕从右边滑入，在 Android 上从底部淡入。

上面我们将一个路由配置对象传递给了*createstacknaviator*函数。**回家**路线对应*主屏*，关于路线对应*关于屏幕*。

如果我们想指出哪一个是初始路线(显示的第一个屏幕)，我们可以添加一个单独的对象:

```
//Aboutscreen.js
const AppNavigator = createStackNavigator({
  Home: {
    screen: HomeScreen
  },
  About: {
    screen: AboutScreen
  }
},{
        initialRouteName: "Home"
}); 
```

> 注意， *Home* 和 *About* route 名称-值对被一个整体 route 对象包围。

要运行我们的应用程序，您需要下载 Expo 客户端应用程序，确保您的命令行指向项目文件夹，并且您的计算机和手机连接到同一个网络，然后运行以下命令:`npm start`

### **使用标签导航器**

移动应用中最常见的导航方式之一是基于标签的导航。这可以是屏幕底部的标签，也可以是标题下方的顶部标签(甚至可以代替标题)。
这里我们将重点介绍如何使用 *createBottomTabNavigator* 实现标签导航。

让我们通过在*/组件*下创建一个 **ProductScreen.js** 文件，在我们的应用程序中添加另一个屏幕。将以下内容添加到您的 ProductScreen.js

```
//ProductScreen.js
import React, { Component } from 'react'
import { Text, View } from 'react-native'

export default class ProductScreen extends Component {
    render() {
        return (
            <View style = {styles.container}>
                <Text> Welcome to Product's page </Text>
            </View>
        )
    }
}

const styles = StyleSheet.create({
    container: {
      flex : 1,
      alignItems: 'center',
      justifyContent: 'center' },
  }); 
```

接下来，我们将把我们的*产品屏幕*导入到 *App.js* 中。此外，我们将通过导入*createBottonTabNavigation*来实现我们的选项卡导航。在 *AppNavigator* 对象中用 *createBottomTabNavigator* 替换 *createStackNavigator* 。
我们的 *App.js* 现在应该是这样的:

```
//App.js
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { createBottomTabNavigator, createAppContainer } from "react-navigation";

import HomeScreen from './components/HomeScreen';
import AboutScreen from './components/AboutScreen';
import ProductScreen from './components/ProductScreen';

export default function App() {
  return (
    <AppContainer />
  );
}

const AppNavigator = createBottomTabNavigator({
  Home : {
    screen : HomeScreen 
  },
  About: {
    screen: AboutScreen
  },
  Product: {
    screen: ProductScreen
  }
}, {
  initialRouteName: "Home"
});

const AppContainer = createAppContainer(AppNavigator);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
}); 
```

如果我们运行我们的应用程序，我们应该看到我们新的导航标签。

### **抽屉导航**

就像我们在实现选项卡导航时所做的那样，我们将用 *createDrawerNavigator* 替换 *App.js* 中的 *createBottomTabNavigator* ，但是首先我们将导入导航器:

`import { createDrawerNavigator, createAppContainer } from "react-navigation";`

然后更新我们的 *AppNavigator* 变量:

```
const AppNavigator = createDrawerNavigator({
  Home: {
    screen: HomeScreen
  },
  About: {
    screen: AboutScreen
  },
  Contact: {
    screen: ContactScreen
  }
}, {
    initialRouteName: "Home"
  }); 
```

我们还可以决定在路线名称旁边添加图标，为此我在我们的资产文件夹中添加了一些图像，然后在不同的屏幕/路线中添加了*导航选项*。
T3![](img/d8c8f95053ab0f3095a9151116505625.png)T5】

对我们的 *HomeScreen.js 进行以下更改:*

```
//With ES7 syntax, you could type 'rcn" to bootstrap a react native component skeleton
import React, { Component } from 'react'
import { Text, View, Button, Image, StyleSheet } from 'react-native'
import { createStackNavigator, createAppContainer } from 'react-navigation';

export default class Homescreen extends Component {
    static navigationOptions = {
        drawerLabel: 'Home',
        drawerIcon: ({tintColor}) => (
            <Image
                source = {require('../assets/home-icon.png')}
                style= {[styles.icon, {tintColor: tintColor}]}
                />
        )
    }
    render() {
        return (
            <View style={styles.container}>
                <Text> Welcome To Home Screen </Text>
                <Button
                    title = "Go to About Page"
                    onPress={() => this.props.navigation.navigate('About')}
                    />
            </View>
        )
    }
}

const styles = StyleSheet.create({
    container: {
      flex : 1,
      alignItems: 'center',
      justifyContent: 'center'
    },
    icon: {
        width:24,
        height:24,
    }
  }); 
```

对我们的 *AboutScreen.js* 和 *ProductScreen.js* 、*做同样的修改，确保*使用合适的图标目录路径。

通过 *tintColor* 属性，我们可以根据导航标签的活动或不活动状态应用任何颜色。例如，我们可以改变导航抽屉标签的活动状态颜色。转到 *AppNavigator* 变量并添加到选项对象:

```
const AppNavigator = createDrawerNavigator({
  Home: {
    screen: HomeScreen
  },
  About: {
    screen: AboutScreen
  },
  Product: {
    screen: ProductScreen
  }
}, {
    initialRouteName: "Home",
      contentOptions: {
        activeTintColor: '#136207'
     }
  }); 
```

### **结论**

我希望你能从这篇文章中学到一些东西，你也可以留下一些掌声和传播一些爱。接下来，我们将构建一个完整的应用程序，并集中精力探索 React 导航。你也可以在我的 github repo 上查看[最终代码](https://github.com/kpose/NavOptions)。