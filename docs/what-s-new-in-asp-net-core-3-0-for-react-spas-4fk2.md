# React SPAs 的 ASP.NET Core 3.0 有什么新功能？

> 原文：<https://dev.to/carlrippon/what-s-new-in-asp-net-core-3-0-for-react-spas-4fk2>

ASP，NET Core 3.0 的发布指日可待。React SPAs 可以利用的新功能有哪些？让我们找出答案...

## MVC 服务注册

在`Startup`类的`ConfigureServices`方法中有一些新的扩展方法来添加 MVC 相关的服务。`AddMvc`将继续照常工作，添加所有 MVC 相关的服务。然而，我们现在有`AddControllers`、`AddControllersWithViews`和`AddRazorPages`，它们为更具体的场景添加了服务:

*   `AddControllers`。当应用程序纯粹是一个 web API，不需要任何服务器端视图或 Razor 页面时，可以使用这种方法