# 在 IPFS 上构建一个零依赖 Notes 应用程序——第一部分

> 原文：<https://dev.to/polluterofminds/build-a-zero-dependency-notes-app-on-ipfs-part-i-4noc>

这是两部分教程的第一部分。第一部分关注认证和 [*第二部分*](https://medium.com/simpleid-dev-tools/tutorial-build-an-encrypted-notes-app-on-ipfs-part-ii-3bdba2d867ad) *关注从 IPFS 发布和获取内容。*

[![](img/3e35c937b697caf38d105a08e0bff5bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uBz5Wv1j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/0%2AvtzWVF2hGeBNIsEl.png)

新兴的 Web 3.0 技术预示着用户控制权的回归——不仅仅是数据，还有互联网。曾经有一段时间，我们使用的网络不是由寡头垄断的。这是在我们脱离了美国在线的围墙花园模式之后。我们变得自由，联系，参与。但随后，事情又开始接近了。亚马逊玫瑰。谷歌成了权力真空。脸书决定用 AOL 2.0。现在，有了去中心化的网络技术，我们有机会夺回曾经拥有的网络。IPFS 只是 Web 3.0 空间中的一个解决方案，但它让我们看到了可能的可能性。今天，我们将构建一个简单的 notes 应用程序，但首先，让我们了解什么是 IPFS。

IPFS(星际文件系统)希望取代 http 协议成为世界获取内容的方式。http 和 IPFS 最大的区别在于，https 是基于位置的，而 IPFS 是基于内容的。但这实际上意味着什么呢？这意味着在今天的网络中，你必须从单一来源获取你的内容(越来越多的 AWS S3 桶一些公司已经站起来)。基于内容的 IPFS 意味着您可以根据文件的内容获取文件。您告诉 IPFS 对等网络，您正在寻找具有确切内容的文件，并且网络中具有该内容的第一个对等体将返回该文件。许多同行可能拥有这种内容，它比今天的网络提供了更多的审查阻力，但它也比今天的网络提供了更多的容错能力。如果我之前提到的 S3 存储桶由于任何原因变得不可访问，您就无法获取内容。但是，如果持有您在 IPFS 上查找的内容的对等点变得不可访问，网络将继续搜索，直到持有该内容的对等点可访问为止。

我们可以花一整篇文章来讨论 IPFS 做了什么以及它如何帮助网络，但是已经有很多关于 T2 的文章和关于 T4 的文章了。现在你已经对 IPFS 有了一个基本的了解，在我们开始本教程之前，我们还需要掌握一件事情:

内容锁定。

当内容被添加到 IPFS 网络，这并不意味着是永久性的。就像 90 年代的旧音乐传输服务一样，只要对内容有需求，IPFS 网络就将继续托管内容。然而，当需求下降时，这些内容可能会被垃圾收集。但是，如果您需要该文件保持可访问性，即使您是唯一访问它的人，即使您每年只访问它一次，该怎么办呢？

这就是内容锁定发挥作用的地方。您可以运行一个 IPFS 节点并自己固定内容，但是有一些缺点。a)这对于新手来说不容易做到，B)如果你在本地机器上运行该节点，除非该机器一直连接到互联网，否则你将无法访问内容。

这就是为什么内容锁定服务随着 IPFS 的崛起而发展。其中一项服务是 Pinata T1。Pinata 运行多个 IPFS 节点，并为开发人员提供一个简单的 API 来存储和获取 IPFS 上的内容。他们还会锁定内容以确保其可用性。

今天，我们将使用我自己的产品 [SimpleID](https://www.simpleid.xyz/?t=ipfs_star) 和 Pinata 在 IPFS 上构建一个零依赖的 notes 应用程序。让我们开始吧。

### 开始设置

我们将使用 SimpleID API 用普通的 JavaScript 来构建这个应用程序，以使它尽可能地为许多不同 JavaScript 框架的开发者所用。因此，您需要以下内容:

*   文本编辑器

就是这样。

让我们通过创建您的项目来进行设置。从终端，创建一个目录，无论它在你的机器的文件夹层次结构中的什么位置。对我来说，它在我的/documents/development 文件夹中。

首先，创建一个目录来存放应用程序:mkdir ipfs-notes。如果你愿意，你可以给这个应用起其他名字。

然后，切换到那个目录:cd ipfs-notes。一旦完成，我们就可以开始了。因为我们使用可以从浏览器调用的 API 端点，所以不需要安装其他东西。

现在，您需要获取一个 SimpleID API 密钥。你可以在这里注册 [SimpleID 免费计划。注册时，会要求您验证您的电子邮件地址。这样做，然后您将能够创建一个项目并选择您想要包含在项目中的模块。单击 Create Project 按钮，为其命名，并提供一个项目 URL(注意:出于安全原因，URL 必须是惟一的，因此如果您没有，可以创建一个或提供您计划最终部署到的 URL)。单击“编辑模块”按钮，并为您的身份验证模块选择 Blockstack。切换到存储模块并选择 Pinata。点击右下角的保存，然后点击左上角的菜单，选择账户，返回账户页面。](https://app.simpleid.xyz/?t=ipfs_su)

您需要做的最后一件事是获取您的开发人员 ID 和 API 密钥。点击查看项目，你会看到这两个。把它们保存在某个地方，因为我们马上就要开始编码了！

### 启动项目

在“终端”的项目文件夹中，我们只创建一个文件:

触摸 index.html

对于本教程，我们将在一个 JavaScript 文件中编写所有的 JavaScript 代码，并用一个脚本标签将它们导入到我们的 index.html 文件中。这将是一个单页应用程序，所以我们将只需要一个 html 文件。

现在，您可以在您最喜欢的代码编辑器中打开您的项目，我们可以开始编码了。在 index.html 文件中，让我们添加一些标准的 HTML 样板:

```
<!DOCTYPE html>
<html>
   <head>
     <meta charset="UTF-8">
     IPFS Notes
   </head>
   <body>
     <!--The app code will go here-->

     <script type="application/javascript" src="main.js"></script>
   </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在项目目录的根目录下创建一个名为 main.js 的文件。在整个项目中，您将只有两个文件:

*   index.html
*   主页. js

让我们确保一切正常。在您的 main.js 文件中，添加这个:

```
testing();

function testing() {
  console.log("testing");
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，打开你的 index.html 文件，它应该在你的浏览器中加载。打开控制台，您应该会看到单词“testing”

好了，现在我们来添加一些真实的代码。在我们编辑任何 HTML 之前，让我们先来看看 JavaScript。从 [SimpleID 文档](https://docs.simpleid.xyz)中，我们可以看到我们需要设置我们的功能和初始配置。所以，删除 main.js 文件中的测试代码，添加这个:

```
const config = {
  apiKey: ${yourApiKey},
  devId: ${yourDevId},
  authProviders: ['blockstack'], 
  storageProviders: ['blockstack', 'pinata'], 
  appOrigin: "https://yourapp.com", 
  scopes: ['publish\_data', 'store\_write', 'email'] 
}

async function signUp() {

//sign up code here
}

async function signIn() {

//sign in code here
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这是怎么回事？我们声明了一个全局变量，它是一个包含配置选项的对象。在配置对象中，我们包括我们开发人员 ID 和 API 密钥。但是我们还需要告诉 SimpleID 我们使用的是什么认证模块和存储模块(记住:我们使用 Blockstack 进行认证，Pinata 进行存储)。appOrigin 应该与您在创建项目和生成 API 密钥时输入的内容相匹配。scopes 数组只是授予您对某些功能的访问权。

您会注意到我们两个函数前面的 async 关键字。我们将发出导致承诺的 http 请求，我们需要在执行额外的代码之前等待承诺解决，因此我们需要使这些函数成为 async/await 函数。你很快就会明白我的意思。

现在，我们需要创建一个注册表单，并将该表单的结果传递给注册函数。对我来说，我认为如果用户没有登录，注册表单页面应该首先显示，所以我将创建一个变量来指定它。在 JavaScript 代码的顶部，在脚本标记之间，我添加了以下内容:

let startPage = " signup

当我们在那里的时候，我们还可以创建两个我们需要的变量。

```
let loggedIn = false;
let loading = false; 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们在 html:
的主体部分创建注册表单

```
<div style="display: none;" id="signup-form">
  <form onsubmit="event.preventDefault()">
    <input type="text" id="username-signup" placeholder="username" /><br/>
    <label>Username</label><br/>
    <input type="password" id="password-signup" placeholder="password" /><br/>
    <label>Password</label><br/>
    <input type="text" id="email-signup" placeholder="email" /><br/>     
    <label>Email</label><br/>
    <button onclick="signUp(event)" type="submit">Sign Up</button>
  <form>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

很基本的形式。我们稍后将对此进行样式化，但是您会注意到在开始的 div 中有一个样式属性。为什么我们不显示这个表单？嗯，我们需要根据一些变量有条件地显示它:

*   用户已经登录了吗？不要显示它。
*   用户已经决定登录而不注册了吗？不要显示它。
*   页面正在加载吗？不要显示它。

我们如何利用之前创建的变量来有条件地呈现这个表单呢？在此之前，让我们也创建我们的登录表单。我们可以复制注册表单并做一些调整。您应该会得到这样的结果:

```
<div style="display: none;" id="signin-form">
  <form onsubmit="event.preventDefault()">
    <input type="text" id="username-signin" placeholder="username" /><br/>
    <label>Username</label><br/>
    <input type="password" id="password-signin" placeholder="password" /><br/>
    <label>Password</label><br/>
    <button onclick="signIn(event)" type="submit">Sign In</button>
  <form>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

当用户登录时，我们不需要电子邮件字段。我们还需要将元素 id 更改为特定于登录，而不是注册。我们还更新了在表单提交和按钮标签上调用的函数。很简单。

现在，我们可以让我们创建的那些变量为我们做一些工作。我们希望这个新函数能够在每次加载页面和执行特定操作时处理被调用的更新。让我们像这样做。在 JavaScript 代码的顶部，在所有变量声明之后，让我们调用一个名为 pageLoad()的函数，并在它下面创建实际的函数:

```
pageLoad();

function pageLoad() {
  console.log("page loaded");
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您保存您的文件，并在浏览器中重新加载 index.html 文件，您应该在控制台中看到“页面已加载”。不过，这不是我们想要发生。让我们让这些代码真正为我们工作。条件逻辑的时间:

```
pageLoad();

function pageLoad() {
  if(loading) {
    //Need to render a loading screen here.
  } else if(loggedIn) {
    //Need to render the page for logged in users
  } else {
    //Here we are deciding whether to show the sign up or sign in form
    if(startPage === "signup") {
      document.getElementById('signin-form').style.display = "none";
      document.getElementById('signup-form').style.display = "block";
    }  
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们保存并重新加载我们的页面，我们现在应该看到注册页面！但是如果用户想要登录呢？我们该如何处理？简单！我们可以设置几个按钮，让用户在登录和注册表单之间切换。在你的注册和登录表单上方，在你的 html 的主体部分，添加这些按钮，包裹在一个 div 中，我们可以根据需要隐藏和显示:

```
<div id="auth-buttons">
  <button onclick="changeForm('signup')">Sign Up</button>
  <button onclick="changeForm('signin')">Sign In</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以在 pageLoad 函数下面创建另一个名为 changeForm 的函数。

```
function changeForm(page) {
  if(page === "signin") {
    document.getElementById('signin-form').style.display = "block";
    document.getElementById('signup-form').style.display = "none";
  } else {
    document.getElementById('signin-form').style.display = "none";
    document.getElementById('signup-form').style.display = "block";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有很多不同的方法来处理这个问题，但这是一个非常简单的解决方案，所以我要用它。如果您愿意，欢迎您实施不同的策略。

我们应该在 html 的主体中添加几个更有条件的呈现部分。我们需要一个加载屏幕和一个容纳登录用户的应用程序屏幕。我们可以像这样简单地模仿它:

```
<div style="display: none" id="loading">
  <h1>Loading...</h1>
</div>

<div style="display: none" id="root-app">
  <h1>This is the app</h1>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要做的就是更新我们的页面加载函数，并在我们更新应用程序状态的任何时候调用它。该函数现在应该是这样的:

```
function pageLoad() {
  if(loading) {
    document.getElementById('loading').style.display = "block";
    document.getElementById('auth-buttons').style.display = "none";
    document.getElementById('root-app').style.display = "none";
    document.getElementById('signup-form').style.display = "none";
    document.getElementById('signin-form').style.display = "none";
  } else if(loggedIn) {
    document.getElementById('loading').style.display = "none";
    document.getElementById('auth-buttons').style.display = "none";
    document.getElementById('signup-form').style.display = "none";
    document.getElementById('signin-form').style.display = "none";
    document.getElementById('root-app').style.display = "block";
  } else {
    //Here we are deciding whether to show the sign up or sign in form
    if(startPage === "signup") {
      document.getElementById('signin-form').style.display = "none";
      document.getElementById('signup-form').style.display = "block";
    } else if(startPage === "signin") {
      document.getElementById('signin-form').style.display = "block";
      document.getElementById('signup-form').style.display = "none";
    } else {
      document.getElementById('root-app').style.display = "block";
      document.getElementById('auth-buttons').style.display = "none";
      document.getElementById('signup-form').style.display = "none";
      document.getElementById('signin-form').style.display = "none";
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经准备好实际注册并登录了！让我们开始吧。您之前已经创建了配置对象。所以现在我们需要做的就是获取用户的注册凭证。

有几种方法可以做到这一点，但为了尽可能简单，我们将只获取输入值。在你的注册函数中，添加这个:

```
async function signUp(e) {
  e.preventDefault();
  //Keychain request
  loading = true;
  const username = document.getElementById('username-signup').value;
  const password = document.getElementById('password-signup').value;
  const email = document.getElementById('email-signup').value;
  pageLoad();
  const data = `username=${username}&password=${password}&email=${email}&development=true&devId=${config.devId}`;
  const urlKeychain = "https://api.simpleid.xyz/keychain";
  const urlAppKeys = "https://api.simpleid.xyz/appkeys";
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是我们最终向 SimpleID API 发出 post 请求所需的开始。我们已经将 loading 变量转换为 true，并且正在捕获注册表单的输入字段。您会注意到，在输入元素被捕获为变量之后，我们调用了 pageLoad()函数。这只是一种安全措施，因为我们正在更新页面状态以显示加载屏幕而不是表单，我们希望确保我们已经存储了用户名、密码和电子邮件并准备好使用。

我会花点时间指出我没有对这些输入元素进行任何验证。这超出了本教程的范围，但是您确实应该这样做。

我们还存储了两个 SimpleID API 端点作为变量。我们实际上需要这两者来创建帐户。第一个创建用户的钥匙串(用户信息、私钥等)。第二个函数返回构建用户会话所需的特定于应用程序的信息。

现在，在我们继续 signUp()函数代码之前，我们应该设置我们的 HTTP 请求代码。我们将在任何需要向 SimpleID API 发出请求的时候调用这个函数，结果将是一个承诺。因此，继续创建一个名为 postToApi():
的新函数

```
function postToApi(data, url) {
  return new Promise((resolve, reject) => {
    var xhr = new XMLHttpRequest();
    xhr.withCredentials = true;
    xhr.addEventListener("readystatechange", function () {
      if (this.readyState === 4) {
        resolve(this.responseText);
      }
    });

    xhr.open("POST", url);

    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

    xhr.setRequestHeader("Authorization", config.apiKey);

    xhr.send(data);
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

根据 SimpleID API 文档，我们知道我们正在发送表单数据，因此内容类型已设置。您可能已经注意到，在前面的注册函数中，我们捕获了一个名为 data 的变量，该变量具有我们需要的 urlencoded 格式，以便将表单数据传递给 API。

确保用在 SimpleID 中创建项目时收到的 API 键替换 apiKey。

因为我们已经将此作为承诺，所以当承诺解析时，将会发送回响应。所以现在，我们可以在我们之前写的代码下面的注册函数中这样做:

```
const keychain = await postToApi(data, urlKeychain);
console.log(keychain); 
```

Enter fullscreen mode Exit fullscreen mode

您实际上可以保存它，并用我们到目前为止编写的代码测试您的注册表单。但是它所要做的只是返回一个身份地址，这个地址本身并不是很有用。记住，我们还需要调用第二个 SimpleID API 端点。但是，只有当我们收到的钥匙串值不是错误时，我们才应该这样做。在 console.log(钥匙串)下面添加以下内容:

```
if(!keychain.includes("KEYCHAIN\_ERROR")) {
  //Now we need to fetch the user data from a second API call
  let profile = {
    '@type': 'Person',
    '@context': 'http://schema.org',
    'apps': {}
  }
  profile.apps[window.location.origin] = "";
  const url = encodeURIComponent(window.location.orign);
  const uriEncodedProfile = encodeURIComponent(JSON.stringify(profile))
  const keyData = `username=${username}&password=${password}&profile=${uriEncodedProfile}&url=${url}&development=true&devId=${congig.devId}`

  const userData = await postToApi(keyData, urlAppKeys);
  console.log(userData)

} else {
  loading = false;
  loggedIn = false;
  pageLoad();
  console.log("Failed")
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，看起来这里发生了很多事情，但也没那么糟糕。我们说，如果第一个 API 调用没有错误，我们就开始第二个 API 调用。根据 SimpleID 文档，我们需要向我们的 devID 提供用户的用户名、用户的密码、我们的应用程序的 url 和配置文件对象。让我们快速地讨论一下剖析对象。

SimpleID 支持多种 Web 3.0 协议，其中一种协议是 Blockstack。Blockstack 具有这种配置文件概念，允许用户相互交互并共享数据。我们希望确保我们总是准备好一个 profile 对象，以防您作为开发人员决定构建一个使用块堆栈存储的应用程序，或者如果用户决定使用另一个也实现了 SimpleID 的应用程序。请注意，用户可以在多个应用程序中使用相同的帐户凭据。因此，只需知道概要文件格式(如 SimpleID 文档中所记录的)采用如上所示的形式。当然，我们必须对它进行 URIEncode，以支持向 API 发送表单数据。

url 变量和您提供给配置文件对象的 url 需要是格式良好的 url，因此如果您在没有 web 服务器的情况下进行开发，您不能只使用 window.location.origin。如果是这种情况，您应该提供一个类似于 [http://localhost:3000](http://localhost:3000) 的原点或您计划将应用程序部署到的最终 URL。

你可能也注意到了我们发送给 API 的数据中有一个发展的标志。在生产中，确保这一点始终正确。

在我们的 else 语句中，如果第一个 API 调用失败，我们希望将应用程序状态返回到登录屏幕，并在控制台记录某种消息。但是如果一切都成功，我们调用第二个 API 端点，我们需要嵌套一些额外的逻辑来支持该端点上的成功和失败。在 console.log(userData)下面添加以下内容:

```
if(!userData.includes('ERROR')) {
  let userSession = JSON.parse(userData);
  userSession.username = username;
  localStorage.setItem('user-session', JSON.stringify(userSession));
  loading = false;
  loggedIn = true;
  pageLoad();
} else {
  loading = false;
  loggedIn = false;
  pageLoad();
  console.log("Error");
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，这里我们说如果第二个 API 调用成功，让我们解析响应(这是 API 返回的字符串)。然后，我们希望将用户的用户名添加到我们现在创建的对象中(因为 API 不返回用户名)。一旦我们做到了这一点，我们需要一种方法来持久化用户登录的会话。有不同的方法来处理这个问题，并且对于什么类型的数据应该存储在本地存储器中存在争论。自学这些主题，但是要知道，为了本教程，我将把会话信息放到本地存储中。

我们还需要翻转状态变量，并通过调用 pageLoad()来更新应用程序状态。当然，如果第二个 API 调用失败，用户将无法登录，我们需要将他们返回到注册屏幕。

如果你保存了这个，你就可以注册一个账户了。这里需要注意的一点是，如果你只是在浏览器中打开你的 index.html 文件，而不是运行一个本地 web 服务器，你将需要一个 CORS 扩展[，就像这个](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi)。原因是 API 端点期望基于浏览器的请求具有格式良好的来源。以 File://开头的原点是不可接受的。但是如果您使用 CORS 扩展，您将为开发做好一切准备。

如果你的注册一切顺利，你会看到你的注册表单消失，一个加载指示器弹出，然后，这是我们之前用作占位符的应用程序文本将出现。

这太棒了，但是我们还需要做两件事。第一，如果用户刷新页面会发生什么？好吧，试试看。看来用户是自动注销了？此外，用户如何登录？我们还没有连接登录功能。

让我们快速解决会话持久性问题。在 pageLoad()函数下的 main.js 文件中，我们只需要快速检查用户会话信息的本地存储。如果你遵循了我上面的代码，你可以把它添加到 pageLoad()函数的顶部:

```
if(localStorage.getItem('user-session')) {
  loggedIn = true;
} 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单！

最后，我们希望支持登录和注销。这将比设置登录代码容易得多。对于登录，我们将复制注册代码的一半。让我们现在做那件事。在 signUp()函数下，添加以下内容:

```
async function signIn(e) {
  e.preventDefault();
  loading = true;
  const username = document.getElementById('username-signin').value;
  const password = document.getElementById('password-signin').value;
  pageLoad();
  const urlAppKeys = "https://api.simpleid.xyz/appkeys";

  let profile = {
    '@type': 'Person',
    '@context': 'http://schema.org',
    'apps': {}
  }
  profile.apps[window.location.origin] = "";
  const url = encodeURIComponent(window.location.origin);
  const uriEncodedProfile = encodeURIComponent(JSON.stringify(profile))

  const keyData = `username=${username}&password=${password}&profile=${uriEncodedProfile}&url=${url}&development=true&devId=${config.devId}`
  const userData = await postToApi(keyData, urlAppKeys);
  if(!userData.includes('ERROR')) {
    console.log(userData);
    let userSession = JSON.parse(userData);
    userSession.username = username;
    localStorage.setItem('user-session', JSON.stringify(userSession));
    loading = false;
    loggedIn = true;
    pageLoad();
  } else {
    loading = false;
    loggedIn = false;
    pageLoad();
    console.log("Error");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了跳过第一个 API 调用之外，这段代码和注册代码做的是一样的。因此，如果您仍然在注册页面上，请单击屏幕顶部的登录按钮，输入您之前用于注册的凭据，您应该会看到页面更新为加载，然后更新为这是应用程序文本。

现在，因为我们已经持久化了用户会话，所以您应该能够刷新页面，并且用户仍将登录。如果一切顺利，我们还有最后一件事要做。我们需要让用户退出。

回到你的 html 文件，找到我们写的应用程序代码。让我们在该文本上方添加一个“注销”按钮。

```
<div style="display: none" id="root-app">
  <button onclick="signOut()">Sign Out</button>
  <h1>This is the app</h1>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，回到 main.js 文件，我们可以像这样添加非常简单的 signOut()函数:

```
function signOut() {
  localStorage.removeItem('user-session');
  window.location.reload();
} 
```

Enter fullscreen mode Exit fullscreen mode

保存，刷新你的页面，然后点击退出。应该工作得很漂亮。

恭喜你！您刚刚构建了一个零依赖性的单页面应用程序，允许用户注册、登录和注销。在本教程的第二部分中，你将添加 IPFS 功能，加入一些样式，并使之成为一个真正的应用。

* * *