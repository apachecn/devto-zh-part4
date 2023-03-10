# 十大常用 HTTP 状态代码

> 原文：<https://dev.to/mukunzidd/10-most-used-http-status-codes-2ck5>

HTTP 响应状态代码指示特定的 HTTP 请求是否已成功完成。响应分为五类:信息响应、成功响应、重定向、客户端错误和服务器错误。

以下是 10 个常用且有用的 HTTP 状态代码:

## 1xx:信息性

*T2`None in this class.`*

## 2xx:成功

#### 200 OK

这是一般状态代码。用来表示成功的最常见代码。
请求已成功。成功的含义根据 HTTP 方法的不同而有所不同:
**GET:** 资源已被获取并在消息体中传输。
T5】HEAD:实体头在消息体中。
**PUT 或 POST:** 描述动作结果的资源在消息体中传输。
**跟踪:**消息体包含服务器收到的请求消息

#### 201 创建

成功创建(通过 POST 或 PUT)。将 Location 头设置为包含指向新创建的资源的链接(在 POST 上)。响应正文内容可能存在，也可能不存在。

#### 204 无内容

未使用包装响应且正文中没有内容时的状态(例如，删除)。

## 3xx:重定向

#### 304 未修改

这用于缓存目的。它告诉客户端响应没有被修改，因此客户端可以继续使用响应的相同缓存版本。

## 4xx:客户端错误

#### 400 坏请求

完成请求时的一般错误会导致无效状态。域验证错误、丢失数据等。举几个例子。

#### 401 未经授权

身份验证令牌丢失或无效的错误代码响应。

#### 403 禁止

用户未被授权执行操作或资源由于某种原因(如时间限制等)不可用的错误代码。).

#### 404 未找到

当找不到请求的资源时使用，无论它是否不存在，或者如果出于安全原因，服务希望屏蔽 401 或 403。

#### 409 冲突

每当满足请求会导致资源冲突时。重复条目和在不支持级联删除时删除根对象就是两个例子。

## 5xx:服务器错误

#### 500 内部服务器错误

当服务器端抛出异常时的一般错误。