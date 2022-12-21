# 2019 年思科路由器上的 Python 自动化- NETCONF，YANG & Jinja2

> 原文：<https://dev.to/alecbuda/python-automation-on-cisco-routers-in-2019-netconf-yang-jinja2-52ho>

在阅读这篇文章之前，我强烈建议你看看我们的第一篇 NETCONF 文章,如果你是这个主题的新手的话。在这篇文章中，我解释了 NETCONF 和 YANG 模型的基础知识，这是进入 Python 自动化之前的重要背景知识。

在今天的练习中，我们将编写一个 Python 程序来配置 Cisco CSR 1000v 路由器上的接口。我们的解决方案将为配置数据模型使用 Jinja 模板，并使用 NETCONF 协议将我们呈现的配置有效负载推送到路由器——我希望这篇文章将阐明如何通过将配置模板与 python 代码库分离，以可扩展的方式实现这项技术。

首先，让我们启动一个新的 Cisco CSR 1000v 路由器。然后我们需要配置凭证并启用 NETCONF。

```
conf t
!
username admin privilege 15 secret admin
!
netconf-yang 
```

我们还将在路由器上配置一个接口，供 NETCONF 客户机连接。

```
interface GigabitEthernet1
 ip address 192.168.159.10 255.255.255.0
 no shut
 exit
!
end 
```

接下来，[在您的工作站上安装 Python](https://www.python.org/downloads/) ，如果您还没有安装的话。

完成后，让我们打开一个终端并安装“ncclient”包。这个包将为我们提供一个 NETCONF 客户端，我们将使用它来管理我们与路由器的会话。

```
pip install ncclient 
```

我们现在将为我们的 python 程序创建一个新文件——我们将其命名为“cisco-automation-tutorial.py”。在文件的开头，导入我们的程序将使用的依赖项。

```
# Filename:                     cisco-automation-tutorial.py
# Command to run the program:   python cisco-automation-tutorial.py 
# Import the required dependencies from ncclient import manager
from jinja2 import Template 
```

接下来，我们将使用“connect”方法建立到路由器的 NETCONF 会话。

```
# Establish our NETCONF Session m = manager.connect(host='192.168.159.10', port=830, username='admin',
                    password='admin', device_params={'name': 'csr'}) 
```

为了用 NETCONF 配置我们的设备，我们首先必须了解如何在 RPC 有效负载中构造配置数据。NETCONF 中的数据结构由 [YANG](https://tools.ietf.org/html/rfc6020) 模型定义，CSR 路由器实际上支持许多不同的选项供选择。例如， [OpenConfig 型号](https://github.com/openconfig)和 [IETF 型号](https://tools.ietf.org/html/rfc8343)都兼容 IOS-XE 软件家族。

在今天的练习中，我们将使用 [IOS-XE 原生杨模型](https://github.com/YangModels/yang/tree/master/vendor/cisco/xe)。

要快速体验本机模型，请将下面的 python 代码添加到您的程序中。

```
# Create a configuration filter interface_filter = '''
  <filter>
      <native >
          <interface>
            <GigabitEthernet>
              <name>1</name>
            </GigabitEthernet>
          </interface>
      </native>
  </filter>
'''

# Execute the get-config RPC result = m.get_config('running', interface_filter)
print(result) 
```

这段代码本质上与 CLI 命令`"show running-config interface GigabitEthernet 1"`相同。当我们使用`python cisco-automation-tutorial.py`执行程序时，我们得到下面的输出。

```
<?xml version="1.0" encoding="UTF-8"?>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:f4241f31-5098-475c-9d01-bcf34df25643" 
  xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0">
  <data>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
      <interface>
        <GigabitEthernet>
          <name xmlns:nc='urn:ietf:params:xml:ns:netconf:base:1.0'>1</name>
          <ip>
            <address>
              <primary>
                <address>192.168.159.10</address>
                <mask>255.255.255.0</mask>
              </primary>
            </address>
          </ip>
          <mop>
            <enabled>false</enabled>
            <sysid>false</sysid>
          </mop>
          <negotiation xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-ethernet">
            <auto>true</auto>
          </negotiation>
        </GigabitEthernet>
      </interface>
    </native>
  </data>
</rpc-reply> 
```

从 RPC 回复中可以看到，我们现在已经揭示了一个接口的 IOS-XE 数据结构。

我们现在可以继续使用这个模型作为配置其他接口的模板。从 RPC 回复中，将`config`标记中的所有内容复制并粘贴到一个名为“interface.xml”的新文件中。这将成为我们的[金贾模板](https://jinja.palletsprojects.com/en/2.10.x/)。接下来，使用双花括号语法将 IP 地址、子网掩码和接口索引替换为 Jinja 变量。一旦完成，你应该得到一个类似下面的文件。确保文件与 python 程序放在同一个目录中。

```
<config>
  <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
    <interface>
      <GigabitEthernet>
        <name xmlns:nc='urn:ietf:params:xml:ns:netconf:base:1.0'>{{ INTERFACE_INDEX }}</name>
        <ip>
          <address>
            <primary>
              <address>{{ IP_ADDRESS }}</address>
              <mask>{{ SUBNET_MASK }}</mask>
            </primary>
          </address>
        </ip>
        <mop>
          <enabled>false</enabled>
          <sysid>false</sysid>
        </mop>
        <negotiation xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-ethernet">
          <auto>true</auto>
        </negotiation>
      </GigabitEthernet>
    </interface>
  </native>
</config> 
```

回到我们的 python 程序，我们现在可以用两个步骤配置路由器上的任何接口。

1.  **模板渲染** -用想要的变量值渲染 Jinja 模板。
2.  **NETCONF 事务**——发送渲染对象作为`edit-config` RPC 的有效负载。

要在我们的 python 程序中做到这一点，请添加下面的代码。在我们的示例中，我们将接口 GigabitEthernet 2 配置为地址 10.0.0.1/30。

```
# Render our Jinja template interface_template = Template(open('interface.xml').read())
interface_rendered = interface_template.render(
  INTERFACE_INDEX='2', 
  IP_ADDRESS='10.0.0.1', 
  SUBNET_MASK='255.255.255.252'
)

# Execute the edit-config RPC result = m.edit_config(target='running', config=interface_rendered)
print(result) 
```

程序执行后，您应该看到来自路由器的“ok”RPC 回复，表明事务成功完成。

```
<?xml version="1.0" encoding="UTF-8"?>
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:2db17593-b51d-4ab2-be28-a268441d6af1" 
  xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0">
  <ok/>
</rpc-reply> 
```

最后，使用熟悉的 CLI 命令检验配置是否确实存在于路由器上。

```
Router#show running-config interface GigabitEthernet 2
Building configuration...

Current configuration : 129 bytes
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.252
 shutdown
 negotiation auto
 no mop enabled
 no mop sysid
end 
```

虽然我们今天展示的例子很琐碎，但我希望它能启发你思考如何扩展这项技术的可能性。例如，您可以用原子 Jinja 模板构建一个 API 后端来配置网络上的任何东西。

然后可以构建多个前端应用程序来使用该配置服务——这些前端工具可以是网络编排器、变更管理软件、订单激活工具、策略管理器等等。这项技术确实能够实现网络行业公司的数字化转型。

最后，如果你没有听说过 [Ultra Config Generator](https://dev.to/ultra-config-generator/) ，我强烈建议你去看看。它本质上是我们刚刚讨论的技术的开箱即用解决方案，允许公司快速数字化其配置流程。我们设计的产品允许网络工程师以高度灵活、高效和优雅的方式生成网络配置。我们的客户喜欢这个应用程序，我希望您也会喜欢。

非常感谢您的阅读，如果您喜欢其中的内容，请随意关注。

下次再见吧！

亚历克（男子名）