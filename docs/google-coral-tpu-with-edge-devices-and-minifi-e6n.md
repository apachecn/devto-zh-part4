# 谷歌珊瑚 TPU 与边缘设备和迷你

> 原文：<https://dev.to/tspannhw/google-coral-tpu-with-edge-devices-and-minifi-e6n>

### 用 Edge 设备和 MiNiFi 谷歌珊瑚 TPU

使用 Cloudera Edge Flow Manager 设计我们的 Edge AI 流程。

[![](img/9e1e0a9da48cc94588e04be3dc50affd.png)](https://1.bp.blogspot.com/-JDwdp-ZfYy8/XV8CV7ctHII/AAAAAAAAYHM/ynTk71FRx6s5m7XqxPWp3iNyZVOjX3NmwCLcBGAs/s1600/cemCoralFlow.png)

配置您的远程进程组，将数据发送到您的 NiFi 集群

[![](img/023ceabdb0b5e65350eda42f79e4ccdb.png)](https://1.bp.blogspot.com/-jKiJmeoyrhk/XV8CVoEulbI/AAAAAAAAYHI/_x21-w3Sti04_ys0KYqCAp1H060wW7_VwCLcBGAs/s1600/cemCoralRPG.png)

从事件窗口监控您的代理

[![](img/f803b452b6f31b859227e342735f13af.png)](https://1.bp.blogspot.com/-o1O6bDsWpis/XV8CV9mOg-I/AAAAAAAAYHQ/JCw6i0_CaIIR5tmoM5Ts-cJ8Y3ekTuFvgCLcBGAs/s1600/cemMonitorEvents.png)

让我们抓住所有的新图像，然后删除完成

[![](img/669941a8cf4f4964a4cec6ccde6adb87.png)](https://1.bp.blogspot.com/-JrjLqlnjmGw/XV8CWmZX0NI/AAAAAAAAYHU/6wweN9Hzbpsx10GNNhWc2W3QAvBgZ11RwCLcBGAs/s1600/coralFetchFile.png)

我们有输入和输出端口，可以与 0-n MiNiFi 代理进行双向通信

[![](img/16fade904ae99d78a8d3a0496a1c3b49.png)](https://1.bp.blogspot.com/-vxnukWS2hrk/XWA_EhQ_lJI/AAAAAAAAYJY/keCP8xl3_d4pOMGFZImCi1VVDYr04EpHgCLcBGAs/s1600/coralBidirectional.png)

我们的 NiFi 流程处理来自运行 Coral TPUs 的 MiNiFi 代理的呼叫

[![](img/346ceb00864532d4e80d756d18ef6122.png)](https://1.bp.blogspot.com/-m40cBk-oR4E/XWA_E21BkoI/AAAAAAAAYJg/43vubpQE-D8gUEum7ML5HMoIyp29OGpuwCLcBGAs/s1600/coralDataProcessingOverview.png)

我们运行一个查询来检查 TensorFlow Lite 分类结果，并发出一条 slack 消息。

[![](img/f5672ab679f62fc38eb0876bae98a69f.png)](https://1.bp.blogspot.com/-s_tAD1Ex_MI/XWA_E6oNQII/AAAAAAAAYJc/KZyCbOzlqvYfE4xvLqvtrjk9iYdcJbR7QCLcBGAs/s1600/coralTensorFlowProcessing.png)

让我们将 JSON 数据推送到 AWS 中的 Kafka 集群

[![](img/1752a92618a7f97d3102585654b2629e.png)](https://1.bp.blogspot.com/-svmRMndbPM4/XWA3pTZiabI/AAAAAAAAYJQ/AqMyjcSAiIwYpUtg-N33rTSBgpbRlmaDQCEwYBhgL/s1600/publishKafkaRec2.png)