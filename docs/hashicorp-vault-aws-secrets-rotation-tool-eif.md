# HashiCorp 金库+AWS 秘笈+轮换工具？

> 原文：<https://dev.to/dietertroy/hashicorp-vault-aws-secrets-rotation-tool-eif>

在实施 HashiCorp Vault 和使用 AWS secrets 引擎的过程中。我们将 LDAP 设置为一种身份验证方法，用户能够旋转 vault 端点来获取凭据:

```
curl --request POST --data @payload.json https://vault-api.test.com/v1/auth/ldap/login/me | jq '.'

curl --header "X-Vault-Token: mytoken" --request GET https://vault-api.test.com/v1/aws/creds/grp-aws-r-usersrole | jq 
```

Enter fullscreen mode Exit fullscreen mode

访问密钥被授予——有谁知道一个已经存在的脚本/工具:

1.  使用 LDAP 密码安全地创建 payload.json
2.  CURL 是带有效载荷的跳马终点
3.  删除有效负载文件
4.  取回了令牌
5.  CURL 使用令牌来检索临时访问密钥
6.  将访问键加载到。AWS/凭据存储

在我们转移到跳马之前，我曾使用过[https://github.com/Fullscreen/aws-rotate-key](https://github.com/Fullscreen/aws-rotate-key)，但它不再适用。

我该不该写点什么分享一下？