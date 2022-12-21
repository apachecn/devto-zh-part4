# 释放孤立的 OpenStack 卷

> 原文：<https://dev.to/garettmd/liberate-orphaned-openstack-volumes-355i>

如果你删除了一个附加了卷的服务器，然后在服务器还没有删除完之前就快速尝试删除该卷(至少，我认为问题就源于此；即使您等待它完全删除，也可能会发生这种情况)，卷可能会被卡住，表示它仍处于连接状态。你不能运行`openstack server remove volume <server id> < volume id>`，因为它会报错服务器不存在。我想出的解决这个问题的唯一方法是使用`cinder`工具删除附件 ID

```
openstack volume show <volume_id>
# Grab the attachment ID for use in next step

cinder --os-volume-api-version 3.50 attachment-delete <attachment_id>
# For some reason you have to specify at least api version 3.50\. Why it can't figure this out on it's own is beyond me 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助一些人。