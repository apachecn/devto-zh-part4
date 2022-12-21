# 供应 Spark 集群并在您的笔记本电脑上运行作业

> 原文：<https://dev.to/charlesdlandau/provision-spark-clusters-and-run-jobs-right-in-your-notebook-320n>

我们将使用 GCP SDK 来供应 Spark 集群并在其上运行 PySpark 作业——所有这些都从一台笔记本电脑上完成。事实上，这个文档的来源是一个笔记本。您可以在此处复制此演示:

[https://github.com/CharlesDLandau/sparkAllInOne](https://github.com/CharlesDLandau/sparkAllInOne)

这将需要五到十分钟。我被公共云宠坏了，我甚至不明白这种程度的便利有多疯狂。

在本练习中，我们需要一些先决条件。它们是:

*   带有 python 虚拟环境的项目目录。

```
mkdir sparkDemo && cd sparkDemo
python -m virtualenv venv

// windows
call venv/Scripts/activate

// else
source venv/bin/activate

pip install google-cloud-dataproc jupyterlab 
```

Enter fullscreen mode Exit fullscreen mode

*   GCP 的一个支持计费的项目。

    *   [创建一个项目](https://console.cloud.google.com/iam-admin/projects)
    *   [启用计费](https://cloud.google.com/billing/docs/how-to/modify-project#enable_billing_for_a_project)
    *   记下项目 ID。它被列在 [GCP 主屏幕](https://console.cloud.google.com/home/)的前面和中间(确保你打开了正确的项目)。也可以在附近地区随意编辑来自[的这个列表](https://cloud.google.com/compute/docs/regions-zones/#locations)

```
project_id = 'sparkdemo'
region = 'us-east1-b' 
```

Enter fullscreen mode Exit fullscreen mode

*   一个服务帐户，以及带有其凭证的 JSON 文件

    *   进入 [IAM 控制台](https://console.cloud.google.com/iam-admin)并进入服务账户(左侧菜单)。创建一个新帐户，并合理地填写详细信息。授予服务帐户`dataproc worker`权限。当出现提示时，记得将凭证文件保存为 JSON。我的习惯是将它保存为`client_secrets.json`和**，然后立即将该文件名添加到我的 gitignore 文件中。**

#### 手里拿着的...让我们设置我们的客户

```
from google.cloud import dataproc_v1 
```

Enter fullscreen mode Exit fullscreen mode

```
client = dataproc_v1.ClusterControllerClient.from_service_account_json(
    './client_secrets.json') 
```

Enter fullscreen mode Exit fullscreen mode

上面，我们导入了`dataproc_v1` SDK，并用特殊的`from_service_account_json`方法实例化了一个客户端。

```
# A parser for google's responses 
def response_parser(client_obj, verbose=False):
    responses = []
    for page in client_obj.pages:
        for element in page:
            responses.append(element)

    if not responses and verbose:
        print('No responses found!')

    if responses and verbose:
        for resp in responses:
            print(resp)

    return responses

clusters = client.list_clusters(project_id, 'global')
responses = response_parser(clusters);
len(responses)

>> 0 
```

Enter fullscreen mode Exit fullscreen mode

我们显示调配了零个集群。让我们调配一个集群。

```
## A response handler that we can attach to cluster actions 
def handler(ops):
    result = ops.result()

    # Uncomment this to see results from the calls in this notebook
    # (this can also help you learn config options)
    # print(result) 
```

Enter fullscreen mode Exit fullscreen mode

这里重要的是创建一个`dataproc_v1.types.Cluster`实例。

```
config={'gce_cluster_config':{'zone_uri':region}}
cluster_config = dataproc_v1.types.Cluster(
    project_id=project_id, cluster_name="democluster", config=config) 
```

Enter fullscreen mode Exit fullscreen mode

GCP 使用合理的默认值来配置这个实例，但是您可以使用回调处理程序的打印输出来检查配置字段，并在适当的地方进行覆盖。例如，为了扩大工人的规模，将以下内容添加到`config`:

```
'worker_config':{'num_instances':3} 
```

Enter fullscreen mode Exit fullscreen mode

还要注意的是，`region`是调用`create_cluster`时的`global`，而不是在`gce_cluster_config`中。

```
creating = client.create_cluster(project_id, 'global', cluster_config)
creating.add_done_callback(handler) 
```

Enter fullscreen mode Exit fullscreen mode

如果您在回调中启用了打印，那么您现在只需等待它启动。一旦集群启动，我们可以重复对`list_clusters`的调用，并看到我们现在有一个。

```
clusters = client.list_clusters(project_id, 'global')

responses = response_parser(clusters);
len(responses)

>> 1 
```

Enter fullscreen mode Exit fullscreen mode

万岁！

#### 运行作业

现在让我们设置一个作业控制器。

```
jobber = dataproc_v1.JobControllerClient.from_service_account_json(
    './client_secrets.json'
) 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要放工作文件的地方

```
from google.cloud import storage
import uuid

gs = storage.Client.from_service_account_json('./client_secrets.json')
bucket = gs.create_bucket(f'sparkdemo-{uuid.uuid1()}') 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将代码存储在桶中。如果您安装了 Spark，这将是您可以在本地模式下开始原型开发的部分。现在，只需将下面的代码复制到文件`main.py`中，并保存在项目的根目录下。

```
import pyspark
import sys

sc = pyspark.SparkContext()

# Dummy task terms = ["fodder" for x in range(9000)] + ["spartan", "sparring", "sparrow"]
words = sc.parallelize(terms)
words.filter(lambda w: w.startswith("spar")).take(2)

# Output target was passed to args config field bucket = sys.argv[1]
output_directory = 'gs://{}/pyspark_output'.format(bucket)

# For csv output sqlContext = pyspark.SQLContext(sc)
df = sqlContext.createDataFrame(words, pyspark.sql.types.StringType())
df.coalesce(1).write.format('com.databricks.spark.csv'
            ).options(header='true').save(output_directory) 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个简单的函数，几乎没有修改存储客户端库文档中的样板文件。我们将使用它将文件上传到我们的 bucket，特别是我们的`main.py`文件。

```
def upload_blob(bucket, source_file_name, destination_blob_name):
    """Uploads a file to the bucket."""
    blob = bucket.blob(destination_blob_name)

    blob.upload_from_filename(source_file_name)

    print('File {} uploaded to {}.'.format(
        source_file_name,
        destination_blob_name))

# Use the helper function to upload our code upload_blob(bucket, './main.py', "input_files/main.py")

>> File ./main.py uploaded to input_files/main.py. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的代码已经就绪，我们需要创建一个`dataproc_v1.types.PySparkJob`
的实例

```
pyspark_job = dataproc_v1.types.PySparkJob(
    main_python_file_uri=f"gs://{bucket.name}/input_files/main.py",
    args={bucket.name}
) 
```

Enter fullscreen mode Exit fullscreen mode

`main_python_file_uri`指向我们的代码。如果我们有更多的支持文件要发送，我们也可以通过`python_file_uris`和`file_uris`，但是我们没有。

我们的`main.py`脚本使用`sys.argv[1]`在我们的桶中构建一个输出目录，所以我们将它传递给`args`。

我们还需要将作业放在我们的集群中:

```
placement = dataproc_v1.types.JobPlacement(cluster_name="democluster") 
```

Enter fullscreen mode Exit fullscreen mode

我们准备提交作业。

```
job = jobber.submit_job(project_id, 'global', {'placement': placement,
                                               'pyspark_job':pyspark_job}) 
```

Enter fullscreen mode Exit fullscreen mode

大约一分钟后，工作完成。我们可以这样检查:

```
job_status = jobber.get_job(project_id, 'global', job.job_uuid)
job_status.status

>>    state: RUNNING
>>    state_start_time {
>>      seconds: 1563547231
>>      nanos: 583000000
>>    } 
```

Enter fullscreen mode Exit fullscreen mode

再试...

```
job_status = jobber.get_job(project_id, 'global', job.job_uuid)
job_status.status

>>    state: DONE
>>    state_start_time {
>>      seconds: 1563547265
>>      nanos: 793000000
>>    } 
```

Enter fullscreen mode Exit fullscreen mode

任务完成了。

[![We did it!](img/e4d9cf6e3fd7076484d1e3bdd6c0942c.png)](https://i.giphy.com/media/3o84U9arAYRM73AIvu/giphy.gif)

让我们从我们的桶中下载内容。

```
import os

try:
    os.mkdir('output')
except FileExistsError as e:
    pass

for page in bucket.list_blobs().pages:
    for element in page:
        print(element.name)
        blob = bucket.blob(element.name)
        destination = element.name.split('/')[-1]
        if destination:
            blob.download_to_filename(f"./output/{destination}")

>>    input_files/main.py
>>    pyspark_output/
>>    pyspark_output/_SUCCESS
>>    pyspark_output/part-00000-df4302c1-aec3-415b-84d6-66bd915ae938-c000.csv 
```

Enter fullscreen mode Exit fullscreen mode

我们的输出在本地下载。我们完了，让我们把它都拆了吧。

```
destroying = client.delete_cluster(project_id, 'global', "democluster")
destroying.add_done_callback(handler) 
```

Enter fullscreen mode Exit fullscreen mode

```
# Clean and delete bucket for page in bucket.list_blobs().pages:
    for element in page:
        bucket.delete_blob(element.name)
bucket.delete() 
```

Enter fullscreen mode Exit fullscreen mode

这就是了。火花簇对你来说是一次性的。当你想要一个时，你可以把它旋转到你的信用卡支持的任何大小。在本地制作作业原型，并从笔记本中提交。完成后，把它撕回去。

你为什么想知道如何做到这一点？用于一次性批处理和报告。你什么时候绝对不会用这个？生产中。

#### 参考单据

1.  [https://Google APIs . github . io/Google-cloud-python/latest/data proc/](https://googleapis.github.io/google-cloud-python/latest/dataproc/)
2.  [https://Google APIs . github . io/Google-cloud-python/latest/storage/](https://googleapis.github.io/google-cloud-python/latest/storage/)
3.  [https://spark.apache.org/docs/2.1.0/api/python/](https://spark.apache.org/docs/2.1.0/api/python/)

#### 谢谢！

感谢您的阅读。我希望这对你有所帮助。

<figure>

我一直在学习，所以如果你在这里看到任何错误，我希望你能给我留言，分享你的见解。或者干脆留个条。

<figcaption>Photo by Jez Timms on [Unsplash](https://unsplash.com/photos/_Ch_onWf38o)</figcaption>

</figure>