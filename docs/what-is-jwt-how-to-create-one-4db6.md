# 什么是 JWT？如何创建一个？

> 原文：<https://dev.to/roybs2/what-is-jwt-how-to-create-one-4db6>

JSON Web Token (JWT)是一个开放标准(RFC 7519 ),它定义了一种紧凑且独立的方式，以 JSON 对象的形式在各方之间安全地传输信息。该信息可以被验证和信任，因为它是数字签名的。jwt 可以使用秘密(使用 HMAC 算法)或使用 RSA 或 ECDSA 的公钥/私钥对进行签名。

***JSON Web Token 结构是什么？*T3】**

在其紧凑的形式中，JSON Web 令牌由三部分组成，用点(.)，分别是:
报头
有效载荷
签名

因此，JWT 通常如下所示。xxxxx.yyyyy.zzzzz

让我们来分解不同的部分。

报头
报头通常由两部分组成:令牌的类型，即 JWT，以及使用的签名算法，如 HMAC SHA256 或 RSA。

例如:

{
"alg": "HS256 "，
"typ": "JWT"
}
那么，这个 JSON 就是 Base64Url 编码形成 JWT 的第一部分。

令牌的第二部分是有效负载，它包含声明。声明是关于实体(通常是用户)和附加数据的声明。有三种类型的债权:注册债权、公共债权和私人债权。

注册声明:这些是一组预定义的声明，它们不是强制性的，而是推荐性的，以提供一组有用的、可互操作的声明。其中一些是:iss(发行者)、exp(到期时间)、sub(主题)、aud(受众)和其他。

请注意，索赔名称只有三个字符长，因为 JWT 是为了简洁。

公共声明:这些可以由使用 jwt 的人随意定义。但是为了避免冲突，应该在 IANA JSON Web 令牌注册中心中定义它们，或者将其定义为包含抗冲突名称空间的 URI。

私有声明:这些自定义声明是为了在同意使用它们的各方之间共享信息而创建的，既不是注册声明，也不是公共声明。

有效载荷的例子可以是:

{
"sub": "1234567890 "，
"name": "John Doe "，
"admin": true
}
然后对有效载荷进行 Base64Url 编码，形成 JSON Web 令牌的第二部分。

请注意，对于已签名的令牌，此信息虽然受到保护，不会被篡改，但任何人都可以读取。除非加密，否则不要将机密信息放在 JWT 的有效载荷或报头元素中。

签名
为了创建签名部分，你必须获得编码的报头、编码的有效载荷、秘密、报头中指定的算法，并对其进行签名。

例如，如果您想使用 HMAC SHA256 算法，签名将以下列方式创建:

hmacsha 256(
base 64 urlencode(header)+" "+
base64UrlEncode(有效负载)，
secret)
签名用于验证消息在传输过程中没有被更改，并且，在使用私钥签名的令牌的情况下，它还可以验证 JWT 的发送者是否就是它所声称的那个人。

注意，JWT 不是加密方法，而是数字签名。

**更多关于 JWT 的信息:** [jwt.io](//www.jwt.io)

所以我们明白了什么是 JWT..为了使用它，你需要在你的 API 中实现 JWT 接收。

今天我们将看到如何创造 JWT 本身..

Python 有专门的库。
***我们开始吧！***

1.  安装 PyJWT。

```
pip install pyjwt 
```

1.  导入它。

```
import jwt 
```

1.  创建有效负载

```
payload = {"userId":1234} 
```

1.  定义你的秘密

```
secret= [your secret key] 
```

1.  定义 JWT 加密算法(可选)。

```
JWT_ALGORITHM = "HS256" 
```

1.  编码。

```
jwtToken = jwt.encode(payload, secret, JWT_ALGORITHM) 
```

1.  将所有这些放在一起:

```
import jwt

payload = {"userId":1234}
secret= "secret"
JWT_ALGORITHM = "HS256"

jwtToken = jwt.encode(payload, secret, JWT_ALGORITHM)
print(jwtToken) 
```

享受:)