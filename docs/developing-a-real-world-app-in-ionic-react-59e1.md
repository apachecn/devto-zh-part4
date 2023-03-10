# 在 Ionic React 中开发真实世界的应用程序

> 原文：<https://dev.to/jscrambler/developing-a-real-world-app-in-ionic-react-59e1>

Ionic 最近增加了对 React 和 Vue 框架的支持。传统上，Ionic 使用 Angular 和 Cordova 框架来创建他们的应用程序。有了 [Ionic 4](https://blog.jscrambler.com/introduction-to-ionic-4/) ，我们可以开发与框架无关的应用。在本教程中，我们将在 React 框架中创建一个基本的 Ionic 应用程序。

### 现实世界和管道

Thinkster 的人正在进行一项名为 RealWorld project 的项目。Conduit 是一个中型克隆，可以使用多个堆栈来构建。我们可以混合搭配不同类型的后端和前端。每个后端和前端都遵循相同的 [API 规范](https://github.com/gothinkster/realworld/tree/master/spec)。

与简单的 CRUD ToDo 应用不同，Conduit 是一个成熟的 CMS。实施这个项目需要一些努力。

## 入门

对于本教程，我们将涵盖以下几点。

*   主页(有和没有授权)
    *   文章列表
    *   收藏一篇文章(需要授权)
    *   导航至文章页面
    *   导航至作者个人资料页面
    *   显示您的订阅源(需要授权)
    *   显示热门标签。
    *   按标签过滤文章。
*   文章页面(有授权和无授权)
    *   阅读文章
    *   列出评论
    *   添加评论(需要授权)
    *   导航到作者页面
*   设置页面(需要授权)
    *   修改用户设置
*   个人资料页面(有和没有授权)
    *   显示用户详细信息
    *   按用户显示帖子
    *   显示用户喜欢的帖子
    *   关注用户(需要授权)
*   创建文章(需要授权)
    *   创建文章
*   登录/注册页面
    *   注册用户
    *   登录用户

### 了解管道 API

管道在这里有一个生产 API 端点。在本教程中，我们将使用该端点。或者，您可以使用各种语言建立自己的后端。

我们将使用:

*   /articles:用于获取文章、评论
*   /users:用于创建和验证用户
*   /profiles:对于以下配置文件
*   /tags:用于获取标记

用户通过报头中的 JWT 令牌进行身份验证。

### 设置项目

Ionic CLI 目前不支持 React 模板。我们将使用 create-react-app 来设置我们的项目。用途:

```
npm i -g create-react-app
create-react-app conduit --typescript 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以使用 npx:

```
npx create-react-app conduit --typescript 
```

Enter fullscreen mode Exit fullscreen mode

导航到文件夹并安装 Ionic 和 React 路由器:

```
cd conduit
npm install @ionic/react react-router react-router-dom @types/react-router @types/react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

让我们在这个项目中初始化 Ionic。

```
ionic init "Conduit" --type=custom 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在我们的项目
中启用电容器

```
ionic integrations enable capacitor 
```

Enter fullscreen mode Exit fullscreen mode

我们的项目目录中会生成一个文件`capacitor.config.json`。在文件中，我们需要将构建目录从`"webDir": "www"`改为`"webDir": "build"`。

使用
创建构建文件夹或生成构建

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以向我们的项目添加一个平台。

```
npx capacitor add android 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们将使用这种文件夹结构。

*   科学研究委员会
    *   组件/
    *   页数/
    *   常数. ts

让我们建立我们的常数文件。

```
export interface AppConfig {    
    API_ENDPOINT : string

  }  

export const CONFIG: AppConfig = {
    API_ENDPOINT : "https://conduit.productionready.io/api/"
  }; 
```

Enter fullscreen mode Exit fullscreen mode

在`App.tsx`文件中，我们需要导入 Ionic CSS 文件。

```
import '@ionic/core/css/core.css';
import '@ionic/core/css/ionic.bundle.css'; 
```

Enter fullscreen mode Exit fullscreen mode

## 开发 App

离线用户可以访问主页、文章、个人资料和登录页面。在线用户可以访问主页、文章、个人资料、设置和登录页面。

有 70 多种离子成分可以用来加速我们的开发。首先，让我们设置路由。

### 路由

我们使用 React 路由器来管理路由。我们将使用`IonRouterOutlet`来管理路线。与网站的关键区别在于用户不能使用 URL 导航。我们还为侧菜单导航创建了一个定制组件`SideMenu`。`IonMenuToggle`用于在组件中切换菜单。

登录键和事件监听器用于管理用户状态。从身份验证获得的令牌将保存在 localstorage 中。

```
class App extends Component {
  constructor(props: any){
    super(props);
  }
  render() {
    return (
      <Router>
        <div className="App">        
          <IonApp>
          <IonSplitPane contentId="main">
            <SideMenu></SideMenu>
            <IonPage id="main">        
                <IonRouterOutlet>
                    <Route exact path="/" component={HomePage} />
                    <Route exact path="/login" component={LoginPage} />  
                    <Route path="/article/:slug" component={ArticlePage} />
                    <Route path="/profile/:authorname" component={ProfilePage} />
                    <Route path="/settings" component={SettingsPage} />
                    <Route path="/newarticle" component={NewArticlePage} />                             
                </IonRouterOutlet>
            </IonPage>
            </IonSplitPane>
          </IonApp>
        </div>
      </Router>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

[![Protect your Code with Jscrambler](img/ee5fdc03b17ad6adb178b9607c227f8a.png)](https://blog.jscrambler.com/protecting-hybrid-mobile-apps-with-ionic-and-jscrambler/?utm_source=dev.to&utm_medium=referral)

侧菜单组件:

```
class SideMenu extends React.Component<any, any> {
  constructor(props: any){
    super(props);
        this.state = {      
      isLoggedIn: localStorage.getItem("isLogin") ? localStorage.getItem("isLogin") :"false",   
      routes:  {
        appPages: [
          { title: 'Home', path: '/', icon: 'home' },         
        ],        
        loggedOutPages: [
          { title: 'Login', path: '/login', icon: 'log-in' },
        ]  }
    }
    window.addEventListener('loggedIn', (e: any) => {            
      this.setState({
        isLoggedIn : e['detail'].toString(),
        routes : {
          appPages: [
            { title: 'Home', path: '/', icon: 'home' },         
          ],
          loggedInPages: [
            { title: 'My Profile', path: '/profile/'+localStorage.getItem("username"), icon: 'person'},        
            { title: 'New Article', path: '/newarticle', icon: 'create' },
            { title: 'Settings', path: '/settings', icon: 'settings' },
            { title: 'Logout', path: '/login', icon: 'log-out' }
          ],
          loggedOutPages: [
            { title: 'Login', path: '/login', icon: 'log-in' },
          ]  }        
      })      

    });  
  }    

   renderMenuItem(menu: any) {
    return (
         <IonMenuToggle key={menu.title} auto-hide="false">
                   <IonItem>
                     <IonIcon name={menu.icon} ></IonIcon>
                     <Link replace className="sidemenu-link" to={menu.path} >{menu.title}</Link>                    </IonItem>
                   </IonMenuToggle>
      )
  }

  render() {
      return (  
        <IonMenu side="start" menuId="first" contentId="main">
          <IonHeader>
            <IonToolbar color="success">
              <IonTitle>Start Menu</IonTitle>
            </IonToolbar>
          </IonHeader>
          <IonContent>
            <IonList>
              {this.state.routes.appPages.map((art: any) => this.renderMenuItem(art))}
              {this.state.isLoggedIn === "true" ? <> {this.state.routes.loggedInPages.map((art: any) =>
                this.renderMenuItem(art))} </> :<> {this.state.routes.loggedOutPages.map((art: any) =>
                this.renderMenuItem(art))} </> }
            </IonList>
          </IonContent>
        </IonMenu>
      )
  }
}
export default SideMenu 
```

Enter fullscreen mode Exit fullscreen mode

### 首页

如上所述，主页上有一个文章列表。`/articles`端点为我们提供了 20 篇文章的数组。

普通用户可以:

*   查看文章列表
*   浏览整篇文章
*   导航到作者个人资料
*   按标签过滤文章

经过身份验证的用户还可以:

*   查看他/她的文章摘要
*   最喜欢的文章

我们将创建`ArticleCard`和`TagsCloud`组件来简化我们的任务。

另外，`Header`是 Ionic Header 的包装器，以`title`为道具。

```
 <IonHeader>
          <IonToolbar >
          <IonTitle  class="header"  color="success">{this.props.title}</IonTitle>           
            <IonMenuButton slot="start"></IonMenuButton>
          </IonToolbar>
        </IonHeader> 
```

Enter fullscreen mode Exit fullscreen mode

`TagsCloud`是一个检索流行标签的组件。我们在端点`/tags`接收一个标签数组。我们将在道具中传递一个函数`onTagClick`。因此，我们将能够使用点击的标签过滤文章。我们使用`IonChip`来渲染标签芯片。

```
handleClick(tag: any) {
    console.log(tag);
    this.props.onTagClick(tag);
  }

  componentDidMount() {    
    fetch(CONFIG.API_ENDPOINT+"tags")
      .then(res => res.json())
      .then(
        (res) => {
          this.setState({          
            tags: res.tags
          });
        },      
        (err) => {
          console.log(err)
        }
      )
  }

  render() {
    return (      
      <div className="tagcloud">
      <IonLabel text-center>
            Popular Tags
          </IonLabel>
          <div>        
         {this.state.tags.map((tag: any, index: number) => 
          <IonChip color="success" key={index}>
          <IonLabel  onClick={() => this.handleClick(tag)}>{tag}</IonLabel>
        </IonChip>           
             )}         
         </div>
      </div>     );       
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`ArticleCard`组件为登录的用户返回一个`IonItem`和`IonItemSliding`。如果用户愿意，他/她可以滑动并收藏一篇文章。

让我们创建一个函数`loggedOutCard`来返回一个`IonItem`。

```
loggedOutCard() {
  return (                 
           <IonItem >
          <IonAvatar slot="start">
              <img src={this.props.src} />              
            </IonAvatar>
            <IonLabel>
              <p className="title">{this.props.title}</p>              
              <IonGrid >
                <IonRow>
                  <IonCol class="author" size="6">
                  <Link className="link" to={this.profileLink}>
                  {this.props.author}</Link>                     
                 </IonCol>
                  <IonCol  size="6" text-right>                  
                  <Link className="link" to={this.routeLink}>Read More</Link>        
                  </IonCol>
                </IonRow>
              </IonGrid>
            </IonLabel>
          </IonItem>      
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个函数`loggedInCard`来返回一个`IonSlidingItem`。

```
loggedInCard(){
  return (        
           <IonItemSliding>
          {this.loggedOutCard()}
        <IonItemOptions side="end">
          <IonItemOption  color={this.state.favorited ? "success": "light"} onClick={this.favoriteArticle}>
          <IonIcon color={this.state.favorited ? "light": "success"} class="icon-blog-card" name="heart" />{this.state.favoritesCount}</IonItemOption>
        </IonItemOptions>
      </IonItemSliding>           
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

经过认证的用户可以收藏文章。我们将创建一个函数`favoriteArticle`来实现这一点。

```
favoriteArticle = (params: any) => {
  console.log(params);
  let url = CONFIG.API_ENDPOINT+"articles/" + this.props.slug + '/favorite';
  let method;
  if (!this.state.favorited) {
    method = 'POST'
  } else {
    method = "DELETE"
  }
  fetch(url, {
      method: method,
      headers: {
        "Content-Type": "application/json",
        "Authorization": "Token " + localStorage.getItem("token"),
      }
    })
    .then(res => res.json())
    .then(
      (res) => {       
        this.setState({
          favorited: res.article.favorited,
          favoritesCount: res.article.favoritesCount,
        })
      },
      (err) => {
        console.error(err);
      }
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们基于用户状态返回组件。

```
 render() {   
      return (
        <>
        {localStorage.getItem("isLogin") === "true" ? this.loggedInCard() : this.loggedOutCard()} 
        </>               
      );    
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从`/articles`端点获取文章后，我们将在主页中迭代`ArticleCard`组件。

```
render() {      
      return (
        <>   
        <Header title="Home"></Header>
        <IonContent> 
          <IonSegment onIonChange={this.toggle} color="success">
              <IonSegmentButton value="Global" color="success" checked={this.state.segment==='Global' }>
                  <IonLabel>Global Feed</IonLabel>
              </IonSegmentButton>
              {localStorage.getItem("isLogin") === "true" ? <IonSegmentButton value="myfeed" color="success"
                  checked={this.state.segment==='myfeed' }>
                  <IonLabel>Your Feed</IonLabel>
              </IonSegmentButton> : '' }
          </IonSegment>
        <IonList>
        {this.state.articles.map((article: any) => 
        <ArticleCard key={article.slug} title={article.title} src={article.author.image} description={article.description} favorited={article.favorited} favoritesCount={article.favoritesCount} slug={article.slug} author={article.author.username}></ArticleCard>
        )}      
        </IonList>
        <TagCloud onTagClick={(e: any) => this.handleTagClick(e)} ></TagCloud>   
        </IonContent>    
      </>
      );
    } 
```

Enter fullscreen mode Exit fullscreen mode

`toggle`和`handleTagClick`函数在源代码中。

### 文章页面

我们通过文章页面中的`slug`获取一篇文章。这篇文章的正文是减价格式的。为了显示文章，我们将使用摊牌来转换它。此外，文章的评论从`/articles/slug/comments`端点获取，登录的用户可以发表评论。我们将创建一个单独的`Comment`组件来迭代列表。登录用户可以使用`IonTextArea`发表评论。

```
npm i showdown @types/showdown 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用
初始化摊牌

```
 this.converter = new Showdown.Converter({
          tables: true,
          simplifiedAutoLink: true,
          strikethrough: true,
          tasklists: true,
          requireSpaceBeforeHeadingText: true
        }); 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用
显示降价显示

```
 <div dangerouslySetInnerHTML={{ __html: this.converter.makeHtml(article.body)}}></div>

            </div> 
```

Enter fullscreen mode Exit fullscreen mode

其余的源代码可以在这里查看[。](https://github.com/JscramblerBlog/conduit-ionic-react)

[![Protect your React App with Jscrambler](img/023cad5b7d64267809a8b3c0348c4e3b.png)](https://blog.jscrambler.com/protecting-your-react-js-source-code-with-jscrambler/?utm_source=dev.to&utm_medium=referral)

### 登录页面

登录页面是一个基本的表单页面。我们将接受电子邮件和密码。`/users/login`端点接受 POST 请求并返回一个 JWT 令牌。我们将在本地存储中保存 JWT 令牌来管理我们的授权。为了切换侧菜单，我们将使用标准的 JS 事件。此外，我们将使用`/users`端点注册用户。此外，我们使用`IonToast`组件来显示错误消息。用于登录页面的代码如下。`IonInput`使用`CustomEvent`，我们可以使用`onIonChange`事件将输入绑定到状态。

```
updateUserName = (event: any) => {
    this.setState({ username: event.detail.value });  
  };
login= () => {
    let url , credentials;     
    if(this.state.action  == 'Login'){
      url = CONFIG.API_ENDPOINT + '/users/login';
      credentials = {
        "user": {
          "email": this.state.email,
          "password": this.state.password
      }
      }

    } else {
      url = CONFIG.API_ENDPOINT + '/users';
      credentials = {
        "user": {
          "email": this.state.email,
          "password": this.state.password,
          "username": this.state.username
      }
      }
    }
    fetch(url, {
            method: 'POST',
            headers: {
                "Content-Type": "application/json",              
            },
            body: JSON.stringify(credentials)

        })
        .then((res) => {
          console.log(res);
          if(res.status == 200){
            return res.json();
          } else {  
            if(this.state.action == 'SignUp') {
              throw new Error("Error creating user");
            } else {
              throw new Error("Error Logging in")  
            }                
          }

        } )
        .then(
          (result) => {
              console.log(result);    
              localStorage.setItem("token",result.user.token);       
              localStorage.setItem("username", result.user.username);
              localStorage.setItem("isLogin", "true");
              localStorage.setItem("email", result.user.email);

              this.event = new CustomEvent('loggedIn', {
                detail: true
              });
              window.dispatchEvent(this.event);
              this.props.history.push('/blog');
          },

          (error) => {
           console.error(error);           
           this.setState({toastMessage: error.toString(), toastState: true});
          }
        )
  }

  render(){
    return(
      <>
    <Header title="Login"></Header>    
    <IonContent padding>

    <div className="ion-text-center">
    <img src={image} alt="logo" width="25%" /> 
    </div>
    <h1 className="ion-text-center conduit-title">conduit</h1>      
    <IonToast
        isOpen={this.state.toastState}
        onDidDismiss={() => this.setState(() => ({ toastState: false }))}
        message= {this.state.toastMessage}
        duration={400}
      >
      </IonToast> 
    <form action="">

    <IonItem>

      <IonInput  onIonChange={this.updateEmail} type="email" placeholder="Email"></IonInput>
    </IonItem>
    {this.state.action === 'SignUp' ?    
      <IonItem>

        <IonInput onIonChange={this.updateUserName} type="text" placeholder="Username"></IonInput>
      </IonItem>
      : <></> } 
    <IonItem>
    <IonInput onIonChange={this.updatePassword} type="password" placeholder="Password"></IonInput>      
    </IonItem> 
    </form>      
            <IonButton onClick={this.login}>{this.state.action}</IonButton>         
    </IonContent>
    <IonFooter>
      <IonToolbar text-center>
          Click here to <a onClick={this.toggleAction}>{this.state.action === 'Login'? 'SignUp' : 'Login'}</a>
      </IonToolbar>
    </IonFooter>
  </>
    )
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 新建文章页面

该页面是一个类似登录页面的表单页面。然而，对于文章主体，我们将使用 Markdown 编辑器而不是文本区域。对于本教程，我们将使用 [react-mde](https://github.com/andrerpena/react-mde) 。

```
npm install --save react-mde 
```

Enter fullscreen mode Exit fullscreen mode

表单代码如下:

```
<form onSubmit={this.submitArticle}>
      <IonInput type="text" placeholder="Title" onIonChange={this.titleChange} class="border-input"></IonInput>
    <IonInput type="text" placeholder="What's this article about"  onIonChange={this.descriptionChange} class="border-input"></IonInput>

          <ReactMde
          onChange={this.handleBodyChange}
          onTabChange={this.handleTabChange}
          value={this.state.articleBody}
          selectedTab={this.state.tab}
          generateMarkdownPreview={markdown =>
            Promise.resolve(this.converter.makeHtml(markdown))
          }
        />
         <IonInput type="text"  placeholder="Enter Tags" class="border-input"  onIonChange={this.tagsChange}></IonInput>
         <IonButton expand="block" onClick={this.submitArticle}>Submit Article</IonButton>        
      </form> 
```

Enter fullscreen mode Exit fullscreen mode

### 使用电容器创建构建

我们将使用`run build`来构建我们的 React 项目:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将使用电容器来复制文件:

```
npx capacitor copy 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要在本地编译项目:

```
npx capacitor open android|ios 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

我开发管道的里程数变化很大。Conduit 没有针对移动设备的规范，因此需要一段时间来找出最佳工作流程。

我们可以通过使用像 [Redux](https://blog.jscrambler.com/becoming-familiar-with-redux/) 这样的中间件，在适当的地方使用骨架加载器，集成无限滚动和更好地处理错误来改进应用程序。为了完成应用程序的体验，我们将不得不修改后端，使其能够处理推送通知。如果你收到一个新的关注者或者你关注的人发布了一个新帖子，通知系统是有意义的。

就 Ionic React 而言，感觉就像我在使用一组预定义的组件开发一个 React web 应用程序。像`IonInfiniteScroll`这样的组件仍然是在制品，我们可以希望很快得到它们。就我个人而言，我很怀念框架和侧边菜单模板的紧密结合。我们可以期待将来完全的 Ionic CLI 和 Cordova 支持。

安卓用户可以在这里查看 app [。](https://play.google.com/store/apps/details?id=me.karandpr.conduit)

**最后，如果你正在构建一个具有敏感逻辑的应用程序，一定要遵循我们的指南 [React](https://blog.jscrambler.com/protecting-your-react-js-source-code-with-jscrambler/?utm_source=dev.to&utm_medium=referral) 和 [Ionic](https://blog.jscrambler.com/protecting-hybrid-mobile-apps-with-ionic-and-jscrambler/?utm_source=dev.to&utm_medium=referral) 来防止代码窃取和逆向工程。**

最初由卡兰·甘地在 Jscrambler 博客上发表。