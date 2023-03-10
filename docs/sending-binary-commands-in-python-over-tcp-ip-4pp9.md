# 通过 TCP/IP 用 Python 发送二进制命令

> 原文：<https://dev.to/teaglebuilt/sending-binary-commands-in-python-over-tcp-ip-4pp9>

我最近开始与一家公司合作，该公司的商业模式是围绕 IOT 设备展开的。我最近接到一个任务，编写一个 python 程序，将命令从服务器发送到设备。

要发送命令，我们首先必须了解并选择要使用的协议。我将使用 TCP 协议发送这个命令，并构建一个经过编码并适合代表另一台主机发送的数据包。

确保您已经测试了双方之间的 tcp 连接。验证您指定的端口是否打开，然后测试连接，您的 ip 表/防火墙规则可能会阻止来自外部主机的 tcp 连接。为此，请运行 Ncat 命令。

```
nc -vv address port 
```

Enter fullscreen mode Exit fullscreen mode

您也可以运行这个命令来监听端口上的传入连接

```
nc -vlp 9100 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该会收到一条消息，说明连接已经建立。

第一步是创建一个套接字，并在双方之间建立连接。

```
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((dns, 9100)) 
```

Enter fullscreen mode Exit fullscreen mode

一旦建立了连接，就该发送命令了。要发送数据包，您必须首先对命令进行编码。最常见的用例是 command . encode(“utf-8”)

在本例中，我将字符串值存储在一个字节数组中，并在开头和结尾连接 ASCII 27 和 ASCII 13。

现在，它已准备好作为数据包发送。我将套接字设置为半关闭，直到我收到响应。

```
packet = bytearray("\x1b" + command + "\x0d")
s.send(packet)
s.shutdown(socket.SHUT_WR) 
```

Enter fullscreen mode Exit fullscreen mode

要检索响应，使用 s.recv()并在方法中记录数据包的大小。收到消息后，我们解析数据包，现在我们有了响应。

```
result = s.recv(1024)
response = result.split('\0', 1)[0].strip() 
```

Enter fullscreen mode Exit fullscreen mode

这个例子只显示了一个与 Ansible 一起使用的脚本，用于向许多设备发送命令。为了让设备接收和响应，它还需要一个 python 脚本来接受连接、接收、解析和响应。

```
import socket
import sys
def send_command(dns, command):
try:
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((dns, 9100))
except (socket.error,socket.herror,socket.gaierror) as (errno,

strErr):
    raise Exception("Failed to connect to device" + dns + ": " + 

    strErr)
try:
    packet = bytearray("\x1b" + command + "\x0d")
    s.send(packet)
    s.shutdown(socket.SHUT_WR)
    result = s.recv(1024)
    response = result.split('\0', 1)[0].strip() 
    print(response)
    return response
except (socket.error,socket.herror,socket.gaierror) as (errno,        strErr):
    raise Exception("Failed to communicate with device" +    dns   + 
    ": " + strErr)
finally:
    s.close()
def main():
    dns = sys.argv[1]
    command = sys.argv[2]
    send_command(dns, command)
if __name__ == "__main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

最后，我将运行该文件并传入所需的参数。

```
python name_of_file.py "host" "command" 
```

Enter fullscreen mode Exit fullscreen mode