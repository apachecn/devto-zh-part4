# 将格式赋予您的 gcloud 输出

> 原文：<https://dev.to/googlecloud/giving-format-to-your-gcloud-output-57gm>

使用谷歌云命令行工具( [gcloud cli](https://cloud.google.com/sdk/gcloud/) )时，根据调用的命令不同，经常会得到各种形式的输出。

比如一个通用命令，比如列出你当前的 gcloud 配置:

```
$ gcloud config list
[core]
account = katie@glasnt.com
disable_usage_reporting = True
project = glasnt-unicodex
[run]
region = us-central1 
```

或者更具体一些，比如您的项目当前启用了哪些 GCP 服务:

```
$ gcloud services list --enabled
NAME                              TITLE
bigquery-json.googleapis.com      BigQuery API
cloudapis.googleapis.com          Google Cloud APIs
cloudbuild.googleapis.com         Cloud Build API
... 
```

但是有一些方法可以改变这个输出，以获得一致的、对上下文更有用的结果。在这两个例子中，我们有两种不同的默认格式。

如果您追求一致的、特定的格式，或者只对输出的一部分感兴趣，使用`--format`选项可能会对您有用！

* * *

> ## Every API is different, but using these steps will help you reflect on each API quickly and help you build a command to help you.

对于这里的所有输出，我们将使用最新版本的`gcloud`的输出。我们的终端输入将以`$`开头，输出将保持原样，除了为清楚起见特别截断的地方，这些地方将标有`...`。例如:

```
$ gcloud --version
Google Cloud SDK 257.0.0
alpha 2019.05.17
beta 2019.05.17
... 
```

让我们从前面列出的服务开始。

```
$ gcloud services list
NAME                              TITLE
bigquery-json.googleapis.com      BigQuery API
cloudapis.googleapis.com          Google Cloud APIs
cloudbuild.googleapis.com         Cloud Build API
... 
```

这个输出是什么呢？`--help`能给我们更多的洞察力。

```
$ gcloud services list --help

NAME
    gcloud services list - list services for a project

SYNOPSIS
    gcloud services list [--available | --enabled] [--filter=EXPRESSION]
        [--limit=LIMIT] [--page-size=PAGE_SIZE] [--sort-by=[FIELD,...]]
        [GCLOUD_WIDE_FLAG ...]

DESCRIPTION
    This command lists the services that are enabled or available to be enabled
    by a project. You can choose the mode in which the command will list
    services by using exactly one of the --enabled or --available flags.
    --enabled is the default.

FLAGS
     At most one of these may be specified:

       --available
          Return the services available to the project to enable. This list
          will include any services that the project has already enabled.

       --enabled
          (DEFAULT) Return the services which the project has enabled.

LIST COMMAND FLAGS
     --filter=EXPRESSION
        Apply a Boolean filter EXPRESSION to each resource item to be listed.
        If the expression evaluates True, then that item is listed. For more
        details and examples of filter expressions, run $ gcloud topic filters.
        This flag interacts with other flags that are applied in this order:
        --flatten, --sort-by, --filter, --limit.

     --limit=LIMIT
        Maximum number of resources to list. The default is unlimited. This
        flag interacts with other flags that are applied in this order:
        --flatten, --sort-by, --filter, --limit.

     --page-size=PAGE_SIZE
        Some services group resource list output into pages. This flag
        specifies the maximum number of resources per page. The default is
        determined by the service if it supports paging, otherwise it is
        unlimited (no paging). Paging may be applied before or after --filter
        and --limit depending on the service.

     --sort-by=[FIELD,...]
        Comma-separated list of resource field key names to sort by. The
        default order is ascending. Prefix a field with ``~'' for descending
        order on that field. This flag interacts with other flags that are
        applied in this order: --flatten, --sort-by, --filter, --limit.

GCLOUD WIDE FLAGS
    These flags are available to all commands: --account, --billing-project,
    --configuration, --flags-file, --flatten, --format, --help,
    --impersonate-service-account, --log-http, --project, --quiet,
    --trace-token, --user-output-enabled, --verbosity. Run $ gcloud help for
    details.

EXAMPLES
    To list the services the current project has enabled for consumption, run:

        $ gcloud services list --enabled

    To list the services the current project can enable for consumption, run:

        $ gcloud services list --available

NOTES
    These variants are also available:

        $ gcloud alpha services list
        $ gcloud beta services list 
```

是的，这是一个很长的输出。但是让我们一步一步来。

这里的 FLAGS 部分非常有用，因为它向我们展示了`gcloud services list`命令的默认设置是只显示启用的输出。

如果我们向它询问所有可用的服务，我们会得到一个非常不同的列表。

```
$ gcloud services list --available
NAME                                                  TITLE
abusiveexperiencereport.googleapis.com                Abusive Experience Report API
acceleratedmobilepageurl.googleapis.com               Accelerated Mobile Pages (AMP) URL API
... 
```

这个名单真的很长。多久了？我们可以通过实用程序来检查这个命令，这个实用程序将[计算行数](https://linux.die.net/man/1/wc)。

```
$ gcloud services list --available | wc -l
244 
```

这里有用的是一个服务列表，显示哪些服务已启用，哪些服务未启用。目前，我们只能获得其中之一:所有启用的服务，或所有可用的服务。查看所有可用的服务以及它们是被启用还是被禁用将是有用的。

* * *

从前面我们可以看到，我们有一系列 GCLOUD WIDE 标志，我们今天要关注的是`--format`。

```
$ gcloud help
...
     --format=FORMAT
        Set the format for printing command output resources. The default is a
        command-specific human-friendly output format. The supported formats
        are: config, csv, default, diff, disable, flattened, get, json, list,
        multi, none, object, table, text, value, yaml. For more details run $
        gcloud topic formats.
... 
```

根据哪种数据格式对您来说是最可读的，您可以使用一种让您深入了解相关命令的数据结构的格式，然后我们可以对其进行自省，以获得我们想要关注的值(在我们的例子中，是服务名，以及它的状态(status？状态？让我们来了解一下！))

```
$ gcloud services list --available --format=json

[
  {
    "config": {
      "authentication": {},
      "documentation": {
        "summary": "A data platform for customers to create, manage, share and query data."
      },
      "name": "bigquery-json.googleapis.com",
      "quota": {},
      "title": "BigQuery API",
      "usage": {
        "requirements": [
          "serviceusage.googleapis.com/tos/cloud"
        ]
      }
    },
    "name": "projects/1009201337834/services/bigquery-json.googleapis.com",
    "state": "ENABLED"
  },
   {
    "config": {
      "authentication": {},
      "documentation": {
        "summary": "This is a meta service for Google Cloud APIs for convenience. Enabling this service enables all commonly used Google Cloud APIs for the project. By default, it is enabled for all projects created through Google Cloud Console and Google Cloud SDK, and should be manually enabled for all other projects that intend to use Google Cloud APIs. Note: disabling this service has no effect on other services.\n"
      },
      "name": "cloudapis.googleapis.com",
      "quota": {},
      "title": "Google Cloud APIs",
      "usage": {
        "requirements": [
          "serviceusage.googleapis.com/tos/cloud"
        ]
      }
    },
    "name": "projects/1009201337834/services/cloudapis.googleapis.com",
    "state": "ENABLED"
  },
... 
```

我们可以看到我们有一个对象数组(一个字典列表，一些丰富的对象；您的描述术语可能会有所不同)。不过，我们可以看到对我们有用的字段:标题和州。

我们有一个格式化选项是“值”，一个用制表符分隔的值列表。这对于调试我们的查询来说是一种很好的简写形式，因为它很容易在终端中阅读。

阅读格式的格式部分也是有用的(正如前面在`--format`标签描述中所建议的)。

```
$ gcloud topic formats

...
    A format expression has 3 parts:

     NAME
        name
     ATTRIBUTES
        [ [no-]attribute-name[=value] [, ... ] ]
     PROJECTION
        ( resource-key [, ...] )

    NAME is required, ATTRIBUTES are optional, and PROJECTIONS may be required
    for some formats. Unknown attribute names are silently ignored.
... 
```

`value`格式类型有一个属性“separator”，我们需要用下面的格式指定:

```
--format='value[separator=","](thing,anotherthing...)' 
```

**这里重要提示**:我们传递给`--format`的参数是一个复杂的字符串，所以必须用引号括起来。如果我们不这样做，我们的终端将会因为括号和方括号而变得*非常*混乱。由于我们必须声明逗号作为分隔符，我们需要使用另一组引号。我们也可以避开引号，但是这个版本看起来更干净。

因此，如果我们想要标题和状态，我们需要通过它们的全名来指定字段:

*   config.title 和
*   状态

```
$ gcloud services list --available --format="value[separator=' → '](config.title,state)"

Abusive Experience Report API → DISABLED
Accelerated Mobile Pages (AMP) URL API → DISABLED
...
BigQuery API → ENABLED
... 
```

在最后一个例子中，我使用了一个不常见的分隔符，只是为了强调您可以使用的范围。

您想要的输出格式在很大程度上取决于使用输出的内容。

一个 shell 脚本？也许应该是`--format=csv`有限定值。

内部自述？`--format=table`可以相当酷。

```
$ gcloud services list --available \
      --format="table[box,margin=3,title='✨ Enabled Services ✨'](config.title,state)"

┌───────────────────────────────────────────────────────────────┐
│                     ✨ Enabled Services ✨                    │
├────────────────────────────────────────────────────┬──────────┤
│                       TITLE                        │  STATE   │
├────────────────────────────────────────────────────┼──────────┤
│ Abusive Experience Report API                      │ DISABLED │
│ Accelerated Mobile Pages (AMP) URL API             │ DISABLED │
...

│ BigQuery API                                       │ ENABLED  │
...

│ Zync Render API                                    │ DISABLED │
└────────────────────────────────────────────────────┴──────────┘ 
```

`gcloud topic formats`帮助也给出了许多有用调用的例子。比如:

```
 List the project authenticated user email address:

        $ gcloud info --format="value(config.account)"

    List the URIs for all compute instances:

        $ gcloud compute instances list --format="value(uri())" 
```

希望使用这些发现技巧可以帮助您以相对和及时的发现轻松地自省您的系统！

* * *

你可以在 [GCP 博客上阅读更多关于使用`gcloud --format`的信息。](https://cloud.google.com/blog/products/gcp/filtering-and-formatting-fun-with)