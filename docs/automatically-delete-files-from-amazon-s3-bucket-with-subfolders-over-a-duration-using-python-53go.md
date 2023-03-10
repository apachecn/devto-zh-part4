# 使用 Python 在一段时间内自动删除亚马逊 S3 存储桶及其子文件夹中的文件

> 原文：<https://dev.to/gm456742/automatically-delete-files-from-amazon-s3-bucket-with-subfolders-over-a-duration-using-python-53go>

根据亚马逊:

> 亚马逊网络服务(AWS)是世界上最全面、最广泛采用的云平台，从全球数据中心提供超过 165 种全功能服务。数百万客户——包括发展最快的初创公司、最大的企业和领先的政府机构——信任 AWS 来支持他们的基础设施，变得更加敏捷，并降低成本。

亚马逊提供的众多服务之一是简单的存储服务，俗称 s3。亚马逊 S3 是短期或长期存储文件的好方法。许多机构非常依赖亚马逊 s3(例如 Cloudinary 最近一次检查是在 2019 年 3 月)来存储文件，从 txt 格式的日志文件到叔叔的同父异母姐妹的父亲的奶奶的文件。我未来的儿子放在一起的 gif 照片。你明白我的意思。

它是一种对象存储服务，可提供行业领先的可扩展性、数据可用性、安全性和性能。这意味着各种形状和规模的机构都可以使用它来存储所有用例的数据。亚马逊声称提供有竞争力的价格，但这不是我将要写的内容。过一会儿，出于多种原因，人们可能会想要清除存储在 Amason s3 上的部分(如果不是全部)文件。这些可能包括合规性——当一个机构被要求将某些数据/文件保留一段特定的时间以节省空间时 amazon 会根据你在 s3 上保存的文件大小向你收费，所以你最好删除不用的文件，只是为了好玩。

为什么不直接去 s3 控制台删除你想要的文件，或者写一个 shell 代码递归删除这些文件呢？当你有超过 200 万份长期保存的文件记录时，祝你好运。也许我儿子的叔叔的阿姨的朋友可以帮助你。

我面临的任务是从公司的 Amazon s3 存储桶中移除一些文件，下面是我的做法。在一个帮我清理的@DavidOlinsky 的帮助下。AWSCLI 没有太大帮助，因为我需要控制我删除的内容和遗漏的内容。这段代码是用 python 写的。我对一个`Bucket Structure`的假设是他们的文件日期如下:

```
├── [ 160 Jun 10 15:05]  my_s3_bucket
│   ├── [ 128 Jun 10 15:05]  level-one-folder1
│   │   └── [ 608 Jun 10 15:18]  another-sub-folder
│   │       ├── [   0 Mar 26  2015]  file1.jpg
│   │       ├── [   0 Mar 26  2015]  file10.jpg
│   │       ├── [   0 Mar 26  2015]  file2.jpg
│   │       ├── [   0 Mar 26  2015]  file3.jpg
│   │       ├── [   0 Mar 26  2015]  file4.jpg
│   │       ├── [   0 Mar 26  2015]  file5.jpg
│   │       ├── [   0 Mar 26  2015]  file6.jpg
│   │       ├── [   0 Mar 26  2015]  file7.jpg
│   │       ├── [   0 Mar 26  2015]  file8.jpg
│   │       ├── [   0 Mar 26  2015]  file9.jpg
│   │       ├── [ 416 Jun 10 15:19]  folder-inside-sub-folder
│   │       │   ├── [   0 Mar 26  2010]  culp1.test
│   │       │   ├── [   0 Mar 26  2010]  culp10.test
│   │       │   ├── [   0 Mar 26  2010]  culp2.test
│   │       │   ├── [   0 Mar 26  2010]  culp3.test
│   │       │   ├── [   0 Mar 26  2010]  culp4.test
│   │       │   ├── [   0 Mar 26  2010]  culp5.test
│   │       │   ├── [   0 Mar 26  2010]  culp6.test
│   │       │   ├── [   0 Mar 26  2010]  culp7.test
│   │       │   ├── [   0 Mar 26  2010]  culp8.test
│   │       │   └── [   0 Mar 26  2010]  culp9.test
│   │       ├── [   0 Jun 10 15:17]  newer1.config
│   │       ├── [   0 Jun 10 15:17]  newer2.config
│   │       ├── [   0 Jun 10 15:17]  newer3.config
│   │       ├── [   0 Jun 10 15:17]  newer4.config
│   │       └── [   0 Jun 10 15:17]  newer5.config
│   └── [ 384 Jun 10 15:35]  level-one-folder2
│       ├── [   0 Mar 26  2005]  old1.txt
│       ├── [   0 Mar 26  2005]  old10.txt
│       ├── [   0 Mar 26  2005]  old2.txt
│       ├── [   0 Mar 26  2005]  old3.txt
│       ├── [   0 Mar 26  2005]  old4.txt
│       ├── [   0 Mar 26  2005]  old5.txt
│       ├── [   0 Mar 26  2005]  old6.txt
│       ├── [   0 Mar 26  2005]  old7.txt
│       ├── [   0 Mar 26  2005]  old8.txt
│       └── [   0 Mar 26  2005]  old9.txt 
```

# 要求

1.  `boto3`
2.  `time`
3.  `sys`

你可以通过运行`pip`或`conda install boto3`中的`pip install boto3`来安装 boto3，或者通过任何你能够安装 python 模块的方式。Boto3 是亚马逊自己的 python 库，用于访问他们的服务。你可以去 https://aws.amazon.com/了解所有关于他们图书馆和服务的信息。

# 进口

```
import boto3
import time
import sys 
```

我们正在导入`boto3`以便能够访问我们的 s3 服务，导入`time`以便在`now`期间(可以是任何设定的时间)作为检查点，导入`sys`以便向终端生成错误输出。

让我们设定变量。请注意占位符值。

```
# todays\'s epoch _tday = time.time()
duration = 86400*180 #180 days in epoch seconds
#checkpoint for deletion _expire_limit = tday-duration
# initialize s3 client s3_client = boto3.client('s3')
my_bucket = "my-s3-bucket"
my_ftp_key = "my-s3-key/"
_file_size = [] #just to keep track of the total savings in storage size 
```

以上应该是直截了当的评论，但如果你需要更多的解释，你可以继续阅读这一段。如果你好奇的话，可以查阅 Python 的 [PEP 8](https://pep8.org/#descriptive-naming-styles) 命名风格指南，了解下划线的原因。我是这个领域的学生，所以请原谅我的错误。

我们可以为我们的应用程序设置一个特定的当前时间，但为了大多数人的利益，我将只使用`_tday`作为当前日期。

`duration`是文件检查到的 [UNIX 纪元](https://www.epochconverter.com/)时间(任何早于该日期的文件都将被删除)。这里我们将 UNIX 纪元秒设置为 180 天(6 个月)。

将只是当前日期和我们需要的持续时间之间的差异。任何最后修改日期小于该值的文件都是旧文件，将被删除。

`s3_client`将调用底层[亚马逊 s3 客户端](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#client)的方法/功能。

`my_bucket`将是包含文件的存储桶的名称。文件可以位于存储桶内的子文件夹中。别担心，我们会处理的。

`my_ftp_key`将是您要从中搜索文件的存储桶中特定子文件夹的名称。如果你没有子文件夹，你可以忽略我提到的任何东西`key`(作为`Prefix`传递)。

我们将使用`_file_size`在`bytes`中保存所有已处理文件的大小。

`_del_size`会随着文件的删除而不断重置，即使出现错误，也能帮助我们确定删除的总大小。

接下来是我们的功能。

# 功能

```
#works to only get us key/file information def get_key_info(bucket="my-s3-bucket", prefix="my-s3-key/"):

    print(f"Getting S3 Key Name, Size and LastModified from the Bucket: {bucket} with Prefix: {prefix}")

    key_names = []
    file_timestamp = []
    file_size = []
    kwargs = {"Bucket": bucket, "Prefix": prefix}
    while True:
        response = s3_client.list_objects_v2(**kwargs)
        for obj in response["Contents"]:
            # exclude directories/folder from results. Remove this if folders are to be removed too
            if "." in obj["Key"]:
                key_names.append(obj["Key"])
                file_timestamp.append(obj["LastModified"].timestamp())
                file_size.append(obj["Size"])
        try:
            kwargs["ContinuationToken"] = response["NextContinuationToken"]
        except KeyError:
            break

    key_info = {
        "key_path": key_names,
        "timestamp": file_timestamp,
        "size": file_size
    }
    print(f'All Keys in {bucket} with {prefix} Prefix found!')

    return key_info

# Check if date passed is older than date limit def _check_expiration(key_date=_tday, limit=_expire_limit):
    if key_date < limit:
        return True

# connect to s3 and delete the file def delete_s3_file(file_path, bucket=my_bucket):
    print(f"Deleting {file_path}")
    s3_client.delete_object(Bucket=bucket, Key=file_path)
    return True

# check size deleted def _total_size_dltd(size):
    _file_size.append(size)
    _del_size = round(sum(_file_size)/1.049e+6, 2) #convert from bytes to mebibytes
    return _del_size 
```

`get_key_info`函数接受两个参数，一个`Bucket`名称和一个`Prefix`，它们都将被传递给名为 [list_objects_v2](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.list_objects_v2) 的 s3 客户端方法。这个方法接受两个参数，其中一个是`ContinuationToken`。`list_objects_v2`方法每次调用最多只能返回 1000 条记录。Amazon 使用了一个[分页](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#paginators)方法，该方法返回给我们一个键，我们可以用它来调用下一组 1000 条记录，以防有更多的文件需要处理。

为了清楚起见，让我们想象有一本大约 10 页的书。每次你打开一页，你只能看到那一页上的内容。你知道有更多的页面，因为在右下角有一个文本，上面写着`2/10`。考虑到页面上的行从第 1 页延续到第 10 页，每页有 10 行，我们知道第 2 页将从第 11 页开始到第 20 页，第 5 页将从第 41 页开始到第 50 页。

这就是`list_objects_v2`的工作方式。每次我们调用时，它都会返回下一页的起始号码，这样我们就可以进行下一次调用，告诉`list_objects_v2`从该号码开始获取下一个集合。

这是在 while 循环中实现的，在每次调用后，我们都试图重置`ContinuationToken`，直到我们遇到一个阻塞器，它将是一个`KeyError`。

检查响应语法以了解您将从该对象中需要哪种信息是很重要的。在我们的护理中，我们的文件信息保存在`"Contents"`键中。

我们只返回了一个`key_info`对象，因为该函数的工作只是从调用中返回我们需要的相关信息。

`_check_expiration`只接受两个参数。如果没有传递任何东西给它，那么它将使用上述变量的默认值。只有当 s3 上的`LastModified`日期超过设定的持续时间时，它才会为每个文件返回`True`。

`delete_s3_file`接受`file_path`，它是 s3 上文件的路径，从键(前缀)开始。如果没有传递存储桶名称，它将默认为 my_bucket。

`_total_size_dltd`以 MB 为单位跟踪已删除文件的存储大小。一个简单的谷歌转换工具可以帮上忙。在任何时间点，如果有任何错误，如果一个文件被删除，我们将看到总大小被删除。

最后，我们需要运行我们的应用程序。这个应用程序可以从任何其他项目中调用/引用，只要你明白每个部分是什么。我们将从直接运行我们保存的 python 文件开始，因此下面的代码:

```
if __name__ == "__main__":
    try:
        s3_file = get_key_info()
        for i, fs in enumerate(s3_file["timestamp"]):
            file_expired = _check_expiration(fs)
            if file_expired: #if True is recieved
                file_deleted = delete_s3_file(s3_file["key_path"][i])
                if file_deleted: #if file is deleted
                    _del_size = _total_size_dltd(s3_file["size"][i])

        print(f"Total File(s) Size Deleted: {_del_size} MB")
    except:
        print ("failure:", sys.exc_info()[1])
        print(f"Total File(s) Size Deleted: {_del_size} MB") 
```

如果你的桶名是`my_bucket`并且你的前缀或键是`my_ftp_key`，那么在不传递任何参数的情况下运行这个程序将会遍历`my_bucket`中的文件夹`my_ftp_key`，并且删除从你运行这个程序起超过 180 天的所有文件。

我现在将演示如何从我们假定的文件夹结构中删除旧文件。

1.  如果我们决定删除`folder-inside-sub-folder`中的旧文件，只有这将是我们的电话。

```
if __name__ == "__main__":
    try:
        # the difference is right here
        s3_file = get_key_info("my_s3_bucket", "folder-inside-sub-folder")
        for i, fs in enumerate(s3_file["timestamp"]):
            # you can pass the duration in epoch as second parameter
            file_expired = _check_expiration(fs)
            if file_expired:
                file_deleted = delete_s3_file(s3_file["key_path"][i])
                if file_deleted:
                    _del_size = _total_size_dltd(s3_file["size"][i])

        print(f"Total File(s) Size Deleted: {_del_size} MB")
    except:
        print ("failure:", sys.exc_info()[1])
        print(f"Total File(s) Size Deleted: {_del_size} MB") 
```

1.  如果我们想删除来自`level-one-folder1`的所有旧文件，我们将调用

```
if __name__ == "__main__":
    try:
        # the difference is right here
        s3_file = get_key_info("my_s3_bucket", "level-one-folder1")
        for i, fs in enumerate(s3_file["timestamp"]):
            # you can pass the duration in epoch as second parameter
            file_expired = _check_expiration(fs)
            if file_expired:
                file_deleted = delete_s3_file(s3_file["key_path"][i])
                if file_deleted:
                    _del_size = _total_size_dltd(s3_file["size"][i])

        print(f"Total File(s) Size Deleted: {_del_size} MB")
    except:
        print ("failure:", sys.exc_info()[1])
        print(f"Total File(s) Size Deleted: {_del_size} MB") 
```

这将删除`another-sub-folder`和`folder-inside-sub-folder`中的所有旧文件，因为它们在`level-one-folder1`中。但是，如果我们检查 180 天的旧文件，那么另一个子文件夹中的文件`newer1.config`到`newer5.config`将不会被修改，因为它们没有通过过期测试。

我相信您可以想出许多其他方法来使用这些代码调用基于持续时间的 s3 存储桶上的删除，无论是从使用 argv 的流程脚本还是设置每月自动流程来运行维护。以下是摘要

```
import boto3
import time
import sys

# todays\'s epoch _tday = time.time()
duration = 86400*180 #180 days in epoch seconds
#checkpoint for deletion _expire_limit = tday-duration
# initialize s3 client s3_client = boto3.client('s3')
my_bucket = "my-s3-bucket"
my_ftp_key = "my-s3-key/"
_file_size = [] #just to keep track of the total savings in storage size 
#Functions
#works to only get us key/file information def get_key_info(bucket="my-s3-bucket", prefix="my-s3-key/"):

    print(f"Getting S3 Key Name, Size and LastModified from the Bucket: {bucket} with Prefix: {prefix}")

    key_names = []
    file_timestamp = []
    file_size = []
    kwargs = {"Bucket": bucket, "Prefix": prefix}
    while True:
        response = s3_client.list_objects_v2(**kwargs)
        for obj in response["Contents"]:
            # exclude directories/folder from results. Remove this if folders are to be removed too
            if "." in obj["Key"]:
                key_names.append(obj["Key"])
                file_timestamp.append(obj["LastModified"].timestamp())
                file_size.append(obj["Size"])
        try:
            kwargs["ContinuationToken"] = response["NextContinuationToken"]
        except KeyError:
            break

    key_info = {
        "key_path": key_names,
        "timestamp": file_timestamp,
        "size": file_size
    }
    print(f'All Keys in {bucket} with {prefix} Prefix found!')

    return key_info

# Check if date passed is older than date limit def _check_expiration(key_date=_tday, limit=_expire_limit):
    if key_date < limit:
        return True

# connect to s3 and delete the file def delete_s3_file(file_path, bucket=my_bucket):
    print(f"Deleting {file_path}")
    s3_client.delete_object(Bucket=bucket, Key=file_path)
    return True

# check size deleted def _total_size_dltd(size):
    _file_size.append(size)
    _del_size = round(sum(_file_size)/1.049e+6, 2) #convert from bytes to mebibytes
    return _del_size

if __name__ == "__main__":
    try:
        s3_file = get_key_info()
        for i, fs in enumerate(s3_file["timestamp"]):
            file_expired = _check_expiration(fs)
            if file_expired: #if True is recieved
                file_deleted = delete_s3_file(s3_file["key_path"][i])
                if file_deleted: #if file is deleted
                    _del_size = _total_size_dltd(s3_file["size"][i])

        print(f"Total File(s) Size Deleted: {_del_size} MB")
    except:
        print ("failure:", sys.exc_info()[1])
        print(f"Total File(s) Size Deleted: {_del_size} MB") 
```

我希望我能帮助你解决你可能遇到的问题。这个教程是不完整的，除非你批评它，因为它使我努力工作，做得更好。如有必要，请发表评论。您可以随意复制上述内容供自己使用，但要知道，使用这些内容需要您自担风险。对于因您的使用而对您或第三方造成的任何损害，我概不负责。