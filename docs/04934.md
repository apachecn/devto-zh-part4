# 通过 Vue.js 使用地理定位

> 原文：<https://dev.to/raymondcamden/using-geolocation-with-vue-js-51cf>

我决定用我慵懒的周日早晨写一篇快速的 Vue.js 帖子。地理定位是你可以在你的网络浏览器中使用的较老和较简单的 API 之一，所以这篇文章不一定会很激动人心，但是我认为一个关于 Vue 的 API 的快速演示，以及一些变化，可能对人们有用。提醒一下，使用地理定位**的网页必须**在本地主机或 https 服务器上运行。这是一种安全预防措施，而且……老实说——在 2019 年，没有**零**理由使用不安全的服务器。

## 例一

对于第一个示例，让我们构建一个简单的 Vue 应用程序，它将:

*   自动尝试获取您的位置
*   发生这种情况时，显示“正在加载”类型的消息
*   并适当地支持错误条件。

首先我们将构建前端:

```
<div id="app" v-cloak>

  <div v-if="errorStr">
    Sorry, but the following error
    occurred: {{errorStr}}
  </div>

  <div v-if="gettingLocation">
    <i>Getting your location...</i>
  </div>

  <div v-if="location">
    Your location data is {{ location.coords.latitude }}, {{ location.coords.longitude}}
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

我这里有三个 div。第一个句柄显示一个错误。第二个是加载消息。最后一个 div 显示我们的位置。现在让我们看看代码。

```
const app = new Vue({
  el:'#app',
  data:{
    location:null,
    gettingLocation: false,
    errorStr:null
  },
  created() {
    //do we support geolocation
    if(!("geolocation" in navigator)) {
      this.errorStr = 'Geolocation is not available.';
      return;
    }

    this.gettingLocation = true;
    // get position
    navigator.geolocation.getCurrentPosition(pos => {
      this.gettingLocation = false;
      this.location = pos;
    }, err => {
      this.gettingLocation = false;
      this.errorStr = err.message;
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我正在使用`created`方法，一旦应用程序准备好，就开始请求位置。我做了一个快速检查，看看这个 API 是否受支持。之后，我简单地使用 API。这非常简单，但即使是这段代码也可以改进。你会注意到我的前端将结果寻址为`location.coords.latitude`。如果我知道我只需要纬度和经度，我可以把这些值复制出来。我的前端代码可能看起来像这样:

```
Your location data is {{ latitude }}, {{ longitude}} 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这样更好，因为布局代码更简单，并且不直接依赖于地理定位 API 的使用。你可以在这里玩这个例子:

[https://codepen.io/cfjedimaster/embed/ZEzJwZN?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/ZEzJwZN?height=600&default-tab=result&embed-version=2)

## 例二

在我的下一个例子中，我将改变代码，使它在用户真正需要时才请求您的位置。在这种情况下，我将使用一个简单的按钮来启动该过程。下面是 HTML:

```
<div id="app" v-cloak>

  <p>
    Let us locate you for better results...
    <button @click="locateMe">Get location</button>
  </p>

  <div v-if="errorStr">
    Sorry, but the following error
    occurred: {{errorStr}}
  </div>

  <div v-if="gettingLocation">
    <i>Getting your location...</i>
  </div>

  <div v-if="location">
    Your location data is {{ location.coords.latitude }}, {{ location.coords.longitude}}
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

上面的大部分布局是相同的，除了段落和顶部的按钮。对于代码，我决定抽象一些东西。按钮引用的`locateMe`方法将会更简单，因为我已经移植了地理定位的东西。让我们来看看。

```
const app = new Vue({
  el:'#app',
  data:{
    location:null,
    gettingLocation: false,
    errorStr:null
  },
  methods: {
    async getLocation() {

      return new Promise((resolve, reject) => {

        if(!("geolocation" in navigator)) {
          reject(new Error('Geolocation is not available.'));
        }

        navigator.geolocation.getCurrentPosition(pos => {
          resolve(pos);
        }, err => {
          reject(err);
        });

      });
    },
    async locateMe() {

      this.gettingLocation = true;
      try {
        this.gettingLocation = false;
        this.location = await this.getLocation();
      } catch(e) {
        this.gettingLocation = false;
        this.errorStr = e.message;
      }

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你把注意力集中在`locateMe`上，你会发现它要简单得多。我用`async`和`await`来称呼`getLocation`。我的方法处理加载屏幕、错误和结果之类的事情，但是位置请求的实际机制现在被抽象掉了。`getLocation`利用承诺与`async`和`await`正常工作，但除此之外，几乎和以前一样。

您可以在此处测试该版本:

[https://codepen.io/cfjedimaster/embed/jONLdgQ?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/jONLdgQ?height=600&default-tab=result&embed-version=2)

## 选项三

最后一个例子，让我们做一些有趣的位置。大部分人都不能把一个经纬度翻译成有用的东西。如果我们可以使用反向地理编码(这是尝试将纬度/经度映射到有名称的地方的过程)来以更友好的名称显示用户的位置，那将会更酷。对于这个例子，我将在这里使用[地理编码 API](https://developer.here.com/signup/geocoding) 。免责声明-我上周开始在这里工作，所以我在谈论我的雇主的产品。这个 API(以及更多)有一个免费层，所以你可以随心所欲地使用它们！

这个 API 相当广泛(你可以在这里看到文档)，但是我将把重点放在最简单的例子上。首先，我在我的 HERE 帐户中创建了一个新的 JavaScript 项目。这给了我一个 API 键，我可以在我的代码中使用它。我在这里添加了两个 JavaScript 库，然后是这段初始化代码:

```
const platform = new H.service.Platform({
  'apikey': 'iEnZe8bO68AnNVZEdPpq7hl9UFqiPxTSPjQkLfR3Qcg'
});
const geocoder = platform.getGeocodingService(); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您可以为您的 API 键指定一个域白名单，这将使上面的代码对您的公共 web 页面非常安全。一旦你配置了你的地理编码器，要进行反向地理编码，你可以简单地这样做(伪代码):

```
let reverseGeocodingParameters = {
    prox: 'Latiude,Longitude', // not literaly that, but the real values
    mode: 'retrieveAddresses',
    maxresults: 1
};

geocoder.reverseGeocode(
    reverseGeocodingParameters,
    res => {
       // work with results
    },
    e => reject(e) 
); 
```

Enter fullscreen mode Exit fullscreen mode

下面是更新后的`getLocation` :
的 JavaScript

```
async getLocation() {

    return new Promise((resolve, reject) => {

    if(!("geolocation" in navigator)) {
        reject(new Error('Geolocation is not available.'));
    }

    navigator.geolocation.getCurrentPosition(pos => {
        let reverseGeocodingParameters = {
            prox: `${pos.coords.latitude},${pos.coords.longitude}`,
            mode: 'retrieveAddresses',
            maxresults: 1
        };

        geocoder.reverseGeocode(
        reverseGeocodingParameters,
        res => {
            let results = res.Response.View;
            if(results.length === 0) {
                resolve('No match.')
            } else {
                resolve(results[0].Result[0].Location);
            }
        },
        e => reject(e) 
        );
    }, err => {
        reject(err);
    });

    });
}, 
```

Enter fullscreen mode Exit fullscreen mode

在很大程度上，这只是对上一个例子的简单更新，但是请注意，当我离开这个函数时，我会“深入”到地理编码器结果中，以稍微简化一下:`resolve(results[0].Result[0].Location);`

HTML 现在使用这个:

```
<div v-if="location">
    Your location data is {{ location.Address.Label }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果你还记得我说过的选项一，我不喜欢我的 HTML 有太多关于数据的知识，所以一个更好的解决方案可能只是存储`Address.Label`到`location`。您可以在这里运行:

[https://codepen.io/cfjedimaster/embed/wvwqOMx?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/wvwqOMx?height=600&default-tab=result&embed-version=2)

一如既往，让我知道你的想法，并在下面的评论中提出任何问题。Vue 组件也有多种选择，可以简单地为您定位。一个是[vue-浏览器-地理定位](https://www.npmjs.com/package/vue-browser-geolocation)。

*标题图片由[宝拉·梅](https://unsplash.com/@paulamayphotography?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash* 上拍摄