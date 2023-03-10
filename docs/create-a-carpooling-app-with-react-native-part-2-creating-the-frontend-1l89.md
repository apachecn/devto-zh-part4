# 使用 React Native 创建拼车应用程序——第 2 部分:创建前端

> 原文：<https://dev.to/wernancheta/create-a-carpooling-app-with-react-native-part-2-creating-the-frontend-1l89>

# 使用 React Native 创建拼车应用程序-第 2 部分:创建应用程序

这是关于使用 React Native 创建拼车应用程序的两部分系列的第二部分。这是我们将创建实际应用程序的部分。我将向您展示如何设置该应用程序，以便您可以在 Android 模拟器(Genymotion)或 iOS 设备上运行它。

## 先决条件

本教程的先决条件与第一部分相同。需要在您的机器上设置以下内容:

*   反应本地开发环境
*   坞站和坞站组成
*   饭桶

此外，您应该已经有了一个正在运行的服务器实例，它通过 ngrok 公开给 internet。如果你还没有设置这些，一定要检查第一部分。

为了有效地学习本教程，您应该很好地掌握以下 React 概念:

*   小道具
*   参考文献
*   状态
*   组件生命周期

至于使用 React Native 构建应用程序，了解如何执行以下操作会有所帮助:

*   如何使用原始的 React Native 组件，例如`View`或`Text`。
*   如何向组件添加样式。
*   如何创建自己的组件？

## 我们将建造什么

我们将要构建的内容的完整细节在本系列的第一部分中提供。作为复习，我们将建立一个拼车应用程序。这允许用户共享他们当前乘坐的车辆，以便其他人可以跳上同一辆车。该应用程序负责:

*   匹配用户，使得只有走相同路线的用户可以彼此共乘一次。
*   两个用户匹配后，该应用程序会实时跟踪彼此当前的位置。

在本教程的其余部分，我将把共享乘车的用户称为“骑手”。而正在搜索搭车的用户是“徒步旅行者”。

## 安装依赖项

首先生成一个新的 React 本地项目:

```
react-native init Ridesharer 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个`Ridesharer`目录。这将作为我们在本教程剩余部分中使用的根目录。

该应用程序依赖以下库来实现特定功能:

*   [**axios**](https://github.com/axios/axios)——用于向服务器发出请求。虽然 React Native 已经自带了 [fetch](https://facebook.github.io/react-native/docs/network.html) ，但是 axios 给了我们一个更简单的 API。
*   [**-Pusher-js**](https://github.com/pusher/pusher-js)-官方 Pusher JavaScript 库。这使我们能够连接到 Pusher 应用程序并发送实时数据。
*   [**react-native-geocoding**](https://github.com/marlove/react-native-geocoding)-用于将经纬度对转换为实际的地名。
*   [**-react-native-Google-places-autocomplete**](https://github.com/FaridSafi/react-native-google-places-autocomplete)-用于搜索用户目的地。
*   [**-react-native-maps**](https://github.com/react-community/react-native-maps)-用于显示 app 内的地图。这也用于显示用户位置和目的地的标记。
*   [**-react-native-maps-directions**](https://github.com/bramus/react-native-maps-directions)-用于显示从用户的出发地到目的地的路线。
*   [**-react-native-vector-icons**](https://github.com/oblador/react-native-vector-icons)-用于在 app 内使用图标。
*   [**【react】-导航**](https://github.com/react-navigation/react-navigation) -轻松实现屏幕间导航。

为了确保我们都使用相同的包版本，打开`package.json`文件并用下面的代码更新【T1:】文件

```
"dependencies": {
  "axios": "0.18.0",
  "prop-types": "15.6.1",
  "pusher-js": "4.2.2",
  "react": "16.3.1",
  "react-native": "0.55.4",
  "react-native-geocoding": "0.3.0",
  "react-native-google-places-autocomplete": "1.3.6",
  "react-native-maps": "0.20.1",
  "react-native-maps-directions": "1.6.0",
  "react-native-vector-icons": "4.6.0",
  "react-navigation": "2.0.1"
}, 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，保存文件并执行`npm install`。

## 设置依赖关系

现在，您已经安装了所有的依赖项，在开始编写应用程序之前，您还需要做一件事情。以下依赖项需要额外的设置:

*   [反应原生向量图标](https://github.com/oblador/react-native-vector-icons)
*   [react-native-maps](https://github.com/react-community/react-native-maps)

每个库的 GitHub repos 上都有关于如何设置依赖项的说明。以下是我们正在使用的特定版本的设置说明的链接:

*   [react-native-vector-icons 4 . 6 . 0 版](https://github.com/oblador/react-native-vector-icons/tree/v4.6.0#installation)
*   [react-native-maps v0.20.1](https://github.com/react-community/react-native-maps/blob/v0.20.1/docs/installation.md)

请注意，如果你在将来的某个时候读到这篇文章，你可能必须安装最新的软件包版本，并遵循它们最新的安装说明。

## 构建 app

现在我们已经准备好构建应用程序了。在`Ridesharer`目录中导航，因为这将是我们的工作目录。

请注意，任何时候你对在哪里添加特定代码感到困惑，你都可以访问 [GitHub](https://github.com/anchetaWern/Ridesharer/tree/master/app) [repo](https://github.com/anchetaWern/Ridesharer/tree/master/app) 并查看文件。

### 索引

打开`index.js`文件，确保您注册的名称与您生成项目时使用的名称相同。这种情况下应该是`Ridesharer` :

```
// Ridesharer/index.js
import { AppRegistry } from 'react-native';
import App from './App';

AppRegistry.registerComponent('Ridesharer', () => App); 
```

Enter fullscreen mode Exit fullscreen mode

### 根构件

创建一个`Root.js`文件。这将作为应用程序的根组件。这是我们设置导航的地方，所以我们包括应用程序的两个页面:主页和地图。我们将稍后创建这些页面:

```
// Ridesharer/Root.js
import React from 'react';
import { StackNavigator } from 'react-navigation';

import HomePage from './app/screens/Home';
import MapPage from './app/screens/Map';

const RootStack = StackNavigator(
  {
    Home: {
      screen: HomePage
    },
    Map: {
      screen: MapPage
    }
  },
  { 
    initialRouteName: 'Home', // set the home page as the default page 
  }
);

export default RootStack; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们使用了`StackNavigator`，它是 React 导航库附带的导航器之一。这允许我们将页面推入堆栈或从堆栈中弹出页面。导航到一个页面意味着把它推到堆栈前面，返回意味着弹出当前在堆栈前面的页面。

### App 组件

打开`App.js`文件，渲染`App`组件:

```
// Ridesharer/App.js
import React, { Component } from 'react';
import {
  StyleSheet,
  View
} from 'react-native';

import Root from './Root';

export default class App extends Component {

  render() {
    return (
      <View style={styles.container}>
        <Root />
      </View>
    );
  }

}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff'
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 攻丝机组件

`Tapper`组件只是为了方便而创建的一个按钮。我们实际上不能对内置的 React Native `Button`组件应用自定义样式，所以我们创建了这个组件。这个组件将`Button`组件包装在一个`View`中，在这个`View`中应用了样式:

```
// Ridesharer/app/components/Tapper/Tapper.js
import React from 'react';
import { View, Button } from 'react-native';

import styles from './styles';

const Tapper = (props) => {
  return (
    <View style={styles.button_container}>
      <Button
        onPress={props.onPress}
        title={props.title}
        color={props.color}
      />
    </View>
  );
}

export default Tapper; 
```

Enter fullscreen mode Exit fullscreen mode

下面是风格声明:

```
// Ridesharer/app/components/Tapper/styles.js
import { StyleSheet } from 'react-native';

export default StyleSheet.create({
  button_container: {
    margin: 10
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们使用一个`index.js`文件导出它，这样我们可以简单地将组件称为`Tapper`，而不需要在后面的`import`语句中包含`Tapper.js`文件:

```
// Ridesharer/app/components/Tapper/index.js
import Tapper from './Tapper';

export default Tapper; 
```

Enter fullscreen mode Exit fullscreen mode

如果你不想创建一个单独的组件，你可以使用`TouchableOpacity`和`TouchableHighlight`组件。这两个选项允许您添加自定义样式。

### 首页

`Home`页面是用户打开应用程序时看到的默认页面。

从包含我们需要的所有 React 原生包开始:

```
// Ridesharer/app/screens/Home.js
import React, { Component } from 'react';
import { 
  View, 
  Text, 
  StyleSheet, 
  TextInput, 
  Alert, 
  ActivityIndicator, 
  PermissionsAndroid, 
  KeyboardAvoidingView 
} from 'react-native'; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的包中，只有这三个包需要解释:

*   `PermissionsAndroid` -用于请求许可在 Android 上使用设备的地理定位功能。
*   `KeyboardAvoidingView` -用于在屏幕键盘弹出时自动调整视图。这允许用户在键盘打开时看到他们正在输入的内容。大多数时候，尤其是在小屏幕的设备上，键盘打开时输入是隐藏的。

接下来，包含我们之前安装的第三方包:

```
import axios from 'axios';
import Icon from 'react-native-vector-icons/FontAwesome';
import Tapper from '../components/Tapper'; 
```

Enter fullscreen mode Exit fullscreen mode

添加您的 ngrok URL(这是在本系列的第一部分中创建的):

```
const base_url = 'YOUR NGROK URL'; 
```

Enter fullscreen mode Exit fullscreen mode

声明将请求[地理位置](https://facebook.github.io/react-native/docs/geolocation.html)许可的函数，然后调用它:

```
async function requestGeolocationPermission() {
  try{
    const granted = await PermissionsAndroid.request(
      PermissionsAndroid.PERMISSIONS.ACCESS_FINE_LOCATION,
      {
        'title': 'Ridesharer Geolocation Permission',
        'message': 'Ridesharer needs access to your current location so you can share or search for a ride'
      }
    );

    if(granted === PermissionsAndroid.RESULTS.GRANTED){
      console.log("You can use the geolocation")
    }else{
      console.log("Geolocation permission denied")
    }
  }catch(err){
    console.warn(err)
  }
}

requestGeolocationPermission(); 
```

Enter fullscreen mode Exit fullscreen mode

隐藏标题。`Home`页面不需要:

```
export default class Home extends Component {
  static navigationOptions = {
    header: null,
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

设置加载器的默认状态(用于控制`ActivityIndicator`的可见性)和用户名:

```
state = {
  is_loading: false,
  username: ''
} 
```

Enter fullscreen mode Exit fullscreen mode

呈现`Home`页面。在此页面中，我们有:

*   要求输入用户名的输入
*   用于共乘的按钮
*   搭顺风车的按钮

注意，我们使用`KeyboardAvoidingView`作为包装器。这样，当屏幕上的键盘变得可见时，里面的一切都会相应地调整:

```
render() {

  return (
    <KeyboardAvoidingView style={styles.container} behavior="padding" enabled>
      <View style={styles.jumbo_container}>
        <Icon name="question-circle" size={35} color="#464646" />
        <Text style={styles.jumbo_text}>What do you want to do?</Text>
      </View>

      <View>
        <TextInput
          placeholder="Enter your username"
          style={styles.text_field}
          onChangeText={(username) => this.setState({username})}
          value={this.state.username}
          clearButtonMode={"always"}
          returnKeyType={"done"}
        />
        <ActivityIndicator size="small" color="#007ff5" style={{marginTop: 10}} animating={this.state.is_loading} />
      </View>

      <View style={styles.close_container}>
        <Tapper
          title="Share a Ride"
          color="#007ff5"
          onPress={() => {
            this.enterUser('share');
          }}
        />

        <Tapper 
          title="Hitch a Ride" 
          color="#00bcf5" 
          onPress={() => {
            this.enterUser('hike');
          }} 
        />
      </View>

    </KeyboardAvoidingView>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

当按下任一按钮时，将执行以下功能。它所做的就是创建用户，如果他们还不存在的话:

```
enterUser = (action) => {
  if(this.state.username){ // user should enter a username before they can enter

    this.setState({
      is_loading: true
    });

    // make a POST request to the server for creating the user
    axios.post(`${base_url}/save-user.php`, {
      username: this.state.username // the username entered in the text field
    })
    .then((response) => {

      if(response.data == 'ok'){
        // hide the ActivityIndicator
        this.setState({
          is_loading: false
        });

        // navigate to the Map page, submitting the user's action (ride or hike) and their username as a navigation param (so it becomes available on the Map page)
        this.props.navigation.navigate('Map', {
          action: action,
          username: this.state.username
        });
      }

    });

  }else{
    Alert.alert(
      'Username required',
      'Please enter a username'
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为主页添加样式:

```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'space-around'
  },
  jumbo_container: {
    padding: 50,
    alignItems: 'center'
  },
  jumbo_text: {
    marginTop: 20,
    textAlign: 'center',
    fontSize: 25,
    fontWeight: 'bold'
  },
  text_field: {
    width: 200,
    height: 50,
    padding: 10,
    backgroundColor: '#FFF', 
    borderColor: 'gray', 
    borderWidth: 1
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 地图页面

地图页面包含应用程序的主要内容。这允许用户共享或搜索乘车。位置跟踪是通过谷歌地图、推送频道和 React Native 的地理定位功能实现的。

从包含我们需要的所有 React 原生包开始:

```
// Ridesharer/app/screens/Map.js
import React, { Component } from 'react';
import { 
  View, 
  Text, 
  StyleSheet, 
  Alert, 
  Dimensions, 
  ActivityIndicator
} from 'react-native'; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，包含我们之前安装的包:

```
import { GooglePlacesAutocomplete } from 'react-native-google-places-autocomplete';
import MapView, { Marker, Callout } from 'react-native-maps';
import MapViewDirections from 'react-native-maps-directions';
import Icon from 'react-native-vector-icons/FontAwesome';
import Pusher from 'pusher-js/react-native'; 
import Geocoder from 'react-native-geocoding';
import axios from 'axios'; 
```

Enter fullscreen mode Exit fullscreen mode

包括位置库。我们将稍后创建它，但是现在，知道这些函数用于正确地将地图居中(`regionFrom()`)并获得两个以米为单位的坐标的差(`getLatLonDiffInMeters()` ):

```
import { regionFrom, getLatLonDiffInMeters } from '../lib/location';
import Tapper from '../components/Tapper'; 
```

Enter fullscreen mode Exit fullscreen mode

初始化您的 API 密钥和 ngrok 基本 URL:

```
const google_api_key = 'YOUR GOOGLE PROJECT API KEY';
const base_url = 'YOUR NGROK BASE URL';
const pusher_app_key = 'YOUR PUSHER APP KEY';
const pusher_app_cluster = 'YOUR PUSHER APP CLUSTER';

Geocoder.init(google_api_key); // initialize the geocoder 
```

Enter fullscreen mode Exit fullscreen mode

接下来，还要声明搜索和共享乘车的超时。如果应用程序无法在这些超时时间内匹配两个用户，我们稍后将使用这些值来重置应用程序的 UI:

```
const search_timeout = 1000 * 60 * 10; // 10 minutes
const share_timeout = 1000 * 60 * 5; // 5 minutes 
```

Enter fullscreen mode Exit fullscreen mode

设置地图将显示的默认区域:

```
const default_region = {
  latitude: 37.78825,
  longitude: -122.4324,
  latitudeDelta: 0.0922,
  longitudeDelta: 0.0421,
}; 
```

Enter fullscreen mode Exit fullscreen mode

获取设备宽度。我们稍后将使用它来设置搜索地点的自动完成文本字段的宽度:

```
var device_width = Dimensions.get('window').width; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建`Map`组件并设置`navigationOptions`。与前面的`Home`页面不同，我们需要为导航设置一些选项。这包括页眉标题和应用于它的样式。放置这些导航选项会自动在标题上添加一个后退按钮，允许用户返回到`Home`页面:

```
export default class Map extends Component {

  static navigationOptions = ({navigation}) => ({
    headerTitle: 'Map',
    headerStyle: {
      backgroundColor: '#007ff5'
    },
    headerTitleStyle: {
      color: '#FFF'
    }
  });

  // next: add the code for initializing the state
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，初始化状态:

```
state = {
  start_location: null, // the coordinates (latitude and longitude values) of the user's origin
  end_location: null, // the coordinates of the user's destination
  region: default_region, // the region displayed in the map
  from: '', // the name of the place where the user is from (origin)
  to: '', // the name of the place where the user is going (destination)
  rider_location: null, // the coordinates of the rider's current location
  hiker_location: null, // the coordinates of the hiker's origin
  is_loading: false, // for controlling the visibility of the ActivityIndicator
  has_journey: false // whether the rider has accepted a hiker's request or a hiker's request has been accepted by a rider 
}

// next: add the constructor 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加构造函数:

```
constructor(props) {
  super(props);
  this.from_region = null;
  this.watchId = null; // unique ID for the geolocation watcher. Storing it in a variable allows us to stop it at a later time (for example: when the user is done using the app)
  this.pusher = null; // variable for storing the Pusher instance
  this.user_channel = null; // the Pusher channel for the current user
  this.journey_id = null; // the hiker's route ID
  this.riders_channel = []; // if current user is a hiker, the value of this will be the riders channel
  this.users_channel = null; // the current user's channel
  this.hiker = null // for storing the hiker's origin coordinates; primarily used for getting the distance between the rider and the hiker
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦组件被挂载，您就想要获取之前从主页传递过来的`username`。此`username`稍后用作唯一键，用于识别连接到推送通道的每个用户:

```
componentDidMount() {
  const { navigation } = this.props;
  const username = navigation.getParam('username');

  this.pusher = new Pusher(pusher_app_key, {
    authEndpoint: `${base_url}/pusher-auth.php`,
    cluster: pusher_app_cluster,
    encrypted: true
  });  

  // next: add the code for subscribing to the current user's own channel
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加订阅当前用户自己的频道的代码。这允许用户通过该信道实时发送和接收数据。在徒步旅行者的例子中，他们用它向匹配的骑手发出请求。在骑手的例子中，他们使用它来接收来自徒步旅行者的请求，并向徒步旅行者发送接受和他们的当前位置:

```
this.users_channel = this.pusher.subscribe(`private-user-${username}`); // note that the private-* is required when using private channels 
```

Enter fullscreen mode Exit fullscreen mode

当骑手收到请求时，执行下面的代码。这提醒骑手有人想和他们一起骑。他们可以接受也可以拒绝:

```
this.users_channel.bind('client-rider-request', (hiker) => {

  Alert.alert(
    `${hiker.username} wants to ride with you`,
    `Pickup: ${hiker.origin} \nDrop off: ${hiker.dest}`,
    [
      {
        text: "Decline",
        onPress: () => {
          // do nothing
        },
        style: "cancel"
      },
      {
        text: "Accept", 
        onPress: () => {
          this.acceptRide(hiker);
        }
      },
    ],
    { cancelable: false } // no cancel button
  );

});

// next: add code for getting the user's origin 
```

Enter fullscreen mode Exit fullscreen mode

注意，在上面的代码中，我们并没有真正处理拒绝。这是为了保持对应用程序关键功能的关注。

接下来，通过地理定位 API 获取用户的当前位置。此时，我们已经可以毫无问题地使用该 API 了(除非用户没有批准该权限)。为了简单起见，我们将注意力集中在“快乐之路”上，所以我们假设用户批准了权限请求:

```
navigator.geolocation.getCurrentPosition(
  (position) => {
    // get the region (this return the latitude and longitude delta values to be used by React Native Maps)
    var region = regionFrom(
      position.coords.latitude, 
      position.coords.longitude, 
      position.coords.accuracy
    );

    // convert the coordinates to the descriptive name of the place
    Geocoder.from({
      latitude: position.coords.latitude,
      longitude: position.coords.longitude
    })
    .then((response) => {
      // the response object is the same as what's returned in the HTTP API: https://developers.google.com/maps/documentation/geocoding/intro

      this.from_region = region; // for storing the region in case the user presses the "reset" button

      // update the state to indicate the user's origin on the map (using a marker)
      this.setState({
        start_location: {
          latitude: position.coords.latitude,
          longitude: position.coords.longitude
        },
        region: region, // the region displayed on the map
        from: response.results[0].formatted_address // the descriptive name of the place
      });

    });

  }
); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加`acceptRide()`函数。该功能在骑手接受徒步旅行者的骑行请求时执行:

```
acceptRide = (hiker) => {

  const username = this.props.navigation.getParam('username');

  let rider_data = {
    username: username,
    origin: this.state.from, // descriptive name of the rider's origin
    dest: this.state.to, // descriptive name of the rider's destination
    coords: this.state.start_location // the rider's origin coordinates
  };

  this.users_channel.trigger('client-rider-accepted', rider_data); // inform hiker that the rider accepted their request; send along the rider's info

  // make a request to delete the route so other hikers can no longer search for it (remember the 1:1 ratio for a rider to hiker?)
  axios.post(`${base_url}/delete-route.php`, {
    username: username
  })
  .then((response) => {
    console.log(response.data);
  })
  .catch((err) => {
    console.log('error excluding rider: ', err);
  });

  this.hiker = hiker; // store the hiker's info

  // update the state to stop the loading animation and show the hiker's location
  this.setState({
    is_loading: false,
    has_journey: true,
    hiker_location: hiker.origin_coords
  });

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加用于呈现 UI 的函数:

```
render() {
  const { navigation } = this.props;
  // get the navigation params passed from the Home page earlier
  const action = navigation.getParam('action'); // action is either "ride" or "hike"
  const username = navigation.getParam('username');

  let action_button_label = (action == 'share') ? 'Share Ride' : 'Search Ride';

  // next: add code for rendering the UI
} 
```

Enter fullscreen mode Exit fullscreen mode

地图用户界面包含以下内容:

*   `MapView`用于渲染地图的组件。其中包含以下内容:
    *   `Marker`组件，用于显示用户的出发地和目的地，以及显示骑手(如果用户是徒步旅行者)或徒步旅行者(如果用户是骑手)的位置。
    *   `MapViewDirections`显示当前用户从出发地到目的地的路线的组件。
*   `GooglePlacesAutocomplete`用于呈现自动完成文本字段以搜索和选择目的地的组件。
*   `ActivityIndicator`当骑手等待某人请求搭车时，或者当徒步旅行者等待应用程序找到匹配的骑手时，显示装载动画。
*   `Tapper`共享乘车或搜索乘车的组件。
*   `Tapper`用于重置选择的组件(自动完成文本字段和标记)。

```
return (
  <View style={styles.container}>

    <MapView
      style={styles.map}
      region={this.state.region}
      zoomEnabled={true}
      zoomControlEnabled={true}
    >
      {
        this.state.start_location &&
        <Marker coordinate={this.state.start_location}>
          <Callout>
            <Text>You are here</Text>
          </Callout>
        </Marker>
      }

      {
        this.state.end_location &&
        <Marker
          pinColor="#4196ea"
          coordinate={this.state.end_location}
          draggable={true}
          onDragEnd={this.tweakDestination}
        />
      }

      {
        this.state.rider_location &&
        <Marker 
          pinColor="#25a25a"
          coordinate={this.state.rider_location}
        >
          <Callout>
            <Text>Rider is here</Text>
          </Callout>
        </Marker>
      }

      {
        this.state.hiker_location &&
        <Marker 
          pinColor="#25a25a"
          coordinate={this.state.hiker_location}
        >
          <Callout>
            <Text>Hiker is here</Text>
          </Callout>
        </Marker>
      }

      {
        this.state.start_location && this.state.end_location &&
        <MapViewDirections
          origin={{
            'latitude': this.state.start_location.latitude,
            'longitude': this.state.start_location.longitude
          }}
          destination={{
            'latitude': this.state.end_location.latitude,
            'longitude': this.state.end_location.longitude
          }}
          strokeWidth={5}
          strokeColor={"#2d8cea"}
          apikey={google_api_key}
        />
      }

    </MapView>

    <View style={styles.search_field_container}>

      <GooglePlacesAutocomplete
        ref="endlocation"
        placeholder='Where do you want to go?'
        minLength={5} 
        returnKeyType={'search'} 
        listViewDisplayed='auto' 
        fetchDetails={true}            
        onPress={this.selectDestination}

        query={{
          key: google_api_key,
          language: 'en', 
        }}

        styles={{
          textInputContainer: {
            width: '100%',
            backgroundColor: '#FFF'
          },
          listView: {
            backgroundColor: '#FFF'
          }
        }}
        debounce={200} 
      />
    </View>

    <ActivityIndicator size="small" color="#007ff5" style={{marginBottom: 10}} animating={this.state.is_loading} />

    {
      !this.state.is_loading && !this.state.has_journey &&
      <View style={styles.input_container}>

        <Tapper 
          title={action_button_label}
          color={"#007ff5"}
          onPress={() => {
            this.onPressActionButton();
          }} />

        <Tapper
          title={"Reset"}
          color={"#555"}
          onPress={this.resetSelection} 
        />

      </View>
    }

  </View>
); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码应该是不言自明的。如果你不确定一个特定的道具是做什么的，组件是如何工作的，或者它期望什么样的子组件，你可以随时查看我们正在使用的软件包的 Github repo。

接下来，让我们继续讨论 UI 中使用的函数。当用户按下复位按钮时，执行`resetSelection()`。这将清空用于搜索地点的自动完成文本字段，还会更新状态，以便 UI 恢复到选择目的地之前的状态。这有效地移除了显示用户目的地的标记，以及去往目的地的路线:

```
resetSelection = () => {
  this.refs.endlocation.setAddressText('');
  this.setState({
    end_location: null,
    region: this.from_region,
    to: ''
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

当用户将目标标记放在其他地方时，执行`tweakDestination()`功能:

```
tweakDestination = () => {
  // get the name of the place
  Geocoder.from({
    latitude: evt.nativeEvent.coordinate.latitude,
    longitude: evt.nativeEvent.coordinate.longitude
  })
  .then((response) => {
    this.setState({
      to: response.results[0].formatted_address
    });
  });

  this.setState({
    end_location: evt.nativeEvent.coordinate
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

当用户选择目的地时，执行`selectDestination()`功能。此功能将更新状态，以便在地图中显示用户的目的地:

```
selectDestination = (data, details = null) => {

  const latDelta = Number(details.geometry.viewport.northeast.lat) - Number(details.geometry.viewport.southwest.lat)
  const lngDelta = Number(details.geometry.viewport.northeast.lng) - Number(details.geometry.viewport.southwest.lng)

  let region = {
    latitude: details.geometry.location.lat,
    longitude: details.geometry.location.lng,
    latitudeDelta: latDelta,
    longitudeDelta: lngDelta
  };

  this.setState({
    end_location: {
      latitude: details.geometry.location.lat,
      longitude: details.geometry.location.lng,
    },
    region: region,
    to: this.refs.endlocation.getAddressText() // get the full address of the user's destination
  });

} 
```

Enter fullscreen mode Exit fullscreen mode

当用户按下**拼车**或**搜车**按钮时，执行`onPressActionButton()`功能。这将根据之前从主页选择的动作执行`shareRide()`功能或`hikeRide()`功能:

```
onPressActionButton = () => {

  const action = this.props.navigation.getParam('action');
  const username = this.props.navigation.getParam('username');

  this.setState({
    is_loading: true
  });

  if(action == 'share'){
    this.shareRide(username);
  }else if(action == 'hike'){
    this.hikeRide(username);      
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

当乘客选择目的地后分享他们的旅程时，会执行`shareRide()`功能。这会向服务器发出保存路由的请求。该响应包含分配给骑手路线的唯一 ID。该 ID 被指定为`this.journey_id`的值。这将在以后用于:

*   请求服务器更新存储在 Elasticsearch 索引中的路线记录。
*   知道什么时候开始用当前位置数据做一些事情。这是因为在用户按下**共享乘车**按钮后，当前位置就开始被监视，您将在随后的代码块中看到:

```
shareRide = (username) => {

  axios.post(`${base_url}/save-route.php`, {
    username: username,
    from: this.state.from, 
    to: this.state.to, 
    start_location: this.state.start_location,
    end_location: this.state.end_location
  })
  .then((response) => {
    this.journey_id = response.data.id;
    Alert.alert(
      'Ride was shared!',
      'Wait until someone makes a request.'
    );
  })
  .catch((error) => {
    console.log('error occurred while saving route: ', error);
  });

  // next: add code for watching the rider's current location

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，开始观察用户的当前位置。请注意，我们实际上不会对位置数据做任何事情，除非骑手已经分享了他们的旅程，并且他们已经批准了一个徒步旅行者与他们一起乘坐。一旦两个条件都满足，我们就向服务器发出请求，用骑手的当前位置更新先前保存的路线。这样，当一个徒步旅行者搜索一个旅程时，搜索结果将会基于该旅行者的当前位置而不是他们的出发地而有所偏差:

```
this.watchId = navigator.geolocation.watchPosition(
  (position) => {

    let latitude = position.coords.latitude;
    let longitude = position.coords.longitude;
    let accuracy = position.coords.accuracy;

    if(this.journey_id && this.hiker){ // needs to have a destination and a hiker
      // update the route with the rider's current location
      axios.post(`${base_url}/update-route.php`, {
        id: this.journey_id,
        lat: latitude,
        lon: longitude
      })
      .then((response) => {
        console.log(response);
      });

      // next: add code for sending rider's current location to the hiker

    }

  },
  (error) => {
    console.log('error occured while watching position: ', error);
  },
  { 
    enableHighAccuracy: true, // get more accurate location
    timeout: 20000, // timeout after 20 seconds of not being able to get location
    maximumAge: 2000, // location has to be atleast 2 seconds old for it to be relevant
    distanceFilter: 10 // allow up to 10-meter difference from the previous location before executing the callback function again
  }
);

// last: add code for resetting the UI after 5 minutes of sharing a ride 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们向骑手自己的通道发送一个`client-rider-location`事件。稍后，我们将让徒步旅行者订阅骑手的频道(他们与之匹配的频道)，以便他们可以接收位置更新:

```
let location_data = {
  username: username,
  lat: latitude,
  lon: longitude,
  accy: accuracy 
};

this.users_channel.trigger('client-rider-locationchange', location_data); // note: client-* is required when sending client events through Pusher

// update the state so that the rider’s current location is displayed on the map and indicated with a marker
this.setState({
  region: regionFrom(latitude, longitude, accuracy),
  start_location: {
    latitude: latitude,
    longitude: longitude
  }
});

// next: add code for updating the app based on how near the rider and hiker are from each other 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要得到骑手的坐标和徒步者的起点之间的差值(以米为单位):

```
let diff_in_meters = getLatLonDiffInMeters(latitude, longitude, this.hiker.origin_coords.latitude, this.hiker.origin_coords.longitude);

if(diff_in_meters <= 20){
  this.resetUI();
}else if(diff_in_meters <= 50){
  Alert.alert(
    'Hiker is near',
    'Hiker is around 50 meters from your current location'
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加代码，用于在五分钟后没有人请求与乘客共乘时重置 UI:

```
setTimeout(() => {
  this.resetUI();
}, share_timeout); 
```

Enter fullscreen mode Exit fullscreen mode

下面是重置用户界面的代码:

```
resetUI = () => {

  this.from_region = null;
  this.watchId = null; 
  this.pusher = null; 
  this.user_channel = null; 
  this.journey_id = null;
  this.riders_channel = []; 
  this.users_channel = null; 
  this.hiker = null;

  this.setState({
    start_location: null,
    end_location: null,
    region: default_region,
    from: '',
    to: '',
    rider_location: null, 
    hiker_location: null,
    is_loading: false,
    has_journey: false
  });

  this.props.navigation.goBack(); // go back to the Home page

  Alert.alert('Awesome!', 'Thanks for using the app!');

} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来看看徒步旅行者这边的事情。当徒步旅行者按下**搜索搭车**按钮时，`hikeRide()`功能被执行。这个函数每五秒执行一次，直到它找到一个与旅行者的路线相匹配的骑手。如果 10 分钟内找不到乘客，该功能将停止。一旦服务器返回一个合适的骑手，它就用骑手的信息(用户名、起点、目的地、坐标)来响应。然后，这被用来订阅骑手的频道，以便徒步旅行者可以请求搭车并接收位置更新。请注意，这是自动完成的，因此徒步旅行者无法控制他们与谁共乘:

```
hikeRide = (username) => {

  var interval = setInterval(() => {
    // make a request to the server to get riders that matches the hiker's route
    axios.post(`${base_url}/search-routes.php`, {
      origin: this.state.start_location,
      dest: this.state.end_location
    })
    .then((response) => {

      if(response.data){

        clearInterval(interval); // assumes the rider will accept the request

        let rider = response.data; // the rider's info

        // subscribe to the rider's channel so the hiker can make a request and receive updates from the rider
        this.riders_channel = this.pusher.subscribe(`private-user-${rider.username}`);

        this.riders_channel.bind('pusher:subscription_succeeded', () => {
          // when subscription succeeds, make a request to the rider to share the ride with them
          this.riders_channel.trigger('client-rider-request', {
            username: username, // username of the hiker
            origin: this.state.from, // descriptive name of the hiker's origin
            dest: this.state.to, // descriptive name of the hiker's destination
            origin_coords: this.state.start_location // coordinates of the hiker's origin
          });
        });

        // next: add code for listening for when the rider accepts their request
      }      
    })
    .catch((error) => {
      console.log('error occurred while searching routes: ', error);
    });

  }, 5000);

  setTimeout(() => {
    clearInterval(interval);
    this.resetUI();
  }, ten_minutes);

} 
```

Enter fullscreen mode Exit fullscreen mode

一旦乘客接受乘坐请求，将执行以下功能:

```
this.riders_channel.bind('client-rider-accepted', (rider_data) => {
  Alert.alert(
    `${rider_data.username} accepted your request`,
    `You will now receive updates of their current location`
  );

  // update the map to show the rider's origin
  this.setState({
    is_loading: false,
    has_journey: true,
    rider_location: rider_data.coords
  });

  // next: add code for subscribing to the rider's location change
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如您之前看到的，当骑手的位置改变时，它会触发一个名为`client-rider-location-change`的事件。订阅骑手频道并收听该事件的任何用户将实时获得位置数据:

```
this.riders_channel.bind('client-rider-locationchange', (data) => {
  // update the map with the rider's current location
  this.setState({
    region: regionFrom(data.lat, data.lon, data.accy),
    rider_location: {
      latitude: data.lat,
      longitude: data.lon
    }
  });

  let hikers_origin = this.state.start_location;
  let diff_in_meters = getLatLonDiffInMeters(data.lat, data.lon, hikers_origin.latitude, hikers_origin.longitude);

  if(diff_in_meters <= 20){
    this.resetUI();
  }else if(diff_in_meters <= 50){
    Alert.alert(
      'Rider is near',
      'Rider is around 50 meters from your location'
    );
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

为`Map`页面添加样式:

```
const styles = StyleSheet.create({
  container: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    justifyContent: 'flex-end',
    alignItems: 'center',
  },
  map: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
  },
  search_field_container: {
    height: 150, 
    width: device_width, 
    position: 'absolute', 
    top: 10
  },
  input_container: {
    alignSelf: 'center',
    backgroundColor: '#FFF',
    opacity: 0.80,
    marginBottom: 25
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 位置库

下面是获取纬度和经度增量值的代码。从前面的代码中可以看出，这个函数主要用于获取地图上显示的区域:

```
// Ridesharer/app/lib/location.js
export function regionFrom(lat, lon, accuracy) {
  const oneDegreeOfLongitudeInMeters = 111.32 * 1000;
  const circumference = (40075 / 360) * 1000;

  const latDelta = accuracy * (1 / (Math.cos(lat) * circumference));
  const lonDelta = (accuracy / oneDegreeOfLongitudeInMeters);

  return {
    latitude: lat,
    longitude: lon,
    latitudeDelta: Math.max(0, latDelta),
    longitudeDelta: Math.max(0, lonDelta)
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这是获取两个坐标之间的差值(以米为单位)的函数。这主要用于当用户已经彼此靠近时通知用户，并且当用户已经彼此非常靠近时重置应用 UI:

```
export function getLatLonDiffInMeters(lat1, lon1, lat2, lon2) {
  var R = 6371; // radius of the earth in km
  var dLat = deg2rad(lat2-lat1);  // deg2rad below
  var dLon = deg2rad(lon2-lon1); 
  var a = 
    Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * 
    Math.sin(dLon/2) * Math.sin(dLon/2)
    ; 
  var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
  var d = R * c; // distance in km
  return d * 1000;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面使用的`deg2rad()`函数将度数转换成弧度:

```
function deg2rad(deg) {
  return deg * (Math.PI/180)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 app

在 Android 上运行应用程序之前，您需要确保安装了以下 Android SDK 软件包，您可以在 SDK 管理器的 SDK 工具下找到这些软件包:

*   Google Play 服务
*   Android 支持库
*   谷歌知识库

如果你要在 Genymotion 上测试这款应用，你需要先安装 Google Play 服务。由于该应用程序正在使用谷歌地图，您需要 Google Play 服务才能使用该功能。如果您有 2.10 或更高版本，他们提供了一个简单的安装方法。只需在运行的仿真器实例上点击**打开 GAPPS** ，并通过安装向导。之后，重新启动设备，您应该可以开始工作了:

[![Install Google Play services on Genymotion](img/72a65c3fa59275098218db829cc12811.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vmXWehBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_580926EA6EB1AC90CFCFCE0B11788E0357238DBCBDFB8D5580B132E172C4DEE5_1527413690547_genymotion-google-play-services.png)

要在 Android 上运行应用程序，请执行以下命令。这将在一个打开的仿真器实例(例如:Genymotion)或一个 Android 设备(如果你已经连接了一个)上运行应用程序:

```
react-native run-android 
```

Enter fullscreen mode Exit fullscreen mode

如果你在让应用程序在 Android 上运行时遇到了问题，请一定要查看我关于在 Android 上调试常见的 React 原生问题的文章。

对于 iOS，你只需要确保你安装了最新版本的 Xcode。注意，如果你想在设备上运行应用程序，你只能通过 Xcode 打开`.xcworkspace`文件。

若要在 iOS 设备上运行该应用程序，请在 Xcode 上选择您的设备，然后点按大播放按钮。

要在 iOS 模拟器中运行应用程序，您也可以使用上面的方法通过 Xcode 来完成。但是如果您想从终端运行它，您可以从项目的根目录执行下面的命令:

```
react-native run-ios 
```

Enter fullscreen mode Exit fullscreen mode

如果你想在一个特定的模拟器上运行应用程序，你首先要列出哪些设备是可用的:

```
xcrun simctl list devicetypes 
```

Enter fullscreen mode Exit fullscreen mode

这将返回设备列表:

[![Device types](img/85601e0e7f181a76de4079c8f111f7ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7U60DiH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_580926EA6EB1AC90CFCFCE0B11788E0357238DBCBDFB8D5580B132E172C4DEE5_1528176021930_device-types.png)

然后，您可以复制设备名称(例如:iPhone 5s)并将其指定为`--simulator`选项的值:

```
react-native run-ios --simulator="iPhone 5s" 
```

Enter fullscreen mode Exit fullscreen mode

如果你在 iOS 模拟器或设备上运行该应用有问题，请务必查看我的文章《在 iOS 上调试常见的 React 原生问题》。

## 结论

就是这样！在本系列中，您已经学习了如何使用 React Native 创建拼车应用程序。在此过程中，您还学到了以下内容:

*   如何使用 axios 向服务器发出请求？
*   如何使用 React Native 的地理定位功能？
*   如何在 Genymotion 中添加 Google Play 服务？
*   如何使用 Genymotion 的 GPS 仿真工具？
*   如何使用推手通道？
*   如何使用谷歌的地理编码 API？

你可以在这个 [GitHub](https://github.com/anchetaWern/Ridesharer) [repo](https://github.com/anchetaWern/Ridesharer) 上找到本系列使用的所有代码。

*原载于[推杆教程枢纽](https://pusher.com/tutorials/carpooling-react-native-part-2)。*