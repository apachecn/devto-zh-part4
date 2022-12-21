# 网络微服务的变种。

> 原文：<https://dev.to/tetragius/variant-of-microservices-on-the-web-1m48>

现在网上有很多关于 JS 上微服务架构的文章。

> 我马上就去预订，我反对在网上使用“微服务”这个词来称呼前端，在我看来更准确的说法是“模块”。

## 建筑

#### 核心

核心-提供加载模块、共享状态存储(例如 redux)、基于 RxJS 的公共数据总线、模态窗口和通知的公共服务的功能。如果您愿意，可以扩展列表。

内核根据用户的请求或另一个模块的请求来加载模块。

#### 模块

该模块是一个常规的 web 应用程序，可以组装成一个 UMD 模块，并导出到入口点之外以连接到内核。

在我的例子中，将有三个组件:用于渲染的主要 react 组件、需要连接到共享存储的 Reducer，以及模块准备根据契约共享的公共服务和功能。

模块可以不仅仅是一个页面，它可以是某种小部件或插件，或者只是一组辅助功能

#### 模块间的通信

模块通过请求共享服务经由内核进行通信。或者根据合同中规定的协议通过 RxJS 总线。

## 代码中的解释

#### 模块内

在您的应用程序中，很可能有类似于
的内容

```
...
import App from './containers/app';
...

ReactDOM.render(
  <Provider store={store}>
    <Router>
        <App/>
    </Router>
  </Provider>,
  document.getElementById('app'),
); 
```

要创建一个模块，只需要用下一个内容
创建一个新文件(例如 main.tsx)

```
export { default as Main } from './containers/app';
export { default as reducer } from './redux/reducers';
export { default as shared } from './services/shared-service'; 
```

这将是我们核心所期望的三个切入点。

其中

```
...'./redux/reducers'

const reducers = combineReducers<IState>({
  requests: requestsReducer,
  tasks: maintenanceTaskReducer,
  main: mainReducer
});

export default reducers;
...

...'./services/shared-service'

interface ISharedService {
    mapper(type: string, item: any);
    openPlate(type: string, item: any);
    render(type: string, item: any);
}

class $SharedService implements ISharedService {
    task = new MaintenanceTask(null, null);
    maintenance_audit = new Tasks.MaintenanceAuditTask(null, null);
    maintenance_detach_gui = new Tasks.MaintenanceDetachGuiTask(null, null);
    maintenance_utp_request = new MaintenanceTask(null, null);
    request = new MaintenanceRequest(null, null);
    mapper = (type: string) => this[type] && this[type].mapper || TaskFactoryByTypeName(type);
    openPlate = (type: string) => this[type] && this[type].openPlate || TaskFactoryByTypeName(type);
    render = (type: string) => this[type] && this[type].render || TaskFactoryByTypeName(type);
}

const SharedService = new $SharedService();

export default SharedService;
... 
```

先说一点 SharedService。这是导出的静态服务。第三方模块可能向核心请求某些东西。

> 找到模块- **module_name** ，然后找到其中的通用服务和 **openPlate** 方法，然后调用该方法渲染 **maintenance_detach_gui**

( **getShared** 在核心解释部分)

要添加的最后一个文件是一个存根，以便模块可以在内核内外工作。

```
import * as PS from 'portal-service';

class WebBase {

  static sendNotify(notify: any, type: string, delay: number, closeable: boolean = false) {
    try {
      return PS && PS.notification.send(notify, type, delay, closeable);
    }
    catch (e) {
      return;
    }
  }

  static sendStream(message: { type: string, body: any }) {
    try {
      return PS && PS.stream.next(message);
    }
    catch (e) {
      return;
    }
  }
}

export default WebBase; 
```

注意门户——服务导入，我们后面会讲到。

并添加到 webpack 组装模块

```
...
output: {
    path: paths.build,
    filename: 'index.min.js',
    library: 'Main',
    libraryTarget: 'umd',
},
externals: {
    'portal-service': 'portal-service',
... 
```

在输出端，我们必须得到三个文件。

*   index.min.js
*   main.css
*   manifest.json

manifest.json - core 需要加载模块

```
{  "id":  "D63E7031-DD51-42E3-979E-85107F4DB58F",  "name":  "maintenance",  "version":  "7.0.0"  } 
```

#### 内核内部

为了使**门户服务**在模块中可用，使用了 requireJS 上的闭包函数。这允许我们拦截来自模块的*请求(‘门户服务’)*。

在拦截的时候，我们把提供内核公共服务的对象返回给模块。

> (建立 webpack 后，**导入...从**换成了**要求**

它还为模块打开了独立于内核和其他模块加载它们自己的依赖项的可能性。

但是要使用这样的闭包，内核必须由 gulp 编译，因为 webpack 使用自己的机制而不是 requireJS。

> 我选择了这样一种机制，因为我不想从内核把依赖关系推到模块中，我想让模块自己决定哪里着火了

```
registerPageSync(page: any): boolean {
        if ($ExtModuleService.registredPage[page.name]) {
            return true;
        }

        var self = this;

        function reqListener(responseText) {
            try {

                let getPage = new Function('exports', 'module', responseText + " ;return module.exports;");

                //dependency injection start

                let injectPrepare = new Function('injector', 'page', `

                    var closure = (function(r){
                        var _require = r;
                        var _modules = {};
                        return {require: require, modules: _modules};
                    })(window.require);

                    window.require = function(o, u){
                        try{
                            return closure.require(o, u);
                        }
                        catch(e){
                            if(!closure.modules[o]){
                                console.log('inject : ' + o + ' by: ' + page.name);
                                closure.modules[o] = injector(o, page);
                                return closure.modules[o];
                            }
                            else{
                                return closure.modules[o];
                            }
                        }
                    }

                `);

                var fakeInjector = function (name, page: any) {
                    if (name === "portal-service") {
                        return self.injectPortalService();
                    }
                    else {
                        if (page.dependencies) {
                            for (var depName in page.dependencies) {
                                if (depName === name) {

                                    let dep = page.dependencies[depName];
                                    let oReq = new XMLHttpRequest();
                                    dep.path && oReq.open("GET", `${dep.path}/${dep.fileName}`, false);
                                    !dep.path && oReq.open("GET", `pages/${page.name}/dependencies/${depName}/${dep.fileName}`, false);
                                    oReq.send();

                                    if (oReq.status === 200) {
                                        return eval(oReq.responseText);
                                    }
                                    else {
                                        return false;
                                    }

                                }
                            }
                        }
                    }
                }

                injectPrepare(fakeInjector, page);

                //dependency injection end

                let _page = getPage({}, { exports: null });

                let o = {};
                o[page.name] = React.createElement(_page.Main);

                if (_page.reducer) {
                    injectAsyncReducer(page.name, _page.reducer);
                }

                _.assign($ExtModuleService.registredPage, o);
                return true;
            }
            catch (e) {
                console.log(e);
                return false;
            }
        }

        let fileref = document.createElement("link");
        fileref.setAttribute("rel", "stylesheet");
        fileref.setAttribute("type", "text/css");
        fileref.setAttribute("href", `pages/${page.name}/main.css?ver=${page.version}`);
        document.getElementsByTagName("head")[0].appendChild(fileref);

        if (page.externalLib) {
            let lib = document.createElement("script")
            lib.setAttribute("type", "text/javascript")
            lib.setAttribute("src", `pages/${page.name}/${page.externalLib}?ver=${page.version}`);
            document.getElementsByTagName("head")[0].appendChild(lib);
        }

        let oReq = new XMLHttpRequest();
        oReq.open("GET", `pages/${page.name}/index.min.js?ver=${page.version}`, false);
        oReq.send();

        if (oReq.status === 200) {
            return reqListener(oReq.responseText)
        }
        else {
            return false;
        } 
```

这将在调用门户服务时对模块可用。

```
 injectPortalService() {
        return {
            auth: AuthService,
            stream: MainService.mainStream,
            notification: NotificationService,
            ws: wsService,
            store: store,
            history: history,
            getPermissions: (name) => AuthService.getEmployeePermissionsByModule(name),
            shared: SharedService.services,
            getShared: (module) => SharedService.getSharedSafity.call(SharedService, module),
            modals: (props) => new ModalService(props)
        }
    } 
```

所以模块缩减器连接到全局存储器。

```
export const injectAsyncReducer = (name: string, asyncReducer: any) => {
    (<any>store).asyncReducers[name] = asyncReducer;
    (<any>store).replaceReducer(createReducer((<any>store).asyncReducers));
} 
```

在我们想要渲染模块的地方

```
...
{this.props.pages.map((page) =>
    [
        <Route
            key={page.id}
            path={`/${page.name}`}
            render={(props) => <PageContainer key={page.id + '_requests'} page={page} {...props} />} />
    ])
}
... 
```

在页面容器中，我们使用 **getPageElementAsync** 并呈现我们的模块

```
 ExtModuleService.getPageElementAsync(_page).then(
    page => {
        if (page) {
            let content = React.cloneElement<any, any>(page as any, { ...this.props })
            this.setState({ content: content });
        }
        else {
            this.setState({ error: true });
        }
    }
); 
```

* * *

## 闭包如何工作

UMD 模块总是包含一个类似
的字符串

```
!function(e,t){"object"==typeof exports&&"object"==typeof module?module.exports=t... 
```

这允许前面描述的闭合工作。

* * *

## 结论

这种方法允许基于合同为独立团队开发模块。并将模块作为独立的应用程序或核心的一部分运行。

内核通过协调模块的工作并为诸如请求用户权限或认证之类的工作提供公共机制，来定义加载模块和与模块交互的机制。

当然，这只是微服务架构在 web 上实现的选项之一。也许不是最好的。但是它成功地在我的工作项目中使用了超过六个模块和十个小部件。包括一个模块，该模块仅由对公共方法的调用和来自其他模块的呈现组成。

当然，CORS 和后端基础设施设置被留在幕后，但这是另一个故事。

谢谢！

如果你喜欢，你也可以阅读:

*   [用于反应的基本组件](https://dev.to/tetragius/basecomponent-for-react-2c5n)
*   [我的 react 状态管理库。基于代理 API。](https://dev.to/tetragius/my-warm-state-management-library-for-react-based-on-proxy-api-23nd)