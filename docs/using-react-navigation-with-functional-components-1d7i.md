# 将 React 导航与功能组件一起使用

> 原文：<https://dev.to/harshil1712/using-react-navigation-with-functional-components-1d7i>

React Navigation 是一个流行的库，与 React Native 一起用于导航。它帮助我们构建导航应用。这里有一个使用功能组件实现 React 导航的快速指南。
我一直将`react-navigation`用于类组件，但从未将其用于功能组件。最近我在做一个学习钩子的小项目，遇到了使用功能组件`reat-navigation`的问题。遗憾的是，官方网站上没有可用的文档。我希望这篇文章能帮助那些想用功能组件设置导航的人。

*注意:如果你是 React 导航的新手，我建议你浏览一下他们的[官方文档](https://reactnavigation.org/)以了解基本知识。*

**创建堆栈导航器**

创建堆栈导航器与创建类组件相同。我们使用 createStackNavigator 方法并传递对象。

```
import { createAppContainer, createStackNavigator } from 'react-navigation'

// Importing the screens
import Home from './src/Home'
import Article from './src/Article'

const AppNavigator = createStackNavigator({
    Home: {screen: Home},
    Article: {screen: Article},
    },
    {
        // Specifing Initial Screen
        initalRoute: 'Home'
    }
);

const App = createAppContainer(AppNavigator);

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

**创建主屏幕(类组件)**

我们将通过制作类组件来创建主屏幕，以了解不同之处。

```
import React from 'react'
import { View, Text, TouchableOpacity } from 'react-native'

export default class Home extends React.Component {

    static navigationOptions = {
        // Sets the title of the Header
        title: 'Home',
    };

    render () {
        return (
            <View>
                <TouchableOpacity
                    onPress={()=>{this.props.navigation.navigate('Article')}}
                >
                    <Text>Click Here</Text>
                </TouchableOpacity>
            </View>
        )
    }
}

... 
```

Enter fullscreen mode Exit fullscreen mode

要将这个类组件转换成功能组件，这里要记住的最重要的事情是`navigationOptions`是一个静态方法。

静态方法不会在类的实例上调用。相反，它们在类本身上被调用。

因为我们不能创建功能组件的实例，所以我们在功能组件上调用方法。
导航对象作为道具传递。

**创建主屏幕(功能组件)**

```
import React from 'react'
import { View, Text, TouchableOpacity } from 'react-native'

const Home = ({navigation}) =>(
    <View>
        <TouchableOpacity
            onPress={()=>{navigation.navigate('Article')}}
        >
            <Text>Click Here</Text>
        </TouchableOpacity>
    </View> )

Home.navigationOptions = () => {(
    title:'Home'
)}

...

export default Home 
```

Enter fullscreen mode Exit fullscreen mode

这是一个小演示，希望对你有帮助！