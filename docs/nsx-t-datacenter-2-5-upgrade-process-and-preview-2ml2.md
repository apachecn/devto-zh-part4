# NSX-T 数据中心 2.5 升级流程和预览

> 原文：<https://dev.to/ngschmidt/nsx-t-datacenter-2-5-upgrade-process-and-preview-2ml2>

现在 NSX-T 数据中心 2.5 已经可以下载了，是时候在我的家庭实验室里尝试一下了。

首先，如果您登录时间超过 90 天，您将被完全锁定在设备之外。如果您以正常的 linux 方式( *passwd* 和 **chage** )进行任何更改，设备将在大约一分钟内自动恢复。由于这是一个家庭实验室，VMWare 增加了设置更高最大年龄的功能[在这里](https://kb.vmware.com/s/article/70691)。在生产中，使用活动目录或另一个 LDAP 源来防止自己丢失 NSX-T

下载升级包花了相当长的时间——似乎 VMWare 在主机容量方面遇到了问题。我猜这将是一个受欢迎的版本！

我们以通常的方式开始，上传升级包。mub 文件):

[![](img/118550e9e15ac08fc4a421f9a49a12d2.png)](https://1.bp.blogspot.com/-HcdHys2lULc/XYZAA2KMMqI/AAAAAAAAAqI/DDERJSdIClkWtcI-79XRdJTBpePBflwpwCLcBGAsYHQ/s1600/19sept-1.PNG) 
然后我们点击升级按钮，它会在整个过程中一步一步地提示你。但是没有进度条！

[![](img/8236825a57d038febbfb8821dd3e5997.png)](https://1.bp.blogspot.com/-qvWYL0uEu5Q/XYZAi5a7s3I/AAAAAAAAAqQ/V3Or-T3suDUOcDSauasxdLAmtJq9edtXwCLcBGAsYHQ/s1600/19sept-2.PNG) 
一旦设置了升级协调器，就该运行预检查了。这个警告您消息传递端口发生了变化。

[![](img/312a896fcf9b701e63ab18453961280a.png)](https://1.bp.blogspot.com/-VO2pzcUi05I/XYZFCklKZoI/AAAAAAAAAqc/dHPpcrDT3RUXz8vscJ3XIo8HGv8aiNYdQCLcBGAsYHQ/s1600/19sept-3.PNG)

[![](img/e56c881247c6064aeac8b2c2a2eedfdd.png)](https://1.bp.blogspot.com/-8JaW57BVGwI/XYZFUAHDRnI/AAAAAAAAAqk/eFcmW40-CrQPK3uxW9thilOapvpvlg2iACLcBGAsYHQ/s1600/19sept-4.PNG) 
是时候开始升级东东了！

[![](img/86dd5caf13b3143c1f8082b210d751b6.png)](https://1.bp.blogspot.com/-Hdf70vyol54/XYZFms1JFII/AAAAAAAAAqs/LX8YLAjv6aM6E2e6kfLzI27aCeKsPOsGACLcBGAsYHQ/s1600/19sept-5.PNG)

[![](img/08bf90d5045493a1fd079f60398f41dc.png)](https://1.bp.blogspot.com/-uuwVvhVT-qI/XYZJ8yvLcEI/AAAAAAAAArE/q5ZFjbXeKE42RFBU0ENI7qR1FLmdx9yVACLcBGAsYHQ/s1600/19sept-6.PNG) 
与其他 2.4 版本一样，您不必使用维护模式(如果您这样做，可能会停机)。

[![](img/c7acb5661c92df5a51f7aee06027ddc9.png)](https://1.bp.blogspot.com/-C4gX60-YSRY/XYZOKZyHsnI/AAAAAAAAArc/eNn53RlKvk0H6eowdbXBb9yPSnQTNnEAQCLcBGAsYHQ/s1600/19sept-7.PNG) 
除非您推出集群，否则管理节点将无法访问。如果您看到“设备不正常”或“错误状态 101”消息，这仅仅意味着设备尚未准备好。

升级后，我们可以看到承诺的 BGP 状态:

[![](img/80bd73762bfb37bf3d830831f352c01f.png)](https://1.bp.blogspot.com/-gaJLMnBZpxw/XYZUd-juuEI/AAAAAAAAAro/qek4afRpoA8On5t9bOMZknrCh5vBnvBVACLcBGAsYHQ/s1600/19sept-8.PNG)

[![](img/bfe811e0b24f8fb914e690f0f7e816c9.png)](https://1.bp.blogspot.com/-Qn98R8ckZ5U/XYZWhljzUlI/AAAAAAAAAr0/wg4TUkqWWhYUvEcodjR_sEf4eucikN3WQCLcBGAsYHQ/s1600/19sept-9.PNG)

容量管理就在那里:

[![](img/1e641929ea2510d09659c982cf507522.png)](https://1.bp.blogspot.com/-UsNYl3r1Uqo/XYZW2YRrb9I/AAAAAAAAAr8/h_p1xT4h8XQq_kckOudD6HCDxwZupi-BQCLcBGAsYHQ/s1600/19sept-10.PNG) 
遗憾的是，我在 GUI 中看到了 BGP 状态，但没有路由表。文档可能会对事情有所指导，但我看不到它的全部功能。