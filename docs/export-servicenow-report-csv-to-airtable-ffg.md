# 导出服务现在向 Airtable 报告 CSV

> 原文：<https://dev.to/alanmbarr/export-servicenow-report-csv-to-airtable-ffg>

## ServiceNow 向 Airtable 报告数据

我负责管理平台产品路线图，大部分工作都在 ServiceNow。ServiceNow 最初是一种服务台售票工具。他们似乎将自己定位为一个通用的业务工作流引擎。据我所知，我没有 API 访问权限，但我可以生成报告并将其下载为 CSV 格式。ServiceNow 中似乎有一个模块可以进行敏捷软件开发工作流，但是我没有时间也没有兴趣去了解如何使用这个功能。与此同时，我们使用一个灵活的板，根据任务组将卡片放入我们的视觉任务板。这使得我们的团队能够在看板风格的流程中工作。不幸的是，根据他们的进展生成指标和图表的负担落在了我身上。

## ServiceNow 上报界面

ServiceNow 有一个不错的报告界面，可以很容易地在表格中找到数据，并将数据结构连接在一起。我访问 vtb_card 表，并根据我的团队的分配组填充特定的列。特别是任务。号码，车道，任务。简短描述，任务。状态，标签 1，标签 2，标签 3，标签 4，标签 5，标签 6，标签 7。关于标签最令人讨厌的事情是它们是特定于特定的板，并且不保留它们的名称。但是，绕开它并不可怕。从那里右键单击列名并导出到 CSV。

## 上传到 AirTable 前对数据进行预处理

对于导出的 CSV，我首先要做的一件事是将其转换为 UTF8。默认编码不是 UTF8，我使用 powershell 来帮助实现这一点。我还使用 Google Cloud powershell 库将我转换的 csv 上传到云存储。在这里我可以触发一个无服务器的计算功能来处理 airtable 的上传。这个 powershell 是由一个带有命令的普通批处理脚本触发的。在 python 中可能有一种方法可以做到这一点，但是 PowerShell 非常简单。

```
 PowerShell -NoProfile -ExecutionPolicy Bypass -Command "& './upload\_backlog.ps1'" 
```

```
$srcRoot = "dailybacklogreport\"

$utf8 = New-Object System.Text.UTF8Encoding $false

Get-ChildItem $srcRoot\*  -recurse -Include *.csv | ForEach-Object {
$content = $_ | Get-Content -Raw

Set-Content -Value $utf8.GetBytes($content) -Encoding Byte -Path $_.FullName

}

$fileList = Get-ChildItem -Path $srcRoot -File -Force -Recurse
$file = $fileList | Select-Object -First 1

New-GcsObject -Bucket "alanmbarrdatasyncing" -File $file.FullName -ObjectName "platform_engineering/backlog.csv" -Force
Remove-Item -Path "$srcRoot\*" -Include "*.csv" -Force 
```

一旦上传，它就会触发谷歌云功能来创建文件。我用熊猫来做数据过滤。我只想发送新记录或由于 AirTable 限制而更改的记录。脚本中缺少的是随着时间的推移保持标签同步。这是可能的，但我认为不值得花力气去写一张定制支票。

GC 函数的 requirements.txt 需要包含用于数据处理的库。

```
airtable-python-wrapper==0.12.0
pandas==0.24.2
gcsfs==0.2.3 
```

```
def parse_report(data):
    import pandas as pd
    def filterColumn(row):
        return list(filter(lambda row: row != None, row.values))

    startWithPandas = pd.read_csv('gs://' + data['bucket'] + '/' + data['name'])
    startWithPandas["label_1"] = startWithPandas["label_1"].apply(lambda x: "Defect" if x == True else None )
    startWithPandas["label_2"] = startWithPandas["label_2"].apply(lambda x: "High Priority" if x == True else None )
    startWithPandas["label_3"] = startWithPandas["label_3"].apply(lambda x: "Feature" if x == True else None )
    startWithPandas["label_4"] = startWithPandas["label_4"].apply(lambda x: "Idea" if x == True else None )
    startWithPandas["label_5"] = startWithPandas["label_5"].apply(lambda x: "Request" if x == True else None )
    startWithPandas["label_6"] = startWithPandas["label_6"].apply(lambda x: "Story" if x == True else None )
    startWithPandas["label_7"] = startWithPandas["label_7"].apply(lambda x: "Low Priority" if x == True else None )
    startWithPandas["Labels"] = startWithPandas[["label_1", "label_2", "label_3", "label_4", "label_5", "label_6", "label_7"]].apply(filterColumn,axis=1)
    return startWithPandas.drop(columns=["label_1", "label_2", "label_3", "label_4", "label_5", "label_6", "label_7"])

def parse_airtable_data():
    from airtable import Airtable
    import os, pandas as pd
    base_key = 'app5beEUuaFQatE2T'
    table_name = 'Features & Requests'
    airtable = Airtable(base_key, table_name, api_key=os.environ['AIRTABLE_KEY'])
    res = airtable.get_all(fields=["task.number","task.state","task.short_description","lane"])
    data = []
    for r in res:
        f = r["fields"]
        data.append(
            {"task.number":f["task.number"],
            "task.state":f["task.state"],
            "lane":f["lane"],
            "task.short_description":f["task.short_description"],
            "recordId":r.get("id")
            })

    airtable_data = pd.DataFrame(data)
    return airtable_data

def process_differences(servicenow,airtable_data):
    m = servicenow.merge(airtable_data, on=["task.number","task.state","task.short_description","lane"], how='outer', suffixes=['', '_'], indicator=True)
    differences = m[(m["_merge"]!= "both") ]
    return differences

def upload_to_airtable(differences):
    import os
    from airtable import Airtable
    base_key = 'app5beEUuaFQatE2T'
    table_name = 'Features & Requests'
    airtable = Airtable(base_key, table_name, api_key=os.environ['AIRTABLE_KEY'])
    unique_ids = set(differences["task.number"].values)

    for item in unique_ids:
        row = differences[differences["task.number"] == item]
        if len(row) == 2:
            recordId = row[row["recordId"].notnull()]["recordId"].values[0]
            f = row[~row["recordId"].notnull()]
            fields = f.to_dict(orient='records')[0]
            del fields["recordId"]
            del fields["_merge"]
            if len(fields.get("Labels")) == 1 and fields.get("Labels")[0] == "":
                del fields["Labels"]
            airtable.update(recordId,fields = fields)
        else:
            fields = row.to_dict(orient='records')[0]
            del fields["recordId"]
            del fields["_merge"]
            if isinstance(object, list):
                fields["Labels"]=list(filter(lambda x:x!="",fields["Labels"]))
            else:
                del fields["Labels"]
            airtable.insert(fields)

def upload_airtable_data(event, context):
    """Triggered by a change to a Cloud Storage bucket.
    Args:
         event (dict): Event payload.
         context (google.cloud.functions.Context): Metadata for the event.
    """
    file = event

    print(f"Processing file: {file['name']}.")
    if file['name'] and file['name'].startswith('platform_engineering/'):
        servicenow_frame = parse_report(file)
        airtable_frame = parse_airtable_data()
        diff = process_differences(servicenow_frame,airtable_frame)
        upload_to_airtable(diff)
    print(f"Processing Complete.") 
```