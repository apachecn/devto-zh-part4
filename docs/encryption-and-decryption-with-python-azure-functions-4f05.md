# 使用 Python Azure 函数进行加密和解密

> 原文：<https://dev.to/teckert/encryption-and-decryption-with-python-azure-functions-4f05>

> 受 [David Kopec](https://twitter.com/davekopec) 的[Python 中的经典计算机科学问题](https://www.manning.com/books/classic-computer-science-problems-in-python)1.3 节的启发

[GitHub 上这篇文章的源代码](https://github.com/t-eckert/sample-python-azure-functions/tree/master/encryption_and_decryption)。

让我们构建两个 Azure 函数，都是 HTTP 触发器。一个用来加密信息，另一个用来解密。公平的警告，如果你正在寻找一个安全的加密解决方案，你会在比我聪明的人手中得到更好的服务，但我认为在解决这个问题的过程中有一些有用的东西可以学习。

如上所述，这些例子中使用的逻辑来自 David Kopec 的一本书。我昨晚一直在研究它，想把这一节讲得更深入一点。我将 1.3 节“牢不可破的加密”中的工作重新实现为两个 Azure 函数。我将带您了解整个过程，这样您也可以了解！

## 收集你的材料

开始之前，您需要以下材料:

*   [Python 3.6](https://www.python.org/downloads/)
*   [Azure 功能核心工具](https://docs.microsoft.com/bs-latn-ba/azure/azure-functions/functions-run-local#v2)

## 创建虚拟环境

Python Azure Functions 应用程序要求您在虚拟环境中工作。如果你不熟悉使用`venv`，在 [RealPython](https://realpython.com/python-virtual-environments-a-primer/) 有一本很棒的入门书。

Bash (MacOS 或 Linux)

```
python3.6 -m venv .env  
source .env/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

PowerShell 或命令提示符(Windows)

```
py  -3.6  -m  venv  .env  .\.env\Scripts\activate 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化你的 Azure 功能 App

执行以下命令创建一个新的函数 App:

```
func init encryption_and_decryption 
```

Enter fullscreen mode Exit fullscreen mode

现在有一个名为`encryption_and_decryption`的文件夹，里面有 Python Azure Function 应用程序的样板代码。如果你愿意，花些时间在你最喜欢的编辑器中打开代码，四处看看。

> 如果要找编辑，我推荐 [VS 代码](https://code.visualstudio.com/)。

## 创建`encrypt`功能

确保你在`encryption_and_decryption`目录下:

```
cd encryption_and_decryption 
```

Enter fullscreen mode Exit fullscreen mode

使用
创建新函数

```
func new 
```

Enter fullscreen mode Exit fullscreen mode

出现提示时，选择`HTTP trigger`。然后命名函数`encrypt`。

## 加密输入

打开文件`encrypt/__init__.py`。您应该看到 HTTP 触发器函数的样板代码。这个函数将把`name`作为一个参数，并返回“你好`name`！”。让我们在这个文件的底部添加代码，使用一组随机的令牌字节来加密`name`。

```
from secrets import token_bytes

def random_key(length: int) -> int:
    # returns length random bytes
    key: bytes = token_bytes(length)
    # convert key to bit string
    return int.from_bytes(key, "big") 
```

Enter fullscreen mode Exit fullscreen mode

接下来，编写一个函数来使用这个随机密钥加密`name` :

```
from typing import Tuple

def encrypt(name: str) -> Tuple[int, int]:
    # returns (encrypted_name, dummy_key), encrypted by one-time pad
    # encode the name to bytes, then to a bit string
    name_as_bytes: bytes = name.encode()
    name_key: int = int.from_bytes(name_as_bytes, "big")

    # generate a dummy key for encryption
    dummy_key: int = random_key(len(name_as_bytes))

    # encrypt by XOR operation
    encrypted_name: int = name_key ^ dummy_key

    return encrypted_name, dummy_key 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您已经将名称编码为一个数字。这只是写入`name`数据的另一种方式。数据可以从这个表格转换回来。“托马斯”，尽管这个变换是“T1”。你可以在 Python REPL 中看到这个:

```
>>> name: str = "Thomas"
>>> name_as_bytes: bytes = name.encode()
>>> name_key = int.from_bytes(name_as_bytes, "big")
>>> print(f"name: {name}\nname_as_bytes: {name_as_bytes}\nname_key: {name_key}")
name: Thomas
name_as_bytes: b'Thomas'
name_key: 92807522771315 
```

Enter fullscreen mode Exit fullscreen mode

我可以把它转换回来:

```
>>> name_again = name_key.to_bytes((name_key.bit_length() + 7) // 8, "big").decode()
>>> print(name_again)
Thomas 
```

Enter fullscreen mode Exit fullscreen mode

您获取名称的`int`表示，称为`name_key`，并使用 random_key `int`执行异或(XOR)运算。在 Python 中，XOR 是使用`^`字符完成的。这个操作产生一个加密形式的输入名:

```
name_key ^ random_key = encrypted_name 
```

Enter fullscreen mode Exit fullscreen mode

使用`random_key`再次执行 XOR 运算将撤销加密:

```
encrypted_name ^ random_key = name_key 
```

Enter fullscreen mode Exit fullscreen mode

从某种意义上说，`random_key`锁定和解锁`name`。

## 使用`encrypt`

现在您已经编写了`encrypt`函数，编辑样板代码以获取`name`输入，加密它并返回带有密钥的加密名称。总之，`__init__.py`中的代码应该是这样的:

```
import logging
from secrets import token_bytes
from typing import Tuple

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info("func: encrypt: request received")

    name = req.params.get("name")
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get("name")

    if name:
        # encrypt name
        encrypted_name, dummy_key = encrypt(name)

        response = str({"encrypted_name": encrypted_name, "dummy_key": dummy_key})
        return func.HttpResponse(response)
    else:
        # ask for input if none provided
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400,
        )

def random_key(length: int) -> int:
    # returns length random bytes
    key: bytes = token_bytes(length)
    # convert key to bit string
    return int.from_bytes(key, "big")

def encrypt(name: str) -> Tuple[int, int]:
    # return (encrypted_name, dummy_key); encrypted by one-time pad
    # encode the name to bytes, then to a bit string
    name_as_bytes: bytes = name.encode()
    name_key: int = int.from_bytes(name_as_bytes, "big")

    # generate a dummy key for encryption
    dummy_key: int = random_key(len(name_as_bytes))

    # encrypt by XOR operation
    encrypted_name: int = name_key ^ dummy_key

    return encrypted_name, dummy_key 
```

Enter fullscreen mode Exit fullscreen mode

## 测试`encrypt`

确保您在`encryption_and_decryption`目录中，并使用命令
运行函数应用程序

```
func host start 
```

Enter fullscreen mode Exit fullscreen mode

这个命令的输出包括一个到这个函数的 HTTP 端点的链接:`http://localhost:7071/api/encrypt`。

在浏览器中访问这个 URL 将调用 Azure 函数，您将得到响应“请在查询字符串或请求正文中传递一个名称”。通过将`?name=Sophia`附加到 URL 来给函数传递一个`name`参数，这样它就变成:

```
http://localhost:7071/api/encrypt?name=Sophia 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个类似 JSON 的响应，带有加密的名称和伪密钥:

```
{  "encrypted_name":  191667540651033,  "dummy_key":  278440108655992  } 
```

Enter fullscreen mode Exit fullscreen mode

用不同的名字试试吧！

## 创建`decrypt`功能

正如您对`encrypt`函数所做的那样，从命令行执行:

```
func new 
```

Enter fullscreen mode Exit fullscreen mode

出现提示时，选择`HTTP trigger`。然后命名函数`decrypt`。您将把`encrypted_name`和`dummy_key`都传递给这个 Azure 函数。它将执行 XOR 运算来撤消加密。

## 解密输入

打开文件`decrypt/__init__.py`。在这个文件的底部，添加一个函数来解密

```
def decrypt(encrypted_name: int, dummy_key: int) -> str:
    # returns XOR of encrypted_name and dummy_key converted to bytes and decoded
    name_key: int = encrypted_name ^ dummy_key
    return name_key.to_bytes((name_key.bit_length() + 7) // 8, "big").decode() 
```

Enter fullscreen mode Exit fullscreen mode

如上图所示，对`encrypted_name`和`dummy_key`执行异或运算将输出与`name_key`相同的值。然后，该功能撤销从`int`到`str`的编码。

> 注意:
> 在 Python 3+中，`//`运算符将分子除以分母，并将商作为`int`返回，去掉小数点后的数字。
> 
> 例如`5 // 2 == 2`

## 使用`decrypt`

编辑样板代码以接受两个参数`encrypted_name`和`dummy_key`。它应该是这样的:

```
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info("func: decrypt: request received")

    encrypted_name = req.params.get("encrypted_name")
    dummy_key = req.params.get("dummy_key")
    if not encrypted_name or not dummy_key:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            encrypted_name = req_body.get("encrypted_name")
            dummy_key = req_body.get("dummy_key")

    if encrypted_name and dummy_key:
        name = decrypt(int(encrypted_name), int(dummy_key))

        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400,
        )

def decrypt(encrypted_name: int, dummy_key: int) -> str:
    # returns XOR of encrypted_name and dummy_key converted to bytes and decoded
    name_key: int = encrypted_name ^ dummy_key
    return name_key.to_bytes((name_key.bit_length() + 7) // 8, "big").decode() 
```

Enter fullscreen mode Exit fullscreen mode

## 测试`decrypt`

用
启动 Azure 功能 App

```
func host start 
```

Enter fullscreen mode Exit fullscreen mode

使用来自`encrypt` Azure 函数的输出，通过访问
来测试`decrypt`函数

```
http://localhost:7071/api/decrypt?encrypted_name=133673440856088&dummy_key=47262719615353 
```

Enter fullscreen mode Exit fullscreen mode

这应该返回“你好，索菲亚！”。

通过向`encrypt`端点发送一个名称，并用 Azure 函数返回的值替换`encrypted_name`和`dummy_key`的值，尝试使用您自己的加密值。

## 更进一步

Python Azure 函数:

*   博文:[仔细看看 Python 对 Azure 函数的支持](https://azure.microsoft.com/en-us/blog/taking-a-closer-look-at-python-support-for-azure-functions/)
*   GitHub Repo:[Azure Functions Python Worker](https://github.com/Azure/azure-functions-python-worker)

加密和解密代码经作者许可来自[大卫·科佩克](https://twitter.com/davekopec)的[经典计算机科学问题 Python](https://www.manning.com/books/classic-computer-science-problems-in-python) 。