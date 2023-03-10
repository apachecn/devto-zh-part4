# 使用 Azure 资源管理器模板管理 CNAMEs

> 原文：<https://dev.to/azure/managing-cnames-with-azure-resource-manager-templates-2imj>

所以你购买了自己的域名，并希望在 GitHub 页面上托管你的博客。您发现您需要向您的 DNS 提供商配置一个 [CNAME 记录。](https://help.github.com/en/articles/setting-up-a-custom-subdomain)

> 💡您可以在资源管理器模板中管理其他资源。这篇文章只是有意关注一种特定的资源类型！

先决条件:

*   您的 [GitHub 存储库通过 GitHub Pages](https://help.github.com/en/categories/github-pages-basics) 启用，并与您的 GitHub 存储库的自定义域相关联。
*   Azure 正在托管您的域名。

你可以通过 Azure 中的 Azure 资源管理器模板来管理你的 [CNAMEs](https://docs.microsoft.com/en-us/azure/templates/microsoft.network/2018-05-01/dnszones/cname/?WT.mc_id=devto-blog-jedavis) ,而不是手工制作 DNS 记录。这将允许您在版本控制中对模板进行版本控制、检查和共享。以下假设你已经安装了 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?WT.mc_id=devto-blog-jedavis=&view=azure-cli-latest) 。

资源管理器模板本质上是遵循特定格式的 JSON 文件:

```
{  "$schema":  "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",  "contentVersion":  "",  "parameters":  {  },  "variables":  {  },  "functions":  [  ],  "resources":  [  ],  "outputs":  {  }  } 
```

Enter fullscreen mode Exit fullscreen mode

$schema 属性是顶层模式模板。

*contentVersion* 属性是对模板进行版本控制的一种方式。这就是你在你的模板中跨版本的重要变化的信号。

*resources* 属性是您声明想要部署或更新的资源的地方。

这 3 个属性是资源管理器模板的唯一必需属性。在我的示例 CNAME 模板中，我没有使用其他 JSON 属性。以下是管理单个 CNAME 资源的模板示例: