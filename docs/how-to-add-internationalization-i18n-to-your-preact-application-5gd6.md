# 如何将国际化(i18n)添加到 Preact 应用程序中

> 原文：<https://dev.to/henrylim96/how-to-add-internationalization-i18n-to-your-preact-application-5gd6>

🇹🇼 中文版(中文版):[https://dev.to/henrylim96/i18n-preact-3pie](https://dev.to/henrylim96/i18n-preact-3pie)

* * *

## 什么是国际化(i18n)？

> **国际化**是指产品、应用程序或文档内容的设计和开发，能够为不同文化、地区或语言的目标受众轻松实现本地化。

在本文中，您将使用`preact-i18n`库为您的 Preact 应用程序添加国际化。

* * *

## 步骤 1:设置预动作客户端&创建新项目

> 附注:如果您已经熟悉 Preact，您可以跳到下一步。

如果您的计算机上尚未安装 Preact CLI，请使用以下命令安装 CLI。确保安装了 Node.js 6.x 或更高版本。

`$ npm install -g preact-cli`

安装 Preact CLI 后，让我们使用`default`模板创建一个新项目，并将其命名为`my-project`。

`$ preact create default my-project`

使用下面的命令启动开发服务器:

`$ cd my-project && npm run start`

现在，打开您的浏览器并转到`http://localhost:8080`，您应该会在屏幕上看到类似这样的内容:

[![](img/90c82dca3c160d94cd37d4f3118e1786.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zainKUDk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kk6nzdsfzs4nzrlpahvx.jpg)

## 第二步:添加 preact-i18n 库

使用下面的命令将`preact-i18n`库安装到您的项目中:

`$ npm install --save preact-i18n`

非常容易使用，最重要的是，它非常小，压缩后只有 1.3kb 左右。你可以在这里了解更多关于图书馆的信息:[https://github.com/synacor/preact-i18n](https://github.com/synacor/preact-i18n)

## 第三步:创建定义文件

一旦安装了这个库，就需要创建一个定义文件，将所有翻译字符串存储在一个 JSON 文件中。

在这种情况下，您需要将该文件保存在`src/i18n/zh-tw.json` :
中

```
{  "home":  {  "title":  "主頁",  "text":  "這是個 Home 組件。"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:导入 IntlProvider 和定义文件

接下来，打开位于`src/components`文件夹中的`app.js`文件。然后，将`IntlProvider`和你的`definition`文件导入到`app.js`文件:

```
import { IntlProvider } from 'preact-i18n';
import definition from '../i18n/zh-tw.json'; 
```

Enter fullscreen mode Exit fullscreen mode

## 第五步:通过 IntlProvider 公开定义

之后，您需要通过`<IntlProvider>`将定义文件暴露给整个应用程序。通过这样做，你将能够在应用程序的任何地方读取定义文件。

```
render() {
  return(
    <IntlProvider definition={definition}>
      <div id="app" />
    </IntlProvider>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，你的`app.js`文件应该是这样的:

```
import { h, Component } from 'preact';
import { Router } from 'preact-router';
import Header from './header';
import Home from '../routes/home';
import Profile from '../routes/profile';
// Import IntlProvider and the definition file.
import { IntlProvider } from 'preact-i18n';
import definition from '../i18n/zh-tw.json';
export default class App extends Component {

 handleRoute = e => {
  this.currentUrl = e.url;
 };
render() {
  return (
   // Expose the definition to your whole app via <IntlProvider>
   <IntlProvider definition={definition}>
    <div id="app">
     <Header />
     <Router onChange={this.handleRoute}>
      <Home path="/" />
      <Profile path="/profile/" user="me" />
      <Profile path="/profile/:user" />
     </Router>
    </div>
   </IntlProvider>
  );
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第六步:使用文本翻译字符串文字

你差不多完成了，现在你只需要用`<Text>`替换页面中的文本。在这种情况下，您需要通过在`<h1>`和`<p>`标签中添加`<Text>`来更新主页(`src/routes/home/index.js`)的内容。

```
import { Text } from 'preact-i18n';
const Home = () => ( 
  <div> 
    <h1> 
      <Text id="home.title">Home</Text> 
    </h1> 
    <p> 
      <Text id="home.text">This is the Home component.</Text> 
    </p> 
  </div> 
); 
export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/f17b44ad20d39dfe3653e76287744896.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--asW3wf_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vsgnzsjx6bdg25inkuvj.jpg)

## 回退文本

为了防止页面中出现空白文本，您应该为`<Text>`设置一个回退文本。如果没有包含`unknown.definition`的定义，库会将`<Text>…</Text>`中包含的任何文本作为后备文本:

```
<Text id="unknown.definition">This is a fallback text.</Text>
// It will render this text: "This is a fallback text." 
```

Enter fullscreen mode Exit fullscreen mode

## 定位器和标记文本

如果你想翻译 HTML 属性值的文本(即:`placeholder=""`、`title=""`等…)，那么你将需要使用`<Localizer>`而不是`<Text>`。

但是，如果您想在呈现的字符串中包含 HTML 标记，那么您将需要使用`<MarkupText>`。使用这个组件，您的文本将呈现在一个`<span>`标签中。

在下面的例子中，您将在您的定义文件中添加几行代码。`first_name`和`last_name`将用于`<Localizer>`的例子，`link`用于`<MarkupText>`的例子。

```
{  "first_name":  "名",  "last_name":  "姓",  "link":  "這是個<a href='https://www.google.com'>連結</a>"  } 
```

Enter fullscreen mode Exit fullscreen mode

这样，你就可以在页面中使用`<Localizer>`和`<MarkupText>`。请注意，您需要将`Localizer`和`MarkupText`导入到`src/routes/home/index.js`文件中。

```
import { Text, Localizer, MarkupText } from 'preact-i18n';
const Home = () => ( 
  <div> 
    <Localizer> 
      <input placeholder={<Text id="first_name" />} /> 
    </Localizer> 
    <Localizer> 
      <input placeholder={<Text id="last_name" />} /> 
    </Localizer> 
    <MarkupText id="link"> 
      This is a <a href="https://www.google.com">link</a>
    </MarkupText>
  </div>
);
export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/a0db794272be581c56e16822fec46d13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---wJtsmtp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/opgth3dzvj3mqknojxha.jpg)

## 模板化

如果您想在定义中注入一个定制的字符串或值，您可以使用`fields`属性来完成。

首先，您需要用`{{field}}`占位符更新定义文件。占位符将被替换为您在`fields`道具中传递的对象中的匹配键。

```
{  "page":  "{{count}} / {{total}} 頁"  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要将`fields`属性和值一起添加到`<Text />`中。因此，你的代码应该是这样的:

```
import { Text } from 'preact-i18n'; 
const Home = () => ( 
  <div>
    <h2> 
      <Text id="page" fields={{ count: 5, total: 10 }}>
         5 / 10 Pages
      </Text> 
    </h2> 
  </div> 
); 
export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/de271f3c9c31773088f813adf6da32ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h29051AW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/heuay1pm410dxt5e7jbk.jpg)

## 复数化

使用`preact-i18n`，您有 3 种方法来指定多元化值:

*   `"key": { "singular":"apple", "plural":"apples" }`
*   `"key": { "none":"no apples", "one":"apple", "many":"apples" }`
*   `"key": ["apples", "apple"]`

在下一个例子中，您将结合使用复数和模板。首先，您需要用下面的代码更新定义文件:

```
{  "apple":  {  "singular":  "Henry has {{count}} apple.",  "plural":"Henry has {{count}} apples."  }  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您将使用下面的代码更新主页(`src/routes/home/index.js`):

```
import { Text } from 'preact-i18n'; 
const Home = () => ( 
  <div> 
    <p> 
      <Text id="apple" plural={1} fields={{ count: 1 }} /> 
    </p> 
    <p> 
      <Text id="apple" plural={100} fields={{ count: 100 }} /> 
    </p> 
  </div> 
); 
export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/a538deb3160a783fa1afbcac24176a3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L3VpQWt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rqvabjs585uxp2ywggpm.jpg)

使用上面的方法，你将能够给你的 Preact 应用程序添加复数和模板。

* * *

## 动态导入语言定义文件

在一个真实的场景中，您希望根据用户的选择来设置语言站点，这可以基于`navigator.language`或者用户可以自己更改站点语言。

但是，为了防止您将所有不必要的定义文件导入到项目中，您可以使用`import()`动态导入语言定义文件。通过这样做，您可以根据用户的选择导入语言定义文件。

```
import { Component } from 'preact'; 
import { IntlProvider } from 'preact-i18n'; 
import defaultDefinition from '../i18n/zh-tw.json'; 
export default class App extends Component { 
  state = { 
    definition: defaultDefinition 
  } 
  changeLanguage = (lang) => { 
    // Call this function to change language 
    import(`../i18n/${lang}.json`) 
      .then(definition => this.setState({ definition })); 
  }; 
  render({ }, { definition }) { 
    return ( 
      <IntlProvider definition={definition}> 
        <div id="app" /> 
      </IntlProvider> 
    ); 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，您可以调用`this.changeLanguage('zh-TW')`函数来更改站点语言。

* * *

## 谁在用 preact-i18n？

我用`preact-i18n`做我的副业:遥控幻灯片。

Remote for Slides 是一个渐进式的 Web 应用+ Chrome 扩展，允许用户在任何设备上远程控制他们的谷歌幻灯片，而不需要任何额外的硬件。

remote for Slides Progressive Web App 支持超过 8 种语言，包括:加泰罗尼亚语、英语、西班牙语、尤斯克拉语、法语、波尔斯基语、繁体中文和简体中文。

在这个项目中，我使用了前面提到的“动态导入语言定义文件”的方法。这可以防止 web 应用程序加载一些不必要的定义语言文件，从而提高页面性能。

此外，幻灯片渐进式 Web 应用程序的遥控器将根据浏览器的语言(`navigator.language`)或根据 URL 参数(即:[【s.limhenry.xyz/?hl=zh-tw】](https://slides.limhenry.xyz/?hl=zh-tw))来设置语言，或者用户可以从[设置页面](https://slides.limhenry.xyz/settings/language)进行更改。

[![](img/e89d88046dfbf23fce83f8394dd9e9f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vTEJ6cEX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wxgg59lyyisatdza3pgl.jpg)

您可以在此了解有关幻灯片远程控制的更多信息:

[![henrylim96](img/ae689637ccf7d27dbf9cec545c27ae4d.png)](/henrylim96) [## 幻灯片远程会议，控制演示文稿幻灯片的新方法

### Henry Lim Apr 2 ' 192 分钟阅读

#pwa #web #android #googleslides](/henrylim96/meet-remote-for-slides-a-new-way-to-control-your-presentation-slides-1o7n)

* * *

## 资源

*   [preact-i18n](https://github.com/synacor/preact-i18n)
*   [预测客户端](https://github.com/preactjs/preact-cli)
*   [预先行动](https://preactjs.com/)