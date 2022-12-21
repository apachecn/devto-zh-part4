# Web Atoms Core 中受创新启发的 REST 服务

> 原文：<https://dev.to/web-atoms/retrofit-inspired-rest-services-in-web-atoms-core-1nap>

# 服务声明

```
@DISingleton()
@BaseUrl("/api/v2020/app/")
export default class TaskService extends BaseService {

    @Get("user")
    public getUser(): Promise<IUser>;

    @Get("tasks")
    public getTasks(
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<ITask[]>

    @Get("tasks/{id}/attachments")
    public getAttachments(
        @Path("id") id: number
    ): Promise<ITaskAttachment[]>;

    @Put("tasks")
    public createTask(@Body task: ITask): Promise<ITask>;

    @Post("tasks/{id}/attachments")
    public uploadAttachment(
        @Path("id") id: number,
        @Body att: IAttachment,
        cancelToken: CancelToken): Promise<void>;
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，配置 REST 服务非常容易。

## 缓存

```
 @Get("tasks", { jsCacheSeconds: 900 })
    public getTasks(
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<ITask[]> 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中缓存响应 900 秒。

## 基于结果的缓存

```
 @Get("tasks", { jsCacheSeconds: (r) => r.length ? 900 : 0 })
    public getTasks(
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<ITask[]> 
```

Enter fullscreen mode Exit fullscreen mode

仅当返回的数组有任何项时才缓存响应。

## 固定表头

```
 @Get("tasks", {
            headers: {
               "x-cache": "none",
               "accept": "application/json"
            }
    })
    public getTasks(
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<ITask[]> 
```

Enter fullscreen mode Exit fullscreen mode

## 参数中的表头

```
 @Get("tasks")
    public getTasks(
        @Header("x-auth") auth: string,
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<ITask[]> 
```

Enter fullscreen mode Exit fullscreen mode

## Json 解析选项

```
 @Get("tasks", { 
       jsonOptions: {
          namingStrategy: "underscore",
          indent: 2,
          dateConverter: {
             regex: dateFormatRegex,
             valueConverter: {
                fromSource:(v: string) => Date,
                fromTarget:(date: Date) => string
             }
          }
       }
    })
    public getTasks(
        @Header("x-auth") auth: string,
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<ITask[]> 
```

Enter fullscreen mode Exit fullscreen mode

# 返回表头！！

```
 @Get("tasks", { 
       returnHeaders: true
    })
    public getTasks(
        @Header("x-auth") auth: string,
        @Query("search") search: string,
        // default value should be specified in
        // decorator and not in argument declaration
        @Query("status", "open") status?: string
    ): Promise<IApiResponse<ITask[]>> 
```

Enter fullscreen mode Exit fullscreen mode

唯一不同的是，结果类型总是`IApiResponse<T>`，其中包含`headers`和`value`。

# 嘲讽

模拟服务使得单元测试和设计时开发变得非常容易。

```
@DISingleton({ mock: "./mocks/MockTaskService" })
@BaseUrl("/api/v2020/app/")
export default class TaskService extends BaseService {
... 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以将`MockTaskService`保存在`mocks`文件夹中。并重写每个方法以返回设计时数据。

## 潜入样本

[https://www.webatoms.in/samples.html#contextId=0](https://www.webatoms.in/samples.html#contextId=0)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[we B- atoms](https://github.com/web-atoms)/[core](https://github.com/web-atoms/core)

### MVVM 浏览器 JavaScript 框架。表单，写 TSX/TypeScript 代替 Xaml 和 C#，热重装 Live 发布的 Xamarin。表单应用程序。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Action Status](img/cfa2fdbb651ac020b318dc872b0e8974.png)](https://github.com/web-atoms/core/actions)[![npm version](img/281097dbfee702a2db5f84f4ea1dd388.png)](https://badge.fury.io/js/%40web-atoms%2Fcore)[![codecov](img/6cd68ebe76fa6f8061493408508cc5a5.png)](https://codecov.io/gh/web-atoms/core)

# 网络原子核心

Web Atoms Core 是一个 UI 抽象框架和强大的 MVVM 模式，用于设计现代 Web 和移动应用程序。

## Xamarin。表单功能

1.  使用 VS 代码开发 Xamarin。形式
2.  编写 TypeScript 而不是 C#
3.  写 TSX (JSX)而不是 Xaml
4.  已发布应用的实时热重新加载

## Web 功能

1.  抽象原子成分
2.  抽象设备 API(浏览器服务、消息广播)
3.  没有 CSS 的主题和样式支持
4.  一次性、单向和双向绑定支持
5.  简单依赖注入
6.  内置简单的单元测试框架
7.  UMD 模块支持
8.  具有强大验证功能的全功能 MVVM 框架

## 文件夹结构

1.  网站的所有视图必须放在“src”文件夹内的“web”文件夹下。
2.  Xamarin 表单的所有视图必须放在“src”文件夹内的“xf”文件夹下。

### 示例文件夹结构

```
src
+--images
|  +--AddButton.svg
|
+--view-Models
|  +--TaskListViewModel.ts
|  +--TaskEditorViewModel.ts
|
+--web
|  +--tasks
|     +--TaskListView.tsx
|     +--TaskEditorView.tsx
|
+--xf
   +--tasks
      +--TaskListView.tsx
      +--TaskEditorView.tsx 
```

### 示例视图

…</article>

[View on GitHub](https://github.com/web-atoms/core)