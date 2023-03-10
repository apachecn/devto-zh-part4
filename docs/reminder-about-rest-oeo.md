# 关于休息的提醒

> 原文：<https://dev.to/boxseb/reminder-about-rest-oeo>

### 得到

从 API 端点检索数据的幂等方法

**200** 找到资源时返回
T3】404 找不到资源时返回
T6】400 请求形状不正确时返回

### 岗位

非等幂通过 API 资源集合端点创建新资源

应返回 **201** ，包含创建资源时返回的创建实体
200 或 **204** ，但不能被 URI 识别或在响应体中返回

### 放

用于更新/替换由资源标识符寻址的资源

**200** 或 **204** 返回以标识成功完成
201 当资源已创建时返回
**404** 当资源未找到时返回

### 删除

用于删除由资源标识符寻址的资源

**200** 当动作已经执行并且响应包含状态描述实体
**202** 当动作已经排队时返回
**204** 当动作已经执行但是响应不包含状态描述实体
**404** 当资源未找到时返回

### 补丁

用于部分修改资源

**200** 当动作已经执行并且响应包含状态描述实体
**202** 当动作已经排队时返回
**204** 当动作已经执行但是响应不包含状态描述实体
**404** 当资源未找到时返回

### 头

用于根据响应头找出如果使用 GET 方法将返回什么响应