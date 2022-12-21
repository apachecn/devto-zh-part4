# 创建脱机友好的 React 本地应用程序——第 1 部分:一般提示

> 原文：<https://dev.to/wernancheta/creating-offline-friendly-react-native-apps-part-1-general-tips-oea>

目前，app store 中列出的大多数应用程序都需要互联网连接才能正常工作。但问题是，我们并不真正知道用户的设备是否全天候连接到互联网。这就是为什么我们需要考虑让我们的应用程序尽可能离线友好。这是为了确保用户获得最佳体验，即使他们没有连接到互联网。

在本教程中，我们将探索一些让 React 本地应用离线工作的技术。这并不是说互联网连接在所有应用中都应该被认为是可选的。例如，当用户验证应用程序中的敏感操作时。在这种情况下，用户需要在线才能通过服务器的身份验证。这样，您就可以保护他们的帐户免受不法之徒的侵害。

具体来说，我们将讨论以下内容:

*   关于用户离线时应该避免什么和应该做什么的一般提示。
*   如何通知用户他们脱机。
*   如何从服务器缓存资源？
*   如何看待网络连接是二等公民？

你可以在其 [GitHub repo](https://github.com/anchetawern/RNOffline) 上查看本教程使用的源代码。

## 先决条件

需要 React Native 的基础知识。我们也将使用 Redux 和 Redux Saga，所以对它们稍微熟悉一点也是有帮助的。

本教程假设您已经有了一个工作的 React 本机开发环境。如果您正在使用 Expo，本教程还包括一些专门针对 Expo 用户的提示。但在很大程度上，它假设您正在通过“本机”方式开发 React 本机应用程序。

以下版本供您参考:

*   节点 11.2.0
*   纱线 1.7.0
*   React 本机 CLI 2.0.1
*   反应原生 0.57.6

我们也将使用第三方 React 本地包。如果你想知道使用的具体版本，请参考 [GitHub repo](https://github.com/anchetaWern/RNOffline) 中的`package.json`文件。

## 用户离线时要避免什么

有时候，用户没有互联网连接已经够烦人的了。用户可能正在处理一些事情。当他们打开应用程序时，他们知道他们有连接，但突然他们没有了。他们可能在地铁里，连接时断时续，或者他们的预付费移动数据用完了。我们永远不会真正知道，这就是为什么应用程序在这些场合提供帮助很重要。

以下是当用户离线时，您需要避免的一些事情:

*   弹出窗口和覆盖图显示它们没有连接。
*   完全没有显示任何内容。或者只是显示一个无限旋转的加载动画。
*   只显示他们脱机的错误。
*   阻止用户正在做的事情。或者不允许他们做任何事情。
*   清除他们已经输入的内容。

## 用户离线时做什么

既然您已经知道了当用户离线时您需要避免的事情，那么是时候看看您可以做些什么了:

*   当用户离线时，用一个漂亮的图形告诉他们他们离线了。更好的是，如果你知道他们有连接，但问题出在你的服务器上，用动画来娱乐他们。React Native 自带了 [**NetInfo**](https://facebook.github.io/react-native/docs/netinfo.html) 库来帮助解决这个问题。
*   缓存来自服务器的数据，尤其是他们刚刚访问过的数据。这样，当他们离线时，仍然可以看到最新的数据。如果你正在使用 [Redux](https://redux.js.org/) ，你可以使用 [**redux-persist**](https://github.com/rt2zz/redux-persist) 库。这允许您使用自己选择的存储引擎(例如 AsyncStorage)持久化 Redux 存储，并在用户离线时使用存储的数据。它的工作方式是，如果网络请求成功，我们提供新的数据并用 redux-persist 持久化它。如果网络请求失败，我们会转而提供缓存的副本(恢复)。
*   从服务器缓存资源。对于图片，可以使用[**react-native-cached-image**](https://github.com/kfiroo/react-native-cached-image)库。
*   如果用户离线，使用密码作为认证用户的替代方法。您可以使用[**react-native-sensitive-info**](https://github.com/mCodex/react-native-sensitive-info)库来存储密码和其他需要脱机使用的敏感数据。
*   如果用户试图导航到一个在没有互联网连接的情况下根本无法访问的页面，将他们重定向到应用程序的主页，并通知他们没有连接。这是为了让用户知道他们在哪个应用程序中，他们仍然可以导航到其他页面。

在接下来的几节中，我们将看看如何实现上面提到的一些技巧。

## 通知用户他们离线

在大多数情况下，简单地通知用户何时离线是非常有用的。如果他们试图使用的功能确实需要互联网连接才能使用，这一点尤其正确。在这一节中，我们将看看如何在您的应用中实现这一点。

React Native 自带了一个决定在线状态的开箱即用的库，它叫做 [**NetInfo**](https://facebook.github.io/react-native/docs/netinfo.html) [。](https://facebook.github.io/react-native/docs/netinfo.html)该库允许您确定有关设备连接类型的各种信息。它会告诉您设备是通过 wifi 还是数据套餐连接的。

要使用 NetInfo，首先需要更新`AndroidManifest.xml`文件，以包含访问网络状态的权限:

```
// android/app/src/main/AndroidManifest.xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
```

接下来，在组件类的顶部，导入`NetInfo`库:

```
import { NetInfo } from "react-native"; 
```

在组件类中，初始化存储用户在线状态的状态。我们将它设置为`false`,假设设备默认不在线。稍后，我们将使用`NetInfo`将其值更新为`true`:

```
state = {
  isConnected: false
}; 
```

接下来，我们监听`connectionChange`事件。每当设备的连接性发生变化时，就会触发此事件。这些变化的例子包括:打开或关闭 wifi，启用飞行模式，切换到不同的 wifi 热点，以及通过数据计划连接:

```
componentDidMount() {
  NetInfo.addEventListener("connectionChange", this.handleConnectionChange);
} 
```

当`connectionChange`事件被触发时，执行以下功能。在这里，我们记录响应对象，这样我们就知道我们正在处理的数据。之后，我们确定设备是否实际连接。这将返回一个布尔值，告诉您确切的信息，因此我们可以直接使用该值来更新状态:

```
handleConnectionChange = connectionInfo => {
  console.log("connection info: ", connectionInfo);
  NetInfo.isConnected.fetch().then(isConnected => {
    this.setState({ isConnected: isConnected });
  });
}; 
```

当设备通过 wifi 连接时，这里有一个包含在`connectionInfo`中的示例响应对象:

```
{
  type: "wifi",
  effectiveType: "unknown"
} 
```

如果设备离线，它会包含以下内容:

```
{
  type: "none",
  effectiveType: "unknown"
} 
```

在`render`方法中，我们简单地使用状态中`isConnected`属性的值来为警告框显示不同的背景颜色和文本:

```
render() {
  const boxClass = this.state.isConnected ? "success" : "danger";
  const boxText = this.state.isConnected ? "online" : "offline";

  return (
    <ScrollView contentContainerStyle={styles.container}>
      <View style={[styles.alertBox, styles[boxClass]]}>
        <Text style={styles.statusText}>you're {boxText}</Text>
      </View>
    </ScrollView>
  );
} 
```

最后，不要忘记在卸载组件后删除事件监听器。当用户已经转移到另一个屏幕时，我们真的不希望一个听众在附近徘徊。尤其是在不再需要确定连通性的情况下:

```
componentWillUnmount() {
  NetInfo.isConnected.removeEventListener(
    "connectionChange",
    this.handleConnectionChange
  );
} 
```

NetInfo 在确定网络连接性方面很棒，但它并不能真正确定该连接是否可以访问互联网。例如，在机场、地铁、餐厅或咖啡店，wi-fi 热点位于付费墙后面。在这些情况下，NetInfo 会报告存在连接，而实际上并不存在，因为用户没有通过认证过程、广告或付费墙来访问互联网。

为了克服上述限制，您可以使用 [fetch](https://facebook.github.io/react-native/docs/network.html) 来 ping 任何服务器。如果请求失败，那么你肯定知道你的设备没有连接到互联网。

或者，您可以使用 [react-native-offline](https://github.com/rgommezz/react-native-offline) 库来轻松实现这个功能。首先，用下面的命令安装它:

```
yarn add react-native-offline 
```

在引擎盖下，它也使用 NetInfo，所以你也需要在你的`AndroidManifest.xml`文件中有这个:

```
// android/app/src/main/AndroidManifest.xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
```

安装完成后，您所要做的就是像这样导入和导出您的组件:

```
import { withNetworkConnectivity } from "react-native-offline";
export default withNetworkConnectivity()(OnlineStatusLib); 
```

这使您可以访问组件内部的`isConnected` prop:

```
render() {
  const { isConnected } = this.props;
  const boxClass = isConnected ? "success" : "danger";
  const boxText = isConnected ? "online" : "offline";

  return (
    <View style={[styles.alertBox, styles[boxClass]]}>
      <Text style={styles.statusText}>you're {boxText}</Text>
    </View>
  );
} 
```

默认情况下，它已经 pings 了谷歌。所以即使用户连接了没有互联网连接的网络，仍然会失败，所以`isConnected`变成了`false`。

## 从服务器缓存资源

通知用户他们离线只是创建一个离线友好应用的第一步。第二步是只要有互联网连接，就从服务器缓存资源。这使得用户在离线时仍然能够查看先前已经加载的一些内容。

对于大多数应用程序来说，它需要缓存的唯一资源是图像，所以我们将我们的范围限制在此。

要在 React Native 中缓存图像，可以使用 react-native-cached-image 包。您可以使用以下命令安装它:

```
yarn add react-native-cached-image 
```

安装后，您可以访问以下内容:

*   ImageCacheProvider
*   CachedImage
*   图像缓存管理器

大多数时候，你只会和`ImageCacheProvider`和`CachedImage`一起工作。`ImageCacheProvider`是顶层组件，它向`CachedImage`组件公开缓存功能。该组件取代了 React Native 提供的`Image`组件，它还公开了与`Image`组件相同的 API:

```
import {
  CachedImage,
  ImageCacheProvider,
  ImageCacheManager
} from "react-native-cached-image"; 
```

下面是它的使用方法:

```
const { images } = this.props; // images is an array of objects containing the image data
var urls = images.map(img => {
  return img.url; // URL of the image (example: https://i.imgur.com/aaJYTRw.jpg)
});

return (
  <ImageCacheProvider
    urlsToPreload={urls}
  >
    <View>{this.renderImages(images)}</View>
  </ImageCacheProvider>
); 
```

下面是`renderImages`函数。注意，我们使用的是`CachedImage`组件，而不是`Image`组件。在下面的例子中，`img.url`仍然包含原始的 URL，但是当使用`CachedImage`组件时，它返回文件的缓存版本:

```
renderImages = images => {
  return images.map(img => {
    const uri = img.url;
    return (
      <View style={styles.imageContainer} key={img.id}>
        <CachedImage source={{ uri: uri }} style={styles.image} />
      </View>
    );
  });
}; 
```

如果想管理缓存中的内容，可以使用`ImageCacheManager`。这允许您手动将特定的 URL 下载到缓存中，清除缓存，并获得存储在缓存中的文件列表。以下是如何获取缓存文件列表的示例:

```
const ImageManager = ImageCacheManager({});

ImageManager.getCacheInfo().then(res => {
  console.log("files: ", res.files);
}); 
```

下面是单个文件对象的样子:

```
{  
   "lastModified":1543325588000,
   "size":196551,
   "type":"file",
   "path":"/data/user/0/com.rnoffline/cache/imagesCacheDir/i_imgur_com_bfc5362b31e0f21ee11ac48c03a3d7e4c231bd27/02f23888f180ea23470dbafd4f87da2b3e80e5d0.jpg",
   "filename":"02f23888f180ea23470dbafd4f87da2b3e80e5d0.jpg"
} 
```

如果你使用 Expo，我们上面使用的包的等价物是 react-native-expo-image-cache。您可以使用以下命令安装它:

```
yarn add react-native-expo-image-cache 
```

安装后，您可以通过它提供的`Image`组件来使用它。该组件自动缓存您提供给它的图像，然后使用缓存的副本来呈现图像:

```
import { Image, CacheManager } from "react-native-expo-image-cache";

const uri = "https://i.imgur.com/Ru1qOAB.jpg";
<Image {...{ uri }} style={styles.image} /> 
```

如果要访问缓存图像的实际路径，可以使用以下代码:

```
CacheManager.get(uri)
    .getPath()
    .then(path => {
      console.log(path); // sample path: file:///data/user/0/host.exp.exponent/cache/ExperienceData/%2540wernancheta%252FRNOfflineTips/expo-image-cache/edf3678403b4259059e28e4ff650eae40e9128a7.jpg
    }); 
```

## 把上网当成二等公民

最后一步，把上网当成二等公民。对于通用应用程序，在线应该被认为是可选的。例子包括照片分享、项目管理和笔记应用。用户应该仍然能够使用这些类型的应用程序，即使他们离线。例如，在照片分享应用程序中，用户应该仍然能够选择照片进行上传。一旦他们上线，应用程序会自动为他们上传照片。

一个例外是，当您的应用程序处理高度敏感的数据时，您需要验证用户在应用程序中执行的大多数操作。如果是这样的话，那么在线应该是一个要求。聊天应用、视频流应用和多人游戏等实时应用也是如此。这些应用程序的整个想法是连接到互联网上，以执行其预期的任务。

为了举例说明如何在 React Native 中实现这一点，我们将使用 redux-persist 库。我们将把它添加到一个预编码的应用程序中，这样它就可以在本地保存从服务器上获取的数据。然后，当用户离线时，我们显示这些持久化的数据。

首先，克隆项目 repo 并切换到 starter 分支:

```
git clone https://github.com/anchetaWern/RNOffline.git
cd RNOffline
git checkout starter
yarn install 
```

接下来，安装 redux-persist:

```
yarn add redux-persist 
```

您刚刚克隆的应用程序向 [PokeAPI](https://pokeapi.co/) 发出请求，以获取随机口袋妖怪的数据。目前，如果用户在触发获取时离线，它只是返回一个错误。我们想更新这个，让用户加载上一个口袋妖怪的数据。

打开`src/screens/RehydrateScreen.js`文件，导入 redux-persist 组件:

```
import { persistStore, persistReducer } from "redux-persist";
import storage from "redux-persist/lib/storage";
import { PersistGate } from "redux-persist/integration/react"; 
```

下面是每个组件的作用:

*   `persistStore` -用于持久存储。
*   `persistReducer` -用于增强减速器，使其与 redux-persist 一起工作。这允许您指定选项，如要使用的存储、状态协调器，以及将特定数据列入白名单或黑名单以防止持久化。
*   其中 redux-persist 将存储您的 redux 存储。默认情况下，这使用[异步存储](https://facebook.github.io/react-native/docs/asyncstorage.html)。
*   `PersistGate` -延迟 UI 呈现，直到从本地存储中检索到持久数据并将其加载到存储中。

要使用 redux-persist，调用`persistReducer`并指定您想要使用的选项和缩减器。我们正在使用的示例应用程序已经使用了 Redux，所以我们所要做的就是提供它已经使用的 Redux:

```
// src/screens/RehydrateScreen.js
const persistConfig = {
  key: "root", // name of the key for storing the data
  storage // storage to use. defaults to AsyncStorage
};

const persistedReducer = persistReducer(persistConfig, reducer);
const store = createStore(persistedReducer, applyMiddleware(sagaMiddleware));
let persistor = persistStore(store); 
```

接下来，在您的`render`方法中，您可以像往常一样提供您的`store`。但是这一次，你可以选择使用`PersistGate`组件。这允许您在 redux-persist 从本地存储检索数据时指定要显示的组件。在这种情况下，我们简单地指定了`null`，因为我们实际上没有太多的数据要检索，所以它几乎可以立即加载:

```
// src/screens/RehydrateScreen.js
render() {
  return (
    <Provider store={store}>
      <PersistGate loading={null} persistor={persistor}>
        <PokemonLoader />
      </PersistGate>
    </Provider>
  );
} 
```

接下来，更新 PokemonLoader 组件，以包括一条附加消息和一个按钮，允许用户检索他们离线前加载的最后一个 Pokemon:

```
// src/components/PokemonLoader.js
{error && (
  <View>
    <View style={styles.smallTextContainer}>
      <Text style={styles.errorText}>Something went wrong</Text>
      <Text>Would you like to view the last Pokemon instead?</Text>
    </View>
    <Button onPress={this.rehydrate} title="Yes" color="#841584" />
  </View>
)} 
```

当按钮被点击时，我们执行一个方法，该方法分派用于恢复前一个口袋妖怪的数据的动作:

```
rehydrate = () => {
  this.props.requestPersistedPokemon();
}; 
```

更新`mapDispatchToProps`以分派动作:

```
const mapDispatchToProps = dispatch => {
  return {
    // the rest of the action creators here...
    requestPersistedPokemon: () => dispatch({ type: "API_CALL_RESTORE" }) // add this
  };
}; 
```

最后，更新减速器以处理新的动作类型`API_CALL_RESTORE`。这将从状态中删除`error`。这样，通过`API_CALL_SUCCESS`载入的最后一个口袋妖怪就恢复了。这里需要注意的重要一点是，当调用`API_CALL_FAILURE`时，我们并没有清除`pokemon`数据。这样，即使发生错误，最后一个口袋妖怪也总是保存在本地存储器中:

```
// src/redux/index.js

// the rest of your action types here..
const API_CALL_RESTORE = "API_CALL_RESTORE";

export function reducer(state = initialState, action) {
  switch (action.type) {
    // the rest of the reducers here...

    case API_CALL_SUCCESS:
      return { ...state, fetching: false, pokemon: action.pokemon };
    case API_CALL_FAILURE:
      return { ...state, fetching: false, error: action.error };

    // add this  
    case API_CALL_RESTORE:
      return { ...state, fetching: false, error: null };
    default:
      return state;
  }
} 
```

## 结论

就是这样！在本教程中，您了解了如何让 React 原生应用程序离线友好的一些技巧。具体来说，您了解了如何在用户离线时通知用户，如何从服务器缓存图像，以及如何在本地保存数据，以便在用户离线时可以访问这些数据。

请继续关注第二部分，在那里您将学习如何在现实世界中应用我们所学的概念。

你可以在它的 [GitHub repo](https://github.com/anchetawern/RNOffline) 上找到本教程使用的源代码。

*原载于[推手教程枢纽](https://pusher.com/tutorials/offline-react-native-part-1)T3】*