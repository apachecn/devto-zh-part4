# 角度:当模型变脏时，显示警告并阻止导航

> 原文：<https://dev.to/elasticrash/angular-display-a-warning-and-prevent-navigation-when-model-is-dirty-29e0>

Angular 是我最喜欢的框架之一。我知道有很多人讨厌我，也有很多人喜欢我，这是有原因的。说到企业应用程序，这是它的亮点。

今天，我决定写一篇关于如何创建一个组件的文章，这个组件可以动态地插入到 dom 中，并且当你的视图的模型是脏的时候，可以阻止导航。

这种技术非常方便，尤其是在构建包含大量表单的应用程序时。

我们试图实现的基本目标是，如果表单发生了变化(通过创建或编辑表单),而用户意外地试图导航离开，给他一个选择。

*   返回并保存他的更改
*   或者接受他的更改将会丢失并导航离开

这是我们路由的组件需要实现的接口

*   getRef()返回组件的容器引用
*   isDirty()包含我们定制的比较逻辑

```
interface IDirty {
    isDirty(): boolean;
    getRef(): ViewContainerRef;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是实现`CanDeactivate`的警卫服务。它通过返回 true/false 来决定是否阻止导航。

```
@Injectable()
export class ModelDirtyGuardService implements CanDeactivate<IDirty> {
    constructor(
        private confirmationDialogService: ConfirmationDialogService,
        private confirmationDialogReferenceService: ConfirmationDialogReferenceService
    ) { }
    public canDeactivate(
        component: IDirty,
        currentRoute: ActivatedRouteSnapshot,
        currentState: RouterStateSnapshot,
        nextState: RouterStateSnapshot
    ): Observable<boolean> | Promise<boolean> | boolean {
        let canLeave: boolean = component.isDirty();
        if (canLeave === false) {
            canLeave = this.confirmationDialogService.loadComponent(
              component.getRef(),
              nextState.url
            );
            this.confirmationDialogReferenceService.allow = false;
        } else {
            this.confirmationDialogReferenceService.allow = false;
        }

        return canLeave;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

是负责构建组件并将其插入 dom 的服务。

`ConfirmationDialogReferenceService`保存我们想要插入的组件的全局状态。

正在发生的事情很简单

*   我们通过使用组件需要实现的`isDirty`函数来检查模型是否脏。我们在这里没有涉及的一件事情是，如何检查表单/模型是否是脏的。取决于您的应用程序逻辑。
*   如果模型是脏的，这意味着我们需要通过服务(`ConfirmationDialogService`)将组件(`ConfirmationDialogComponent`)插入到视图中。
*   通过`ConfirmationDialogReferenceService`设置状态`allow`

```
@Injectable()
export class ConfirmationDialogService {
    answer: boolean;
    componentRef: ComponentRef<ConfirmationDialogComponent>;

    constructor(
        private componentFactoryResolver: ComponentFactoryResolver,
        private confirmationDialogReferenceService: ConfirmationDialogReferenceService
    ) { }

    loadComponent(viewContainerRef: ViewContainerRef, nextState) {
        this.confirmationDialogReferenceService.routerState = nextState;
        let componentFactory = this.componentFactoryResolver.resolveComponentFactory(ConfirmationDialogComponent);
        this.componentRef = viewContainerRef.createComponent(componentFactory);
        this.confirmationDialogReferenceService.componentRef = this.componentRef;
        return this.confirmationDialogReferenceService.allow;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建实现 IDirty 的组件，它有两个功能

*   closeDialog()，我们决定不需要导航离开，所以我们需要卸载动态组件(通过`ConfirmationDialogReferenceService`销毁它来完成)并停留在同一个视图中。
*   navigateAway()我们接受我们同意导航离开，丢失任何更改。

```
export class ConfirmationDialogComponent{

    constructor(
    private confirmationDialogReferenceService: ConfirmationDialogReferenceService) { }

    public closeDialog() {
        this.confirmationDialogReferenceService.unloadComponent();
    }

    public navigateAway() {
        this.confirmationDialogReferenceService.allow = true;
        this.confirmationDialogReferenceService.destroyComponentAndAllowNavigation();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们有 ConfirmationDialogReferenceService，它保持我们的动态`ConfirmationDialogComponent`组件的当前状态。

重要的是

*   routerState，我们设置需要导航到的路线
*   unloadComponent，它销毁我们的组件(我们保持相同的视图)
*   destroycomponentandalllownavigation，它破坏我们的组件，让我们导航离开

```
@Injectable()
export class ConfirmationDialogReferenceService {
    private _componentRef: any;
    private _routerState: string;
    private _allow: boolean;

    constructor(
        private router: Router
    ) {

    }

    set componentRef(ref) {
        this._componentRef = ref;
    }

    get componentRef() {
        return this._componentRef;
    }

    set allow(allow) {
        this._allow = allow;
    }

    get allow() {
        return this._allow;
    }

    set routerState(state) {
        this._routerState = state;
    }

    get routerState() {
        return this._routerState;
    }

    public unloadComponent() {
        this.componentRef.destroy();
    }

    public destroyComponentAndAllowNavigation() {
        this.componentRef.destroy();
        this.router.navigate([this.routerState]);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，为了使用`createComponent`函数，我们的组件需要是模块
中的一个`entryComponents`

```
 entryComponents: [
        ConfirmationDialogComponent
    ] 
```

Enter fullscreen mode Exit fullscreen mode

抱歉，如果有任何未使用的财产留下，我试图清理。这整个例子是从现实世界的应用程序中提取的