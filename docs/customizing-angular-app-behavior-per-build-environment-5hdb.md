# 根据构建环境定制 Angular 应用行为

> 原文：<https://dev.to/mainawycliffe/customizing-angular-app-behavior-per-build-environment-5hdb>

如果您使用 angular 的时间很短，那么您可能会遇到环境配置文件。默认情况下，它们是两个，一个用于生产，另一个用于开发。它们允许您指定特定部署环境特有的配置或设置。这可能包括 URL API 端点等配置，甚至是针对所述环境的 Firebase 配置。

例如，Angular 使用环境变量中的`prod`属性在 true 时启用服务工作者，即仅在生产部署中。

```
ServiceWorkerModule.register('ngsw-worker.js', {
  enabled: environment.production
}), 
```

Enter fullscreen mode Exit fullscreen mode

您还可以在此基础上进行构建，以便为不同的目标环境提供更多的配置。比如——`development`、`staging`和`production`。要了解更多关于掌握环境配置的知识，请点击这里查看我之前的[帖子](https://codinglatte.com/posts/angular/environment-variables-angular/)。

在这篇文章中，我们将进一步探讨特定于环境的配置。我们将会看到我们不仅可以定制变量，还可以定制样式、脚本、资产甚至类。为了做到这一点，你需要熟练地修改你的角度工作空间的配置文件`angular.json`。

### 为什么

对于大多数项目，包括我的大部分项目，默认的 Angular CLI 配置都是现成的，不需要或很少需要任何定制。

但是，有时，您可能需要根据构建环境定制 angular 应用程序的行为。例如，你可能想有一个不同的主题，标志等。针对您的 alpha、beta 和生产环境–使您的用户/测试人员能够轻松识别他们所处的环境。

在这种情况下，您可以针对不同的环境使用不同的颜色，同时还可以在预发布版本上更正比稳定版本更多的遥测信息，而大多数用户都将在稳定版本上更正这些信息。

所以，事不宜迟，让我们开始吧:

### 自定义样式和脚本

默认情况下，如果您的 web 应用程序有一些全局样式和脚本，您可以将它们添加到样式数组中。

```
{  ...  "projects":  {  "angular-extreme-build-configuration":  {  ...  "architect":  {  "build":  {  "builder":  "@angular-devkit/build-angular:browser",  "options":  {  ...  "styles":  [  "styles1.scss",  "styles2.scss",  ...  ],  "scripts":  [  "script1.json",  "script2.json",  ...  ]  },  ...  },  ...  }  }  },  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

此配置以及您在本节中所做的其他配置将作为您为应用程序设置的所有构建环境(即生产、服务、暂存)的默认配置。但是在我们的案例中，我们想要的是为不同的目标环境设置不同的风格。

为了实现这一点，我们将为每个环境传递一组不同的样式和脚本。通过在您想要的环境下添加样式和脚本属性，可以做到这一点。每个属性都接受相对于角度工作空间根的文件路径数组。

```
{  ...  "projects":  {  "angular-extreme-build-configuration":  {  ...  "architect":  {  "build":  {  ...  "options":  {  ...  "styles":  [  "src/styles.scss"  //  default  ],  "scripts":  []  },  "configurations":  {  "staging":  {  ...  "styles":  [  "src/styles.staging.scss"  ],  "scripts":  []  },  "production":  {  ...  "styles":  [  "src/styles.prod.scss"  ],  "scripts":  []  }  }  },  ...  }  }  },  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

> **NB:** 任何您没有为其配置脚本和样式的环境都将回退到缺省值。因此，您可以添加默认脚本和样式，然后配置您希望不同的特定环境。

例如，如果您使用 bootstrap 或任何其他 CSS 框架，您可以根据环境对 web 应用程序进行不同的主题化。这使您可以在多个目标环境中自由定制 web 应用程序的外观。您可以拥有一个外观与 staging 完全不同的生产 web 应用程序，但底层代码基本相同。

点击查看演示[，看看这是怎么回事。](https://github.com/mainawycliffe/angular-extreme-build-configuration)

### 针对不同目标环境的独特资产

就像上面的资产和脚本一样，您也可以为您的应用程序提供资产列表。基本上，这些文件不是你的应用程序代码的一部分。当您指定它们时，Angular CLI 会在构建时将它们原样复制到构建目录中。这适用于徽标、图标等。

默认情况下，您指定单个图像或目录，angular 将复制它们，相对于 Angular 工作空间的根。

```
{  ...  "projects":  {  "angular-extreme-build-configuration":  {  ...  "architect":  {  "build":  {  ...  "options":  {  ...  "assets":  [  "src/favicon.ico",  "src/assets"  ],  ...  },  ...  },  ...  }  }  },  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

这对于多环境配置来说不是很好。在这种情况下，您可以向 angular cli 提供有关如何为特定环境复制资产的更多说明。除了文件或目录的简单路径，您还可以指定包含以下字段的字段:

1.  `glob` -指定要复制的文件模式，使用输入目录作为基础
2.  `input` -输入目录，相对于角度工作空间
3.  `output` -输出目录，相对于构建目录
4.  `ignore` -输入目录中要忽略的全局列表

以这个场景为例，您有两个构建环境，登台和生产，每个都有独特的资产。首先，创建两个目录，一个用于生产资产—`prod_assets`，另一个用于暂存资产—`staging_assets`。在构建每个配置的过程中，您需要将生产和登台资产复制到构建目录中的`assets`目录。

在这种情况下，您的暂存资产配置将如下所示:

```
{  ...  "projects":  {  "angular-extreme-build-configuration":  {  ...  "architect":  {  ...  "configurations":  {  "staging":  {  ...  "assets":  [  "src/favicon.ico",  {  "glob":  "**/*",  "input":  "src/staging_assets/",  "output":  "/assets/"  }  ],  ...  },  ...  }  },  ...  }  }  },  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

生产配置如下所示:

```
{  ...  "projects":  {  "angular-extreme-build-configuration":  {  ...  "architect":  {  ...  "configurations":  {  ...  "production":  {  ...  "assets":  [  "src/favicon.ico",  {  "glob":  "**/*",  "input":  "src/prod_assets/",  "output":  "/assets/"  }  ],  ...  }  }  },  ...  }  }  },  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

### 对类型脚本类使用文件替换

文件替换配置允许您在构建 web 应用程序之前，指示 Angular CLI 用另一个文件替换特定文件。例如，您可以用另一个类替换 typescript 类——前提是所需的属性和方法在两个类中都可用——这取决于您的构建目标环境。

事实上，Angular CLI 在使用`--prod`标志进行构建时，使用这个来用`environment.prod.ts`替换默认的`environment.ts`文件。该设置接受对象数组，这意味着您可以进行多个文件替换。该对象有两个属性——replace 和 with，其中第一个是要替换的文件，而后者是替换文件的路径。

```
"fileReplacements":  [  {  "replace":  "path/for/file/to/replace",  "with":  "path/for/file/to/replace/with"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一下这个场景，您有两个日志记录类，在开发过程中，您只想记录到控制台。但是在生产过程中，您希望远程记录日志服务。您可以创建两个类或服务—`logging.ts`和`logging.prod.ts`——并让 angular CLI 在为生产而构建时用生产类替换默认类。

要实现这一点，在`fileReplacements`下进行生产配置，只需要指定要替换的文件以及替换文件，如下图:

```
...  "production":  {  "fileReplacements":  [  {  "replace":  "src/app/logging.ts",  "with":  "src/app/logging.prod.ts"  }  ],  ...  }  ... 
```

Enter fullscreen mode Exit fullscreen mode

我知道使用这样的文件替换有点夸张，但是我没有更好的主意，所以你还是明白了。

关于这个话题，我就说到这里。如果你有更多关于构建配置定制的想法，请使用下面的评论部分，它将对我和这篇文章的未来读者有所帮助。

这篇帖子的所有源代码都可以在 [GitHub](https://github.com/mainawycliffe/angular-extreme-build-configuration) 找到。如果你对我有任何问题，请随时使用下面的评论区或在 [Slack](https://forms.gle/PdD4RcAS8kK6TMbF9) 上给我发消息。