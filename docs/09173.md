# 使用 Python 连接到 macOS 上的 Microsoft SQL Server 数据库

> 原文：<https://dev.to/petereysermans/things-i-wish-i-knew-before-i-got-started-with-cosmosdb-454e>

总有这样的情况，我想自动化小任务。我喜欢用 Python 来做这类事情，你可以很快地让事情运转起来，没有太多的麻烦。我需要在 Microsoft SQL Server 数据库中执行一些数据库更改，并希望使用 Python 连接到该数据库。在 Windows 上，这通常非常简单。但是我使用 macOS 作为我的主要操作系统，在这个过程中我遇到了一些障碍，所以这里有一个快速的方法。

## 准备中

我从 2017 年找到了一篇[的同题文章。如果](https://www.sinafathi.com/connect-to-ms-sql-on-macos-using-python/)[自制软件](https://brew.sh/index_nl)还没有安装，让我们先这样做。这是一个优秀的 macOS 包管理器。在终端中粘贴以下命令进行安装:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

完成后，运行以下命令来安装用于 SQL Server 的 [Microsoft ODBC 驱动程序 13.1。该驱动程序将用于连接到 MS SQL 数据库。](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-2017#microsoft-odbc-driver-131-for-sql-server)

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/mssql-release https://github.com/Microsoft/homebrew-mssql-release
brew update
brew install msodbcsql@13.1.9.2 mssql-tools@14.0.6.0 
```

## Python 包:pyodbc 或 pypyodbc

安装驱动程序后，我们将使用一个 Python 包，该包使用驱动程序连接到数据库。Python 中有两个常用的包来连接: [`pyodbc`](https://github.com/mkleehammer/pyodbc) 和 [`pypyodbc`](https://github.com/jiangwen365/pypyodbc) 。`pypyodbc`是`pyodbc`的纯 Python 再实现。我采取的主要方式是`pypyodbc`更容易在不同平台上建立。我无法让`pyodbc`工作，它就是无法连接到数据库。

安装`pypyodbc`可以使用 [pip 完成，python 包安装程序](https://pypi.org/project/pip/)。

```
pip install pypyodbc 
```

## 代码

现在已经安装了必要的组件，让我们打开我们最喜欢的代码编辑器并编写代码。首先需要做的是导入`pypyodbc`。该脚本以此开头:

```
import pypyodbc 
```

然后我们需要一个到数据库的连接:

```
sqlConnection = pypyodbc.connect(
                "Driver={ODBC Driver 13 for SQL Server};"
        "Server=<server IP address>;"
        "Database=<database>;"
        "uid=<username>;pwd=<password>"); 
```

注意，你必须替换这段代码中的四个值:`server IP address`、`database`、`username`和`password`。驱动程序的值是一个硬编码值，它指示使用什么驱动程序连接到数据库，该值指向先前安装的驱动程序。

现在剩下的就是使用连接并运行查询。

```
cursor = sqlConnection.cursor()
cursor.execute("select * from Values") 
```

光标现在包含我们的查询结果，属性`cursor.rowcount`返回查询返回的行数。现在可以遍历行并访问不同的列:

```
for row in cursor:
    print(cursor)
    # first column
    firstColumn = row[0]
    # ... 
```

完成后，我们需要通过关闭游标和数据库连接来清理它。

```
cursor.close()
sqlConnection.close() 
```

就这样，保存文件并使用`python <filename>.py`或`python3 <filename.py>`命令，这取决于您的配置，来运行。以下是完整的脚本:

```
import pypyodbc

sqlConnection = pypyodbc.connect(
                "Driver={ODBC Driver 13 for SQL Server};"
        "Server=<server IP address>;"
        "Database=<database>;"
        "uid=<username>;pwd=<password>");

cursor = sqlConnection.cursor()
cursor.execute("select * from Values")

for row in cursor:
    print(cursor)
    # first column
    firstColumn = row[0]
    # ...

cursor.close()
sqlConnection.close() 
```

`with`语法也可以用来自动关闭光标和连接，这是编写相同脚本的另一种方式:

```
import pypyodbc

with pypyodbc.connect(
        "Driver={ODBC Driver 13 for SQL Server};"
        "Server=<server IP address>;"
        "Database=<database>;"
        "uid=<username>;pwd=<password>") as sqlConnection:

    with sqlConnection.cursor() as cursor:

        cursor.execute("select * from Values")

        for row in cursor:
            print(cursor)
            # first column
            firstColumn = row[0]
            # ... 
```

如果你正在寻找更多关于这个主题的阅读材料:

*   [Python 如何:连接并管理数据库](https://medium.com/@DrGabrielA81/python-how-connect-to-and-manage-a-database-68b113a5ca62)