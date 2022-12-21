# 如何在 React Native 中制作简单的英雄横幅

> 原文：<https://dev.to/danovity/how-to-make-a-simple-hero-banner-in-react-native-3el1>

今天我想写一个简短的教程，教你如何制作一个有线性渐变层的 React 原生英雄旗帜。

## 第一步，使用`FullWidthImage`制作横幅图像

*   使用 codesandbox 的`react-native-web`模板创建一个项目，删除不使用的组件

```
import React, { Component } from "react";
import { Image, StyleSheet, View } from "react-native";

const logoUri =
  "https://imagesvc.timeincapp.com/v3/fan/image?url=https://raptorsrapture.com/wp-content/uploads/getty-images/2016/04/1094224730.jpeg&w=1600";

class App extends Component {
  render() {
    return (
      <View>
        <Image
          accessibilityLabel="React logo"
          source={{ uri: logoUri }}
          resizeMode="contain"
          style={styles.heroHeaderImage}
        />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  heroHeaderImage: {
    height: 80
  }
});

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### 问题

*   我们只能通过控制图像的高度来调整其宽度

[https://codesandbox.io/embed/rn-herobanner1-8j1py](https://codesandbox.io/embed/rn-herobanner1-8j1py)

### 解

*   用`FullWidthImage`代替`Image`

`FullWidthImage`是一个包，它允许**作为一个响应性的图像元素，在保持纵横比**的同时获取其父元素的全部宽度。

它有一个简洁的内置特性叫做`automatic detection`，这个特性**可以自动检测远程图像的长宽比，你需要提供的只是 uri，就像你对常规图像组件所做的一样。**

这就是我们要用的。

就这样，我们有了一面英雄旗帜。

[https://codesandbox.io/embed/rn-herobanner2-nos19](https://codesandbox.io/embed/rn-herobanner2-nos19)

## 第二步，添加并定位 HeroHeaderText

*   用`heroHeaderContainer`包裹`heroHeaderText`组件，以便同时定位两个组件
*   `heroContainer`将有`position: relative`，这将允许任何有`position: absolute`的子组件使用`heroContainer`作为参考点，在这种情况下，我们将`position: absolute`添加到`heroHeaderContainer`，(注意:即使我们不需要添加`position: relative`来工作，但是如果有

[https://codesandbox.io/embed/rn-herobanner3-ribij](https://codesandbox.io/embed/rn-herobanner3-ribij)

## 第三步，添加线性渐变

*   为了使`heroHeaderText`更加可见，一个常见的做法是给背景图像添加线性渐变，React Native 添加线性渐变的方法与你在网上使用的不同**我们使用`react-native-web-linear-gradient`，因为我们在 codesandbox 上使用`react-native-web`模板，对于`react-native`应用，请使用[React-Native-linear-gradient](https://www.npmjs.com/package/react-native-linear-gradient)**
*   使用`position: absolute`和`zIndex`将线性渐变定位在`Hero Banner`图像的顶部

[https://codesandbox.io/embed/rn-herobanner4-l36r0](https://codesandbox.io/embed/rn-herobanner4-l36r0)

## 第四步，添加一个 heroSubheader

*   为了给我们的英雄横幅添加更多“亮点”，我们将使用来自`react-native`的`Button`组件添加一个英雄副标题

完了，[https://codesandbox.io/embed/rnherovanvleet-mcz62](https://codesandbox.io/embed/rnherovanvleet-mcz62)