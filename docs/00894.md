# 姿势识别注册/登录 tensorflow.js #machinelearning

> 原文：<https://dev.to/kauresss/pose-recognition-signup-login-with-tensorflow-js-machinelearning-3a12>

### 这是按顺序来的下一个项目:

1.  [简单认证:](//https:simplyauth.herokuapp.com)本地即插即用开发人员认证 API
2.  [简单认证社交:](//https:simplyauthsocial.herokuapp.com)社交即插即用开发人员认证 API
3.  美少女战士姿势识别注册/登录(即将推出..)

### app 最基本的功能将是:

1.  用 3 个姿势的序列登记
2.  确认身体姿势
3.  使用 pose 密码登录

我决定用 pose 而不是手势，因为我也在 Unity3d 中引导一些虚拟现实的东西，所以认为让人们用 pose 密码登录虚拟现实应用程序会很酷。

### 正在使用的技术:

1.  前端:Twitter Bootstrap4 & JavaScript
2.  中间件:Express.js
3.  数据库:MongoDB 4。姿态估计和识别:使用 PoseNet 实现 Tensorflow.js

[这里有更多关于姿态估计的信息](https://medium.com/tensorflow/real-time-human-pose-estimation-in-the-browser-with-tensorflow-js-7dd0bc881cd5)