# 使用 Apache NiFi 和 MiNiFi 在边缘更新机器学习模型

> 原文：<https://dev.to/tspannhw/updating-machine-learning-models-at-the-edge-with-apache-nifi-and-minifi-3ama>

#### 用 Apache NiFi 和 MiNiFi 在边缘更新机器学习模型

是的，我们通过 HTTPS 的站点到站点(S2S)与 Apache NiFi 的 MiNiFi 代理进行双向通信。这意味着我可以向代理推送任何我想要的内容，包括命令、文件和更新。

我还可以通过 MQTT、REST 和 Kafka 等选项向 edge 代理传输数据。

[![](img/a7f0da75350355750728f7dcec84543c.png)](https://1.bp.blogspot.com/-BWOud_4dNQI/XWAuIxFJ09I/AAAAAAAAYH0/4JeMBAGVbBAIsPCdKK8UhvF7sff7UnWRwCLcBGAs/s1600/cemMiNiFiJavaBiDirectional.png)

NiFi 准备好发送和接收来自其他 NiFi 节点、集群和 MiNiFi 代理的消息

[![](img/c31c63ce21da417d7ebceb4f152b868a.png)](https://1.bp.blogspot.com/-1ldX2dghWEE/XWAuJ_Gx9oI/AAAAAAAAYIA/iH8ANjS-BgwNnWNCT1EgVnybZGNM5ye3ACLcBGAs/s1600/nifibidirectional.png)

我们的 NiFi 流正在消费 Kafka 和 MQTT 消息，以及读取更新的模型文件和生成集成测试传感器数据。

[![](img/f8c6c81db3b44422730f1a0cfa0fb3f8.png)](https://1.bp.blogspot.com/-2A1NNMQ9DTI/XWAuIy9DGGI/AAAAAAAAYH4/WAGcczYdbn0O74PT9ZvJtBQEm_tSGPtqgCLcBGAs/s1600/bidirectionalnififlow.png)

MiNiFi 代理已经下载了模型和我们发送给它的任何东西

[![](img/972754b0be52427915e60b0508865d32.png)](https://1.bp.blogspot.com/-UkAtwfCV7TE/XWAuJilWiTI/AAAAAAAAYIE/5nOAoe4bHNoX1Dbzkf14H-t6cvq1WP2PACLcBGAs/s1600/filescem2.png)

[![](img/58f0acf0b92a16992155d06a57c5317b.png)](https://1.bp.blogspot.com/-zmt1YJ2CeAE/XWAuJzmWQfI/AAAAAAAAYII/EDqlGUl8a-cEEHjjrZAys140ZyEH2KlQwCLcBGAs/s1600/fileswrittencem.png)

在 CEM 很容易配置 MQTT 消息消费，我们只需要代理(带有端口)和一个主题来过滤。

[![](img/7a358873cbe007bef6cad4e3a8aed455.png)](https://1.bp.blogspot.com/-afbEldLSS5g/XWA3oNGUMlI/AAAAAAAAYI4/Ou-LqLcgdJoUhcWiatcbK32dfBU1K3vSgCLcBGAs/s1600/cemConsumeMQTT1.png)

[![](img/3d4b4e5b0b4e42cdeb2695f2b9c62b69.png)](https://1.bp.blogspot.com/-5IO0Ek8OIFQ/XWA3oJKIQpI/AAAAAAAAYIw/nGLjZROoiO8vv5jcX3diQdA53KBZha0GQCLcBGAs/s1600/cemConsumeMQTT.png)

为了监听文件/模型，您可以很容易地添加一个 REST 端点来代理您选择的数据，可以使用也可以不使用 SSL

[![](img/4c0cc2191802e757bc636f25a8d6bbc2.png)](https://1.bp.blogspot.com/-7ahlPwQ12LA/XWA3ooki9KI/AAAAAAAAYI0/yipYgzksCPIxzrCtUrJiKJ3FSQFO8UROQCLcBGAs/s1600/cemListenHTTP.png)

下面是一个测试 REST API 的示例 CURL 脚本:

p.p1 {边距:0.0 px 0.0 px 0.0 px 0.0 px 0.0 px；字体:11.0px Menlocolor:# 000000 } span . S1 { font-variant-ligations:no-common-ligations }

curl -d '{"key1":"value1 "，" key 2 ":" value 2 " } '-H " Content-Type:application/JSON "-X POST[http://ec2-3-85-54-189.compute-1.amazonaws.com:8899/upload](http://ec2-3-85-54-189.compute-1.amazonaws.com:8899/upload)

我们可以使用 GenerateFlowFile 轻松生成用于集成测试的 JSON IoT 风格数据:

[![](img/a730f9eab116afa80bd99c763680a4cf.png)](https://1.bp.blogspot.com/-K7dhGJXvQKo/XWA3nvbLTmI/AAAAAAAAYIo/x3hG1Hu6-zM5C8rqFXpKmYVZ0c8Zbf7AACLcBGAs/s1600/GenerateFlowFileJSON.png)

当我的数据科学服务器发生变化时，让我们获取更新的模型:

[![](img/ec0d6558227ae476d3451e5573131aeb.png)](https://1.bp.blogspot.com/-ZSRvh3dKbpY/XWA3nv3qtsI/AAAAAAAAYIk/NiIFildtHhcbCGn_UqOoOejInDlUmePQQCLcBGAs/s1600/GrabModelsNiFi.png)

我可以阅读卡夫卡的信息，也可以把它们发送给迷你特工。

[![](img/93dd23cb70dfb89add97f8f3b531b1ca.png)](https://1.bp.blogspot.com/-svmRMndbPM4/XWA3pTZiabI/AAAAAAAAYI8/5H9tvBZ5hXUYRO-0-bze6gFaajSLyVxoACLcBGAs/s1600/publishKafkaRec2.png)

所以我推了一个 TFLITE 的型号，但是 ONNX，PMML，Docker 或者 Pickle 都是选项。