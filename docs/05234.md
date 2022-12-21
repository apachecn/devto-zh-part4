# 使用环境变量的 Python 雪花连接器

> 原文：<https://dev.to/bhuvanakundumani/snowflake-connector-for-python-using-environment-variables-206j>

[![Alt Text](img/bc9a1f508bd69983a75e0400d8ad17a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7AOozzdq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0soe1ikjmnh36ihevny4.png)

这篇博客介绍了使用环境变量从 Python 应用程序安全地连接到雪花的步骤。我们都知道硬编码登录凭证非常不好，应该不惜一切代价避免。硬编码的凭证给网络攻击者和黑客提供了一个简单的途径。让我们看看如何使用环境变量来安全地传递登录凭证。

什么是环境变量？

环境变量是在程序外部设置的变量，通常通过操作系统内置的功能来设置。基本上，环境变量是驻留在本地系统中的键值对(在程序之外)。一个. env 文件基本上包含所有的环境变量。

基本用法:

在项目的根目录下创建一个. env 文件。├──·├──。包封/包围（动词 envelop 的简写）

如下所示，将您的敏感雪花凭据弹出到该文件中。
注:。应该使用忽略 env 文件。使用 git 进行版本控制时忽略文件。下面给出了关于如何做到这一点的更多信息。

SF_ACCOUNT =
SF_USER = <【雪花 _ 用户名】>
SF_WAREHOUSE = <【仓库 _ 名称】>
SF_DATABASE = <【数据库 _ 名称】>
SF_SCHEMA = <【模式 _ 名称】>
SF_PASSWORD = <【雪花 _ 密码】>

在我们的 python 程序中使用 dotenv:

使用包 python-dotenv 读取环境变量非常容易。要安装，请运行命令— pip install python-dotenv
要加载环境变量，请打开一个文件名为 snowflake_connector.py 的文件，然后键入以下命令，如图所示。

从 os.path 导入 join，dirname
从 dotenv 导入 load_dotenv

dotenv _ path = join(dirname(**file**)，'。env ')
load _ dotenv(dotenv _ path)

# 从取得国书。包封/包围（动词 envelop 的简写）

SF _ ACCOUNT = OS . getenv(' SF _ ACCOUNT ')
SF _ USER = OS . getenv(' SF _ USER ')
SF _ WAREHOUSE = OS . getenv(' SF _ WAREHOUSE ')
SF _ DATABASE = OS . getenv(' SF _ DATABASE ')
SF _ SCHEMA = OS . getenv(' SF _ SCHEMA ')
SF _ PASSWORD = OS . getenv(' SF _ PASSWORD ')

python 的雪花连接器:

PyPI 中提供了用于 python 的雪花连接器。要安装连接器，执行以下命令:
pip install —升级雪花连接器—python
(用于 Python 的雪花连接器需要 Python 2.7.9(或更高版本)或 3.5.0(或更高版本))

导入雪花.连接器

# 触发一个雪花连接的实例

connection = snow flake . connector . connect(
ACCOUNT = SF _ ACCOUNT，
user = SF_USER，
password = SF_PASSWORD，
warehouse = SF_WAREHOUSE，
database = SF_DATABASE，
schema = SF_SCHEMA

连接到雪花:

使用命令 import snowflake.connector 导入 snowflake.connector 模块。您可以使用默认身份验证器，使用所需的登录参数连接到 Snowflake。我们已经使用安全地传递了我们的凭据。环境文件。我们准备在数据库中创建表。示例代码创建了一个包含两列的表 test _ table——col 1 integer 和 col2 string。

cs = connection.cursor()

try:
cs.execute(
“创建或替换表”
“test _ TABLE _ 1(col 1 integer，col 2 string)”)

除异常为 e:
提升 e
最后:
cs.close()

connection.close()

我们已经使用 python 的连接器安全地建立了与我们的雪花帐户的连接。

。gitignore 代表 git

如果您使用 git 进行版本控制，您应该忽略这一点。env 文件在。gitignore 文件。在项目的根目录下创建一个. gitignore 文件。├──·├──。婀
├──。gitignore

在中添加以下行。gitignore 避开 venv，。git 存储库中的 env 文件。

代码请参考[https://github.com/bhuvanakundumani/snowflake_connector](https://github.com/bhuvanakundumani/snowflake_connector)。