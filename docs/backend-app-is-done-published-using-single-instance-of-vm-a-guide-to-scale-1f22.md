# 后端应用程序已完成，使用虚拟机的单个实例发布，扩展指南？

> 原文：<https://dev.to/harryawk/backend-app-is-done-published-using-single-instance-of-vm-a-guide-to-scale-1f22>

事情是这样的。有一个包含 REST API 服务的后端应用程序。它已经部署在虚拟机实例中。

# 水平缩放

为了让后端 app 服务更多的连接，**负载均衡**(实例化新实例并配置 NGiNX) **是唯一可能的吗**？

# 垂直缩放

支持后端应用服务更多连接的替代方法，**升级实例规范是唯一可行的方法吗**？

# 其他缩放比例

除了水平或垂直扩展之外，是否还有其他方式可以让这个后端应用服务于更多连接？

类似于通过**在代码**中添加或更改某些东西来重构代码以提高效率？

### P.S

我欣赏任何关于这个话题的想法。