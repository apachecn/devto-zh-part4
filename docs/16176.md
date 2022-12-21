# reac 第 1 章-初始配置和 reac-router-DOM

> [https://dev . to/ecdevs/react-cap-1-初始-y 反应路由器-dom-kc6](https://dev.to/ecudevs/react-capitulo-1-configuracion-inicial-y-react-router-dom-kc6) 配置

# 做出反应

# 是什么

使用 JavaScript 构建接口的书目

# 为什么会出现

世界上最重要的社交网络接口在渲染其视图时性能低下

## 项目的初始设置

1.  安装扩展
    `ES7 React/Redux/GraphQL/React-Native snippets`

2.  创建项目。
    `npx create-react-app red-social-negocios
    cd red-social-negocios
    npm start`

3.  instalar Material-Ui
    【https://material-ui.com/getting-started/installation/】T2
    T0】

4.  Instalar 风格的组件
    【https://material-ui.com/getting-started/installation/】T2
    T0】

5.  我们创建了项目目录。

6.  我们在 theme/[![](img/32fd0f524920ae9864e98dc9bfc7dc28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9sz0zoFT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jwq9ijlukr25uznvffkm.png)文件夹内创建了以下文件

*   app layout . js(functional component)
    我们将使用它作为我们应用程序的掩码，这里是我们应用程序中最常重复使用的组件菜单。
    -- gist --

*   globalsytes . js(JavaScript helper)
    我们将使用它来定义全局样式
    -- gist --

*   uiconstants . js(JavaScript helper)
    将常量(如颜色、屏幕大小、阴影等)保存为 helpers
    -- git --

*   asidelayout . js(功能组件)
    每当我们想把我们的应用程序分成
    [![](img/d43a8f119c4a9e846a530d98b24a0236.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cZvTfgVN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f68em7lhj3o8ex4d7g31.png) 
    -- git --

## 设置导航(基本)

我们将使用 react-router-dom 来处理我们应用程序中的导航。

这些设置在 App.js 文件中进行

1.  安装试剂路由器-dom 用于历史处理和导航
    `npm install react-router-dom`

2.  我们在 App.js
    `//HISTORIAL
    import { BrowserRouter, Router, Route, Switch, withRouter } from 'react-router-dom';
    import { createBrowserHistory } from "history";
    const historial = createBrowserHistory();` 内将试剂路由器 dom 导入我们的项目

3.  Creamos 3 类组件:

    *   home container . js(class component)
    *   perfil container . js(class component)
    *   contactoscontainer . js(class component)
    *   log in container . js(class component)
4.  我们在 App.js 上导入它们:
    `import HomeContainer from './components/containers/HomeContainer';
    import PerfilContainer from './components/containers/PerfilContainer';
    import ContactosContainer from './components/containers/ContactosContainer';
    import LoginContainer from './components/containers/LoginContainer';` 

5.  反应路由器 Dom 如何工作

    *   我们将把常量历史作为标签`<Router history={historial}/>`上的属性发送，这样我们就可以访问我们应用程序中的用户浏览历史记录。
    *   我们使用从 react-router-dom 导入的标签，并将在其中写入我们的路径。
    *   我们开始写我们的路线。其中 path 是我们要放置在浏览器中的方向，component 是当我们移动到该路径时将转至 cargr 的组件。`<Route path="/descrubrir" component={DescubrirPersonasContainer} />`
6.  我们可以通过两种方式进行导航:

    *   通过链路标签`<Link to='/perfil'></Link>`
    *   通过历史〔t0〕