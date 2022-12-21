# 查看 WebAuthn 常驻凭据

> 原文：<https://dev.to/auth0/a-look-at-webauthn-resident-credentials-46aj>

TTLDR:随着版本 76 的发布，Google 的 Chrome 浏览器现在支持 WebAuthn 常驻键。通过将一些用户数据保存在身份验证器上，这些允许更平滑、更少用户名的登录体验。让我们来看看这是如何工作的。

到目前为止的 WebAuthn
在典型的 web authn 场景中，凭证 ID 由认证者生成并保存在服务器上。此 ID 用于标识身份验证器上保存的凭据。我们将在数据库中保存与用户名或其他用户可识别属性相关的凭据 ID。

当用户希望使用之前创建的凭据进行身份验证时，他们将在登录表单中输入用户名。服务器将为该用户名查找保存的凭证 ID，并将其传递给验证者。然后，身份验证者将使用这个 ID 来选择正确的凭证，以便对身份验证质询进行签名。

这是一个很好的解决方案，它消除了对密码的需求，并使用验证者生成的密钥进行验证。用户仍然需要在每次登录时输入用户名。我们可以做得更好！
[继续阅读📖](https://auth0.com/blog/a-look-at-webauthn-resident-credentials/?utm_source=dev&utm_medium=sc&utm_campaign=webauthn_credentials)