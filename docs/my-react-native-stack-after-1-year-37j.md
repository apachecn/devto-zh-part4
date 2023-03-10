# 1 年后我的 React 本机堆栈

> 原文：<https://dev.to/newbiebr/my-react-native-stack-after-1-year-37j>

对于我在 Dev.to 上的第一篇帖子，我想分享我的 React 原生项目结构、配置和一些技巧。它包含了我用 React Native 开发一年后学到的大部分东西，从创建到发布。

我创建了一个名为[typescript-react-native-starter](https://github.com/NewBieBR/typescript-react-native-starter)的 Github 库，现在我在所有项目中都使用它。
所以我希望它对其他刚刚接触 React Native 的开发者有所帮助。欢迎你来做:D 公关

## **特色**

在几个项目之后，我最近开始使用 Typescript，这使我理解了类型化变量的重要性。一开始可能需要一些时间来学习，但这是值得的。多亏了 Typescript，您可以避免数小时甚至数天的调试。

此外，它使您的代码自文档化，这对于有多个开发人员的项目是至关重要的。

*   **打字稿**

    *   [基于 React Native 的类型脚本模板](https://github.com/react-native-community/react-native-template-typescript)
*   **通量状态管理**

    *   [Redux](https://github.com/reduxjs/redux) :可预测状态容器
    *   [Redux Persist](https://github.com/rt2zz/redux-persist) :线下商店
    *   [Redux 传奇](https://github.com/redux-saga/redux-saga):Redux 的副作用模型
    *   [类型安全操作](https://github.com/piotrwitek/typesafe-actions):轻松创建类型安全操作

```
 import { action } from 'typesafe-actions';
      import * as types from './actionTypes';

      export const myAction = payload => action(types.MY_ACTION_TYPE, payload); 
```

*   **导航**

    *   [React 导航](https://github.com/react-navigation/react-navigation):基于 Javascript 的易用导航解决方案
*   **单元测试**

    *   用 [Jest](https://github.com/facebook/jest) 、[酶](https://github.com/airbnb/enzyme)和[react-native-testing-library](https://github.com/callstack/react-native-testing-library)进行单元测试
    *   [Codecov](https://codecov.io/) :覆盖率报告
*   **CI/CD**

    *   用 [husky](https://github.com/typicode/husky) 的钩子运行林挺预提交和单元测试预推送
    *   占位符应用图标:有助于通过浪子快速将你的应用上传到测试版
    *   [App 图标生成器](https://github.com/dwmkerr/app-icon#readme):生成所有需要的大小，标注和注释图标。
    *   占位符功能图形和截图，以快速上传测试版 android 应用程序
*   **林挺**

    *   用 Airbnb 样式配置的 Tslint
    *   Vscode 更美观兼容
*   **国际化和本地化**

    *   [react-native-localization](https://github.com/stefalda/ReactNativeLocalization):i18n 的易用包
*   **其他**

    *   [Cocoapods](https://github.com/CocoaPods/CocoaPods) : iOS 依赖管理器
    *   [jetifier](https://github.com/mikehardy/jetifier#readme) :用于 [React 原生 0.60 AndroidX 迁移的过渡工具](https://facebook.github.io/react-native/blog/2019/07/03/version-60#androidx-support)
    *   autobind-decorator :用装饰器轻松绑定组件的功能

```
// Before
handleClick()  {...}

<button onClick={ this.handleClick.bind(this) }></button>

// After
@boundMethod
handleClick() {...}

<button onClick={ this.handleClick }></button> 
```

## **项目结构**

我使用的结构灵感来自很多方面，所以你可能会觉得这很熟悉。我喜欢把我的文件按类别分开，除了一些特殊的，比如`App.tsx`、`store.ts`，...

`publishing`文件夹也包含一些有用的占位符图像来部署您的应用程序。
例如，为了在 Google Play 上部署您的应用程序，即使是内部测试，您也必须添加截图、功能图形，...一开始还可以，但是在几个项目之后，这有点烦人，所以我决定为此创建一些占位符图像。

```
├── __tests__                            // Unit tests
│   ├── App.test.tsx                     // App component's tests
│   ├── components
│   │   └── MyComponent.test.txs
│   └── ...
├── android
├── app.json
├── assets                               // All assets: images, videos, ...
├── index.js
├── ios
├── publishing                           // Icon, screenshots, preview,... for App Store & Play Store
└── src
    ├── App.tsx
    ├── actions                          // Actions
    │   ├── actionTypes.ts               // Action types
    │   └── app.ts                       // appReducer's actions
    ├── components                       // Components
    │   └── MyComponent.tsx
    ├── constants                        // Colors, sizes, routes,...
    │   └── strings.ts                   // i18n
    ├── containers                       // Screens, pages,...
    ├── lib                              // Libraries, services,...
    ├── index.tsx                        // Root component
    ├── reducers                         // Reducers
    │   └── app.ts                       // appReducer
    ├── sagas                            // Redux sagas
    ├── store.ts
    ├── types                            // Type declarations
    │   └── index.d.ts
    └── utils                            // Utilities 
```

## **有用的提示**

这一部分是完全随机但有用的提示，欢迎在评论中分享你的或做个公关。

### 导航服务

从`src/lib/NavigationService.ts`
使用**导航服务**可以在没有导航道具的情况下[导航](https://reactnavigation.org/docs/en/navigating-without-navigation-prop.html)

```
import NavigationService from '../lib/NavigationService';

//...

NavigationService.navigate('ChatScreen', { userName: 'Lucy' }); 
```

### 可可盒

当你运行`react-native link`并且链接库有 podspec 文件时，那么链接将使用 Podfile。要禁用此功能，请移除

```
# Add new pods below this line 
```

从`ios/Podfile`的第 24 行开始

### 静态捆绑

静态包是在每次定位物理设备时构建的，即使在调试中也是如此。为了节省时间，在调试中禁用了包生成

### 反应-原生-屏幕

您可以将 react-native-screens 与 react-navigation 一起使用，以便[改善内存消耗](https://reactnavigation.org/docs/en/community-libraries-and-navigators.html#react-native-screens)

*   安装并遵循 [react-native-screens](https://github.com/kmagiera/react-native-screens) 中的`Usage with react-navigation (without Expo)`步骤

*   打开`./src/index.tsx`并取消注释

```
// import { useScreens } from 'react-native-screens';
// useScreens(); 
```

### 反应灵敏

*   尽可能避免“绝对”定位和硬值(100，300，1680，...)尤其是大的。
*   请改用弹性框和%值
*   如果你必须使用硬值，我有这个`normalize`函数来根据屏幕的宽度或高度调整硬值。稍后我可能会将它上传到存储库:

```
import { Dimensions, Platform, PixelRatio } from 'react-native';

export const { width: SCREEN_WIDTH, height: SCREEN_HEIGHT } = Dimensions.get(
  'window',
);

// based on iphone X's scale
const wscale = SCREEN_WIDTH / 375;
const hscale = SCREEN_HEIGHT / 812;

export function normalize(size, based = 'width') {
  const newSize = based === 'height' ? size * hscale : size * wscale;
  if (Platform.OS === 'ios') {
    return Math.round(PixelRatio.roundToNearestPixel(newSize));
  } else {
    return Math.round(PixelRatio.roundToNearestPixel(newSize)) - 2;
  }
} 
```

所以现在我可以用:

```
// iphone X
normalize(100) // = 100

// iphone 5s
normalize(100) // = maybe 80

// You can choose either "width" (default) or "height" depend on cases:
container = {
  width: normalize(100, "width"), // "width" is optional, it's default
  height: normalize(100, "height")
} 
```

*   在推送之前，在 3 个不同的模拟器上测试你的应用程序:iphone5s(小)、iphone 8(中)和 iphone Xs Max(大)

### 贝塔分布与浪子

*   安装[快速通道](https://docs.fastlane.tools/getting-started/ios/setup/)

```
 # Using RubyGems
  sudo gem install fastlane -NV

  # Alternatively using Homebrew
  brew cask install fastlane 
```

#### iOS

*   打开您的项目 Xcode 工作区，并更新您的应用程序的`Bundle Identifier`和`Team`
*   初始化快速通道

```
 cd <PROJECT_NAME>/ios
  fastlane init 
```

*   分发你的应用

```
 fastlane beta 
```

#### 安卓

*   [收集您的谷歌凭证](https://docs.fastlane.tools/getting-started/android/setup/#collect-your-google-credentials)
*   使用 Android Studio 打开您的项目，并在`build.gradle (Module: app)`文件中更新您的应用程序的`applicationId`
*   从`Build`菜单中选择`Generated Signed Bundle / APK...`
*   `Next`然后`Create new...`在`Key store path`下面然后`Next`和`Finish`
*   首次部署应用程序时，您必须手动将其上传到 Google Play 控制台。谷歌不允许在第一次上传时使用他们的 API。
*   在 [Google Play 控制台](https://play.google.com/apps/publish/)中创建你的应用程序(与 iOS 不同，浪子无法为你做到这一点)
*   确保这 4 个勾号图标是绿色的

    > 推荐顺序:`Pricing & distribution`、`Content rating`、`Store listing`和`App releases`
    > 
    > 您可以在`publishing/android`文件夹中找到`Store listing`所需的资产

    [![](img/9d04b7f3c82342d4b1bdb7aed5682693.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ADRHMYf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/C7vDL.png)

*   初始化快速通道

```
 cd <PROJECT_NAME>/android
  fastlane init 
```

*   使用`publishing`中的快速文件

```
 cp publishing/android/fastlane/Fastfile android/fastlane 
```

*   分发你的应用

```
 fastlane beta 
```

> 没有官方插件自动升级 android 版本代码(不像 iOS lane)。
> 每次部署前，请务必手动升级`android/app/build.gradle`内的`versionCode`值。

#### 更

*   查看[浪子的测试版发行指南](https://github.com/thecodingmachine/react-native-boilerplate/blob/master/docs/beta%20builds.md)了解更多详情
*   浪子的文档为 React Native

### Apple Store Connect 的合规缺失

如果您不使用浪子，也不想在每次推送时*向*提供出口合规信息**，请将此信息添加到您的`Info.plist`** 

```
<key>ITSAppUsesNonExemptEncryption</key>  <false/> 
```

[![](img/e265cc50c789e248010446ff671d1957.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D5XNsJbH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/i7ret.png) 
注意，如果你的[应用程序使用加密](https://developer.apple.com/documentation/bundleresources/information_property_list/itsappusesnonexemptencryption)，你可能需要将其设置为`<true/>`