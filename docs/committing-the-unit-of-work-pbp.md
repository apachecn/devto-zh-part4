# 提交工作单元

> 原文：<https://dev.to/afsharm/committing-the-unit-of-work-pbp>

我作为一名开发人员所开发的大多数应用程序都涉及到一种基于数据库操作的工作单元。传统上，一个工作单元在 web 请求开始时开始，在请求结束时结束。通常，数据库事务在 web 请求结束时提交。这是有时需要调整的地方。

在*ASP.NET 网络表单*中有一个地方叫做 *Global.asax* 。就我记忆所及，那里可能已经出现了某种自动化。例如，调用提交被放在这里。但是，如果我没记错的话，如果数据库中发生了不好的事情，客户机根本不会得到通知。例如，当完整性规则由于用户触发的删除操作而在数据库中引发错误时，客户端中没有人会被告知此事。当事务在后端回滚时，请求将返回 200 family。这导致了许多问题，因为用户所做的任何更改都没有保存到数据库中。我记得客户报告的错误就是这种行为的结果。

我在想，在 ASP.NET 几乎不可能实现完美的自动化。然而，一个 SO 问题鼓励我再试一次。首先，我在*ASP.NET Core 2 . x*中创建了 *ActionFilter* 如下:

```
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;
using Microsoft.Extensions.DependencyInjection;
using TestFilter.Data;

namespace TestFilter
{
    public class TestActionFilter : IActionFilter
    {
        readonly ApplicationDbContext _applicationDbContext;
        public TestActionFilter(IServiceProvider serviceProvider)
        {
            _applicationDbContext = serviceProvider.GetRequiredService<ApplicationDbContext>();
        }

        public void OnActionExecuted(ActionExecutedContext context)
        {
            if (context.Exception == null)
            {
                try
                {
                    _applicationDbContext.SaveChanges();
                }
                catch (Exception exception)
                {
                    context.Result = new BadRequestObjectResult($"Error: {exception.Message}");
                    return;
                }
            }
            else
            {
                throw new Exception("Captured in Filter", context.Exception);
            }
        }

        public void OnActionExecuting(ActionExecutingContext context)
        {
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`catch`部分，我可以接收可能的数据库错误，从而将它反映给用户。这样，我就不会被迫在每个动作中添加`SaveChanges`。这个解决方案不仅适用于 ASP.NET*核心 MVC* ，也适用于 ASP.NET*核心 API* 。感谢*ASP.NET 核心*，因为 *MVC* 和 *API* 使用相同的基础控制器。

为了满足我的好奇心，我移除了`SaveChanges()`周围的`try-catch`栅栏，并绑上了导致数据库错误的绳子。令我惊讶的是，错误出现在用户方面，而不是被遗漏。似乎几年来有些事情已经改变了，数据库错误不会再被遗漏了。我在体验中使用了`SQLite`数据库。