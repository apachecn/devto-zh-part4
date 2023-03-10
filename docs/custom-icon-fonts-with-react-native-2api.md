# 使用 React Native 自定义图标字体

> 原文：<https://dev.to/orangejellyfish/custom-icon-fonts-with-react-native-2api>

当在 [React Native](https://facebook.github.io/react-native/) 应用中使用图标时，我们被
的大量免费和开源图标集宠坏了，如 [FontAwesome](https://fontawesome.com/) 、
T6】Material 和 [Ionicons](https://ionicons.com/) 。为了让事情变得更简单，精彩的
react-native-vector-icons 项目将所有这些图标
集合以及更多集合打包成一个包。但是有时候免费和开源的图标集
并不能满足你的需求，你会想如何实现
和定制图标集一样的开发者体验。幸运的是，
react-native-vector-icons 和一堆其他项目也让我们在这里有所涉及
。

## 设置反应-原生-矢量-图标

如果你正在使用 [Expo](https://expo.io/) 并且没有弹出到 ExpoKit，那么
在这里没有什么可做的。Expo 在
[`@expo/icons`包](https://docs.expo.io/versions/latest/guides/icons/)中捆绑了一个关于 react-native-vector-icons 的包装。

否则，react-native-vector-icons 包的安装就像你对 react 原生应用程序的期望一样。它被发布到 npm，这样你就可以用 CLI 或类似工具将它添加到你的项目中(当
使用 React Native 时，我们倾向于使用 [Yarn](https://yarnpkg.com/en/) ，因为它在 Expo 中表现得更好):

```
$ yarn add react-native-vector-icons
$ react-native link react-native-vector-icons 
```

## 生成图标字体

随着 react-native-vector-icons 在你的项目中建立，你已经准备好对图标本身进行操作了。根据我们的经验 [IcoMoon](https://icomoon.io/) 是这里最有效的
工具。IcoMoon 是一个 web 应用程序，允许您导入 SVG 文件，然后
从这些 SVG 的集合中生成各种格式的字体文件，如下面的截图
所示:

[![Creating an icon set in IcoMoon](img/d5a786d2d56fb9969afafcda039fc7bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wgkUz6QZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cyx35jc9fzeg6sf60yxu.png) 
*在 IcoMoon 中创建图标集的例子*

将所有图标导入 IcoMoon 应用程序后，您可以选择它们并
“生成”字体文件(请注意，在下面的截图中，高亮显示的“生成字体”按钮左侧显示了所选图标的数量
):

[![Generating an icon font in IcoMoon](img/5249476a0c7ba2362e9070c1695c7419.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVOIx16q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/70dyk98eo23hdpqssebw.png) 
*从 IcoMoon* 中的图标集生成图标字体的例子

有几个选项可以配置结果字体，但大多数情况下
默认值就足够了。当你高兴的时候，下载这个包并解压到
找到一些字体文件，一些例子和一个`selection.json`文件。我们需要的是
文件加上`*.ttf`字体文件。将这些文件复制到 React 本机代码库中的一个
sensible 目录中。我们通常会选择一个顶级
目录`assets`，其中包含应用程序
使用的所有静态资产，包括字体和图像。

## 使用自定义图标字体

建议你预装你的应用将要使用的任何字体，你的新定制图标字体也不例外。在你的主应用入口点你可以
使用`Font.loadAsync`方法。如果你已经使用 Expo CLI 初始化了
你的项目，那么你可能已经有了类似这样的东西:

```
import React from 'react';
import { registerRootComponent, AppLoading } from 'expo';
import * as Font from 'expo-font';

class App extends React.Component {
  state = {
    isLoadingComplete: false,
  };

  loadResourcesAsync = async () => Promise.all([
    Font.loadAsync({
      'custom-icons': require('../assets/fonts/custom-icons.ttf'),
    }),
  ]);

  handleLoadingError = (error) => {
    // In this case, you might want to report the error to your error
    // reporting service, for example Sentry
    console.warn(error);
  };

  handleFinishLoading = () => {
    this.setState({ isLoadingComplete: true });
  };

  render() {
    const { isLoadingComplete } = this.state;

    if (!isLoadingComplete) {
      return (
        <AppLoading
          startAsync={this.loadResourcesAsync}
          onError={this.handleLoadingError}
          onFinish={this.handleFinishLoading}
        />
      );
    }

    return (
      <App />
    );
  }
}

registerRootComponent(App);

// Export the App component for unit testing purposes. Expo handles rendering
// via the "registerRootComponent" call above and does not require an export.
export default App; 
```

通过这种配置，您的自定义图标字体文件将在应用程序启动时加载-
而不是在首次使用时加载，否则会导致
无样式(或缺失)内容的闪烁。

接下来，您需要一个普通的 React 组件来呈现新字体的图标。
react-native-vector-icons 包提供了一些实用方法来简化这个
过程。下面几行就够了。我们通常
将它放在一个`src/components/icon/index.js`文件中:

```
import { createIconSetFromIcoMoon } from 'react-native-vector-icons';
import icoMoonConfig from '../../../assets/fonts/selection.json';

// We use the IcoMoon app (https://icomoon.io) to generate a custom font made up
// of SVG icons. The actual font file is loaded up-front in src/index.js.
export default createIconSetFromIcoMoon(icoMoonConfig, 'custom-icons'); 
```

这里需要注意的要点是从 IcoMoon 下载的
包中导入的`selection.json`文件和字体名称`custom-icons`，正如在主应用程序入口点的`Font.loadAsync`调用中定义的
。

可以将`createIconSetFromIcoMoon`函数看作是一个工厂，由
返回一个 React 组件。现在，您可以从其他
组件中导入该组件来呈现图标。下面的例子在`src/components/button/index.js` :
中想象了一个简单的“按钮”
组件

```
import React from 'react';
import { TouchableOpacity, Text } from 'react-native';
import Icon from '../icons';

const Button = () => (
  <TouchableOpacity>
    <Icon name="settings" />
    <Text>Settings</Text>
  </TouchableOpacity> );

export default Button; 
```

新的`Icon`组件支持与 react-native-vector-icons 捆绑在一起的开源图标
设置的所有属性。这意味着您可以从 React 本机样式表中应用
自定义样式，如大小和颜色。