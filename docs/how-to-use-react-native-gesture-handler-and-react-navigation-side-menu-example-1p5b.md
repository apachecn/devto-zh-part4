# 如何使用 React 本机手势处理程序和 React 导航(侧菜单示例)

> 原文：<https://dev.to/mkilmer/how-to-use-react-native-gesture-handler-and-react-navigation-side-menu-example-1p5b>

所以我决定写这篇文章，因为我在 React Native 上实现手势处理程序时遇到了一些问题，一些朋友也遇到了这个问题。

**T2`But isn't it just looking at the React Native Gesture Handler documentation and implementing the code? So, React-Native-Gesture-Handler supports autolinking on Android but the problem is that it is not yet compatible with AndroidX.`**

**什么是手势处理器？**

声明式 API 暴露平台 **`native touch`** 和 **`gesture`** 系统反应原生。 [React Native 手势处理器](https://github.com/kmagiera/react-native-gesture-handler)提供本地驱动的手势管理 API，用于在 React Native 中构建最佳的基于触摸的体验。

**什么是 React 导航？**

[React 导航](https://reactnavigation.org/en/)诞生于 React 原生社区对完全用 JavaScript 编写的可扩展且易于使用的导航解决方案的需求(因此您可以阅读并理解所有源代码)，基于强大的原生原语。

**`Versions of dependencies`**T3】

```
 "dependencies":  {  "react-native":  "0.60.5",  "react-native-gesture-handler":  "1.3.0",  "react-navigation":  "3.11.1",  "jetifier":  "^1.6.4
  } 
```

Enter fullscreen mode Exit fullscreen mode

**`Instalation`**T3】

```
 yarn add react-native-gesture-handler@1.3.0
  yarn add react-navigation@3.11.1
  yarn add jetifier 
```

Enter fullscreen mode Exit fullscreen mode

现在只需按照 React 导航文档步骤在 Android 和 IOS 上部署即可。

那么如何用手势处理器解决问题呢？Jetifier 就是解决方案。通过在编译时转换所有的依赖包，它有助于使库与您的 AndroidX 项目兼容。

**解决步骤**

1.打开项目 package.json 文件，将这一行添加到脚本部分:

```
 "postinstall"  :  "npx jetify" 
```

Enter fullscreen mode Exit fullscreen mode

2.现在运行 **`npm install`** 或者 **`yarn`**

3.代码！

**如何实现侧面菜单**

项目结构

```
 ├── src                    # source folder
└── screens
  └── SideDrawer           # screen components
  └── HomeScreen             
├── App                    # createAppContainer 
```

Enter fullscreen mode Exit fullscreen mode

sidedrawer . js〔t0〕

```
import React, { Component } from 'react';
import {
    View,
    Text,
    Button,
    StyleSheet
} from 'react-native';

class SideDrawer extends Component{
    render(){
        return(
            <View style={styles.main}>
                <Text>SideMenu</Text>
            </View>
        )
    }
}
const styles = StyleSheet.create({
    main : {
        flex : 1,
        alignItems:'center',
        justifyContent:'center',
        fontWeight:'bold'
    }
})
export default SideDrawer 
```

Enter fullscreen mode Exit fullscreen mode

└── HomeScreen.js

```
import React, { Component } from 'react';
import {
    View,
    Text,
    StyleSheet,
    Button
} from 'react-native';

class HomeScreen extends Component{
    render(){
        return(
            <View style={styles.main}>
               <Button
                title={"Open Side Menu"}
                onPress = {()=> this.props.navigation.openDrawer()}
               />
            </View>
        )
    }
}
const styles = StyleSheet.create({
    main : {
        flex : 1,
        alignItems:'center',
        justifyContent:'center'
    }
})
export default HomeScreen 
```

Enter fullscreen mode Exit fullscreen mode

├── App.js

```
import React, {Component} from 'react';
import {
  StyleSheet,
  View,
  Button,
  Text
} from 'react-native';
import { createAppContainer, createDrawerNavigator } from 'react-navigation';
import HomeScreen from './src/screens/HomeScreen';
import SideDrawer from  './src/screens/SideDrawer';

const App = createAppContainer(
  createDrawerNavigator({
    Home : HomeScreen,

  },{
    contentComponent : SideDrawer
  })
)

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

**最终结果**😆 😎
T3[T5](https://res.cloudinary.com/practicaldev/image/fetch/s--jQe0-uy6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ayyu1m5cslhip9zqrb10.gif)