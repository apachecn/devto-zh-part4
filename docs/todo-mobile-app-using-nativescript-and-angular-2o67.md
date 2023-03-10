# 使用 NativeScript 和 Angular 的 ToDo 移动应用程序

> 原文：<https://dev.to/teclogiq/todo-mobile-app-using-nativescript-and-angular-2o67>

NativeScript 是一个开源的跨平台和混合移动框架，用于构建带有 Angular、Vue.js、TypeScript 或 JavaScript 的本机 iOS 和 Android 移动应用程序。

这篇文章的目标是使用 Angular CLI 在 TypeScript 中构建并运行一个简单的 NativeScript Angular 应用程序。

您可以从 GitHub 资源库[下载 NativeScript Angular Todo 移动应用程序的源代码](https://github.com/sanjay-patel/nativescript-todo-app)

**1。NATIVESCRIPT 入门**

在做任何事情之前，您需要设置您的开发环境。

安装 [Node.js 和 npm](https://nodejs.org/en/download/) 如果它们还没有在你的机器上。

如果没有安装 NativeScript，可以通过安装 Node.js 来安装它。

要安装 NativeScript，请运行:

`$ npm install -g nativescript`

最后一步是为您想要部署的每个平台安装开发工具。对于 Android，这是 Android SDK。对于 iOS，是 XCode。您可以遵循 NativeScript 网站上的[安装指南](http://docs.nativescript.org/angular/start/quick-setup),获得关于如何为您的开发环境设置必要软件的更多详细说明。

设置好环境后，执行:

`$ tns doctor`

**2。为 ANDROID 和 IOS 创建新应用**

在我们开始编码之前，我们需要创建一个新项目。这可以通过从命令提示符(Windows)或终端(Linux 和 Mac)执行以下命令来完成:

`$ tns create TodoApp $ cd TodoApp`
`$ tns platform add android $ tns platform add ios`(如果你使用的不是 Mac，就不能为 iOS 平台添加和构建。)

**3。安装必要的本地插件**

[radsidedrawler](http://docs.telerik.com/devtools/nativescript-ui/Controls/Angular/SideDrawer/overview)是一个可以显示隐藏视图的组件，包含导航 UI 或常用设置。使用 drawer UI 的一个流行应用程序是 Android Play store 应用程序。

[rad listview](http://docs.telerik.com/devtools/nativescript-ui/Controls/Angular/ListView/overview)for native script 是一个虚拟化列表组件，它提供了与使用项目列表的场景相关联的最流行的功能。所有这些功能都嵌入在一个控件中，目的是节省开发人员的时间并提供更好的体验。该控件的主要功能包括:

*项目动画
*不同的布局和方向
*滑动、重新排序、拖动、滑动时刷新列表或仅在需要时加载更多项目。

RadDataForm 插件提供了一种简单且通用的方法，用于基于所提供的数据对象的公共成员构建移动表单。使用数据表单可以:

*用一行代码将表单绑定到数据对象
*利用超过 15 个内置编辑器(或提供您自己的自定义编辑器)
*创建编辑器组，允许它们折叠并设置样式
*以只读模式显示表单
*通过内置数据验证控制收集的数据–使用提供的验证器或创建自定义验证器

我们正在创建的应用程序将利用持久存储，这将保存我们的数据，即使我们的应用程序已经关闭。毕竟，我们不想在重启应用程序后丢失待办事项列表数据。

在这个应用程序中，我们使用了 [NativeScript SQLite](https://www.npmjs.com/package/nativescript-sqlite) 。这可以通过从命令提示符(Windows)或终端(Linux 和 Mac)执行以下命令来完成:

`$ tns plugin add nativescript-ui-sidedrawer $ tns plugin add nativescript-ui-listview $ tns plugin add nativescript-ui-dataform $ tns plugin add nativescript-sqlite`

**4。配置角度路由器**

**src/app/app-routing . module . ts**

从“@angular/core”导入{ ng module }；
从' @angular/router '导入{ Routes }；
从‘nativescript-angular/router’导入{ NativeScriptRouterModule }；

const Routes:Routes =[
{
path:' '，
redirectTo: 'todo-list '，
path match:' full '【T4]}，

{
path: 'todo-list '，
loadChildren:'。/todo-list/todo-list . module # TodoListModule '
}，

{
path:' todo-detail '，
loadChildren:'。/todo-detail/todo-detail . module # TodoDetailModule '
}
；

@ ng module({
imports:[NativeScriptRouterModule . for root(routes)]，
exports:[NativeScriptRouterModule]，
})
export class approving module { } `

**5。创建 TODOLISTMODULE 特征模块**

**src/app/todo-list/todo-list . module . ts**

从“@angular/core”导入{ NgModule，NO _ ERRORS _ SCHEMA }；
从‘nativescript-angular/common’导入{ NativeScriptCommonModule }；
从“nativescript-ui-listview/angular”导入{ nativescriptuilistview module }；
从'导入{ TodoListRoutingModule }。/todo-list-routing . module '；
从'导入{ TodoListComponent }。/todo-list . component '；
从'导入{ TodoService }../services/todo . service '；

@ ng module({
imports:[
NativeScriptCommonModule，
TodoListRoutingModule]，
NativeScriptUIListViewModule
，
声明:[TodoListComponent]，
模式:[NO_ERRORS_SCHEMA]，
提供者:[TodoService]
})
导出类 TodoListModule { }

**6。配置功能模块的路线**

**src/app/todo-list/todo-list-routing . module . ts**

从“@angular/core”导入{ ng module }；
从' @angular/router '导入{ Routes }；
从‘nativescript-angular/router’导入{ NativeScriptRouterModule }；
从'导入{ TodoListComponent }。/todo-list . component '；

const Routes:Routes =[
{
path:' '，
component:' TodoListComponent '
}
；

@ ng module({
imports:[NativeScriptRouterModule . for root(routes)]，
exports:[NativeScriptRouterModule]

})
export class TodoListRoutingModule { }

**7。创建 TODOLIST 组件**

**src/app/todo-list/todo-list . component . ts**

从“@angular/core”导入{ Component，OnInit，view child }；
从‘native script-angular/router’导入{ route extensions }；
从‘tns-core-modules/data/observable-array’导入{ observable array }；
从'导入{ DataItem }。/dataItem '；
从‘应用’导入*为 app
从‘native script-ui-side drawer’导入{ RadSideDrawer }；
从'导入{ TodoService }../services/todo . service '；
从“nativescript-ui-listview”导入{ ListViewEventData，rad listview }；
从‘native script-ui-listview/angular’导入{ radlistview component }；
从‘tns-core-modules/ui/core/View’导入{ View }；
从‘ui/dialogs’中导入*作为对话框；

@Component({
选择器:' todo-list '，
moduleId: module.id，
templateUrl:'。/todo-list.component.html '，
style URLs:[' todo-list . component . CSS ']
})

导出类 TodoListComponent 实现 on init {
private _ dataItems:observable array；
公共数据:DataItem[]=[]；
@ view child(' my listview ')listview component:RadListViewComponent；

构造函数(private _ route extensions:route extensions，private todo service:todo service){ }

get dataItems():observable array {
返回此。_ dataItems
}

ngOnInit():void {
this . loadtodolist()；
}

loadTodoList(){
this . todo service . fetch()。然后((res) = > {
这个。_ dataItems = new observable array(RES)；
})；
}

onDrawerButtonTap():void {
const side drawer = app . getroot view()；
side drawer . showdrawer()；
}

create(): void {
this。_ route extensions . navigate(['/todo-detail ']，
{
animated: true，
transition: {
name:'slide '，
duration: 200，
curve:' ease '
}
})；
}

public onCellSwiping(args:ListViewEventData){
var swipe limits = args . data . swipe limits；
var currentItemView = args . object；
var current view；

```
if (args.data.x > 200) {
  console.log('Notify perform left action');
} else if (args.data.x < -200) {
  console.log('Notify perform right action');
} 
```

}

public onSwipeCellStarted(args:ListViewEventData){
var swipe limits = args . data . swipe limits；
var swipe view = args[' object ']；
var leftItem = swipe view . getviewbyid(' edit-view ')；
var right item = swipe view . getviewbyid(' delete-view ')；
swipe limits . left = left item . getmeasuredwidth()；
swipe limits . right = right item . getmeasuredwidth()；
swipe limits . threshold = left item . getmeasuredwidth()/2；
}

public onLeftSwipeClick(args:ListViewEventData){

var id = args . object . binding context . id；
this . listview component . listview . notifyswipetoexecutefined()；

这个。_ route extensions . navigate(['/todo-detail ']，
{
queryParams:{id: id }，
animated: true，
transition: {
name:'slide '，
duration: 200，
curve:' ease '
}
})；
}

public onRightSwipeClick(args){
var id = args . object . binding context . id；
dialogs.confirm({
title:'确认删除'，
message:'您确定要删除此项目吗？'，
okButtonText: 'Ok '，
Cancel button text:' Cancel '
})。然后(result =>{
if(result){
this . todo service . deleterecord(id)。然后((RES)=>{
if(RES . success){
this。_dataItems.splice(this。_ dataitems . index of(args . object . binding context)，1)；
}
})；
}
})；
T16)}
}

**8。运行和调试应用程序**

您可以通过执行以下命令在设备上运行该应用程序:

`$ tns run`

然后是您想要部署的平台。这里有一个 android 的例子:

`$ tns run android`

如果尚未安装 Android 平台，它会自动为您安装，并在安装后在您的 [android 设备](https://developer.android.com/studio/install)上运行应用程序。

应用程序运行后，您可以执行 tns livesync Android–watch，以便在每次更改源文件时自动刷新应用程序。

NativeScript 允许开发人员调试他们的应用程序。这是用 Chrome 开发工具完成的。有两种方法可以做到这一点:

如果已经有应用在运行，可以打开一个新的终端窗口，执行
`$ tns debug android --start`
如果还没有应用在运行，使用
`$ tns debug android --debug-brk`