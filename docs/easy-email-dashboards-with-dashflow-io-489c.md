# 使用 dashflow.io 轻松发送电子邮件

> 原文：<https://dev.to/maximoguerrero/easy-email-dashboards-with-dashflow-io-489c>

如果您需要创建通过数据库驱动的电子邮件发送的仪表板，请查看 [https://dashflow.io](https://dashflow.io) 。它既可以用作命令行工具，也可以用作 python 模块。

即使使用了所有的 BI 工具，我仍然会收到通过电子邮件发送或打印仪表板的请求。像 Tableau 这样的工具不适合打印或电子邮件。当他们整天都在开会的时候，有些人就是没有时间去点击几下鼠标并深入下去。

Dash flow 可以与任何可以与 sqlalchemy 一起使用的 python db-api2 数据库模块一起开箱即用。你的数据库查询都存储在 sql 文件中，它使用了[https://pugsql.org/](https://pugsql.org/)，这可以被最好地描述为一个反向 ORM。完全基于开源模块构建。

电子邮件是通过一个简单的 Json 文件构建的，该文件包含一系列的部分。节可以是表格、图表或混合了数据的文本。

目前它只支持通过 smtp 发送。但是如果你想尝试一下，https://sendgrid.com 有一个不错的免费层。

图表使用[https://quickchart.io/](https://quickchart.io/)渲染。

现在，我们来展示一下发送电子邮件的步骤。

* * *

#### 克隆并设置 dashflow

```
git clone https://github.com/maximoguerrero/dashflow.git
cd dashflow
chmod u+x df-cli.py 
```

#### 为邮件创建文件夹

```
mkdir testemail
cd testemail

mkdir sql

cp ../sample/sample.db sample.db 
```

#### 创建您的 Sql 文件

因为我们使用 pusql，所以 sql 文件的第一行是一个注释，用于定义模块及其返回类型。这里的模块名是***media _ profit _ by _ type _ filtered***，这就是你将在配置文件中使用的。

这个查询只是返回从我们的示例数据库中最有利可图的媒体类型。将该文件保存在我们在上一步
中创建的 **sql** 文件夹中

```
-- :name media_profit_by_type_filtered    :many
SELECT  mt.Name as label, strftime('%Y',InvoiceDate) as year  ,  printf("%.2f", sum(ii.UnitPrice ) )  as value
FROM `tracks` t
inner join `media_types` mt on mt.MediaTypeId = t.MediaTypeId
inner join `invoice_items` ii on ii.TrackId = t.TrackId
inner join `invoices` i on i.InvoiceId = ii.InvoiceId
where mt.Name like '%' || :kind || '%'
group by t.MediaTypeId, year
order by year; 
```

#### 接下来创建一个简单的 json

在这个文件中，我们将提供一些东西。与 sqlAlchemy 兼容的连接字符串，SQL 文件所在的 SQL 文件夹的相对路径。与 quickcharts.io 兼容的服务 URL。

区段数组包含图表的定义，moduleName 是我们在 SQL 文件中定义的。该参数是 SQL 文件中使用的参数。

下一个重要部分是 SMPT 部分，您可以在这里为 SMPT 服务器提供参数。

```
{
    "isDebug": true,
    "connection": "sqlite:///<absolutePath>/sample.db",
    "sqlfolder": "sql/",
    "quickChartsUrl": "https://quickcharts.dashflow.io/chart?bkg=white&c=",
    "title": "This Months Dashboard",
    "description": "Ad reprehenderit amet mollit Lorem aliquip sint anim ipsum nisi deserunt commodo veniam magna.",
    "sections": [
        {
            "type": "chart",
            "sectionTitle": "horizontal bar chart! with query parramter",
            "description": "Irure esse eu officia consequat mollit ullamco est aliquip..",
            "moduleName": "media_profit_by_type_filtered",
            "moduleParameters": [
                {
                    "name": "kind"
                }
            ],
            "groupBy": "year",
            "chart": {
                "type": "horizontalBar"
            }
        }
    ],
    "to": [
        {
            "type": "string",
            "value": "email.to@send.com"
        }

    ],
    "from": "email@from.com",
    "subject": "my first email",
    "sendEngine": {
        "type": "smtp",
        "host": "YOUR_SMTP_HOST",
        "port": "587",
        "enableTLS": true,
        "requiresAuthentication": true,
        "useEnvVariable":{
            "username": "SMTP_USER",
            "password": "SMTP_PWD"

        }
    }
} 
```

#### 接下来让我们运行脚本

```
./df-cli.py --configFile testemail/sample-config.json --parameter="kind:audio" 
```

你会收到以下邮件: [![](img/06b1ac443323b8484e24dbeaf4e51810.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3LxB9Ko5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dashflow.io/dashflow-preview.jpg)