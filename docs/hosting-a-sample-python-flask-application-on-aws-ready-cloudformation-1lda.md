# 在具有自动缩放和负载平衡器的 AWS EC2 上托管一个示例 Python Flask 应用程序(Ready CloudFormation)

> 原文：<https://dev.to/akshaykrjain/hosting-a-sample-python-flask-application-on-aws-ready-cloudformation-1lda>

1.  打开 AWS 控制台(或 aws cli)

2.  创建新堆栈

3.  在云层下使用

[https://github . com/akshaykrjain/coffee _ with _ code/blob/master/cloud formation/AWS _ CFT . YAML](https://github.com/akshaykrjain/coffee_with_code/blob/master/cloudformation/aws_cft.yaml)

注意- -将用户数据部分更改为您的自定义代码。这个云形成模板中已经添加了一个示例代码。确保你至少有一个公共子网和一个私有子网准备好。
-专用子网必须能够连接到互联网(为此使用互联网网关或 NAT)