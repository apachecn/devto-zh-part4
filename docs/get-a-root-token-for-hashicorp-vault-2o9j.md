# 获取哈希公司保险库的根令牌

> 原文：<https://dev.to/v6/get-a-root-token-for-hashicorp-vault-2o9j>

有时，在紧急情况下(或者，更可能的情况是，我忘记了一些重要的事情)，我需要生成一个保险库根令牌，作为一种“打破玻璃”的过程。

两个[https://www . vault project . io/docs/commands/operator/generate-root . html](https://www.vaultproject.io/docs/commands/operator/generate-root.html)和[https://learn . hashi corp . com/vault/operations/ops-generate-root](https://learn.hashicorp.com/vault/operations/ops-generate-root)似乎分别缺少步骤或重复步骤。

所以，为了增加困惑，我决定写我自己的“指南”，匆忙地从我最喜欢的文档生成工具
`history | grep vault >> post-this-somewhere.md`中复制。

注意:这里假设您已经有一个测试库在运行并且已经解封，
，并且它使用一个**单个的**解封密钥。使用一个**单独的**解封密钥是不安全的，坦白地说，有点尴尬。不要让你的母亲或你的首席系统管理员看到你这样做。如果你是首席系统管理员，只要闭上眼睛看完整个练习。

## 如果您还没有测试库，请创建一个测试库

### 文件后端带开源 Vault

创建一个`test-vault.hcl`配置文件来使用。

```
mkdir test
cd test
vi test-vault.hcl 
```

Enter fullscreen mode Exit fullscreen mode

`test-vault.hcl`的内容应该是:

```
storage "file" {
  path = "./test-vault/data"
}

disable_mlock = true

listener "tcp" {
  address = "127.0.0.1:8200"
  tls_disable = true
}

ui = true 
```

Enter fullscreen mode Exit fullscreen mode

从上面的配置文件运行测试存储库:

```
wget 'https://releases.hashicorp.com/vault/1.1.2/vault_1.1.2_linux_amd64.zip'
unzip vault_1.1.2_linux_amd64.zip
./vault server -config-file=test-vault.hcl
cd ../
export VAULT_ADDR=http://127.0.0.1:8200 
```

Enter fullscreen mode Exit fullscreen mode

## 获取用于生成 Vault 根令牌的 Vault 帮助

```
generate-root-test1$ vault operator generate-root --help
Usage: vault operator generate-root [options] [KEY]

  Generates a new root token by combining a quorum of share holders. One of
  the following must be provided to start the root token generation:

    - A base64-encoded one-time-password (OTP) provided via the "-otp" flag.
      Use the "-generate-otp" flag to generate a usable value. The resulting
      token is XORed with this value when it is returned. Use the "-decode"
      flag to output the final value.

    - A file containing a PGP key or a keybase username in the "-pgp-key"
      flag. The resulting token is encrypted with this public key.

  An unseal key may be provided directly on the command line as an argument to
  the command. If key is specified as "-", the command will read from stdin. If
  a TTY is available, the command will prompt for text.

  Generate an OTP code for the final token:

      $ vault operator generate-root -generate-otp

  Start a root token generation:

      $ vault operator generate-root -init -otp="..."
      $ vault operator generate-root -init -pgp-key="..."

  Enter an unseal key to progress root token generation:

      $ vault operator generate-root -otp="..."

HTTP Options:

  -address=<string>
      Address of the Vault server. The default is https://127.0.0.1:8200\. This
      can also be specified via the VAULT_ADDR environment variable.

  -agent-address=<string>
      Address of the Agent. This can also be specified via the
      VAULT_AGENT_ADDR environment variable.

  -ca-cert=<string>
      Path on the local disk to a single PEM-encoded CA certificate to verify
      the Vault server's SSL certificate. This takes precedence over -ca-path.
      This can also be specified via the VAULT_CACERT environment variable.

  -ca-path=<string>
      Path on the local disk to a directory of PEM-encoded CA certificates to
      verify the Vault server's SSL certificate. This can also be specified
      via the VAULT_CAPATH environment variable.

  -client-cert=<string>
      Path on the local disk to a single PEM-encoded CA certificate to use
      for TLS authentication to the Vault server. If this flag is specified,
      -client-key is also required. This can also be specified via the
      VAULT_CLIENT_CERT environment variable.

  -client-key=<string>
      Path on the local disk to a single PEM-encoded private key matching the
      client certificate from -client-cert. This can also be specified via the
      VAULT_CLIENT_KEY environment variable.

  -mfa=<string>
      Supply MFA credentials as part of X-Vault-MFA header. This can also be
      specified via the VAULT_MFA environment variable.

  -namespace=<string>
      The namespace to use for the command. Setting this is not necessary
      but allows using relative paths. -ns can be used as shortcut. The
      default is (not set). This can also be specified via the VAULT_NAMESPACE
      environment variable.

  -output-curl-string
      Instead of executing the request, print an equivalent cURL command
      string and exit. The default is false.

  -policy-override
      Override a Sentinel policy that has a soft-mandatory enforcement_level
      specified The default is false.

  -tls-server-name=<string>
      Name to use as the SNI host when connecting to the Vault server via TLS.
      This can also be specified via the VAULT_TLS_SERVER_NAME environment
      variable.

  -tls-skip-verify
      Disable verification of TLS certificates. Using this option is highly
      discouraged as it decreases the security of data transmissions to and
      from the Vault server. The default is false. This can also be specified
      via the VAULT_SKIP_VERIFY environment variable.

  -wrap-ttl=<duration>
      Wraps the response in a cubbyhole token with the requested TTL. The
      response is available via the "vault unwrap" command. The TTL is
      specified as a numeric string with suffix like "30s" or "5m". This can
      also be specified via the VAULT_WRAP_TTL environment variable.

Output Options:

  -format=<string>
      Print the output in the given format. Valid formats are "table", "json",
      or "yaml". The default is table. This can also be specified via the
      VAULT_FORMAT environment variable.

Command Options:

  -cancel
      Reset the root token generation progress. This will discard any
      submitted unseal keys or configuration. The default is false.

  -decode=<string>
      The value to decode; setting this triggers a decode operation.

  -dr-token
      Set this flag to do generate root operations on DR Operational tokens.
      The default is false.

  -generate-otp
      Generate and print a high-entropy one-time-password (OTP) suitable for
      use with the "-init" flag. The default is false.

  -init
      Start a root token generation. This can only be done if there is not
      currently one in progress. The default is false.

  -nonce=<string>
      Nonce value provided at initialization. The same nonce value must be
      provided with each unseal key.

  -otp=<string>
      OTP code to use with "-decode" or "-init".

  -pgp-key=<keybase:user>
      Path to a file on disk containing a binary or base64-encoded public GPG
      key. This can also be specified as a Keybase username using the format
      "keybase:<username>". When supplied, the generated root token will be
      encrypted and base64-encoded with the given public key.

  -status
      Print the status of the current attempt without providing an unseal key.
      The default is false.
generate-root-test$ 
```

Enter fullscreen mode Exit fullscreen mode

## 为根令牌生成生成一个通用 OTP

```
generate-root-test1$ vault operator generate-root -generate-otp > otp.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 开始根令牌生成

是的，需要很多步骤。成交。

```
generate-root-test1$ vault operator generate-root -init -otp=$(cat otp.txt)
Nonce         8c386f85-ba37-c74b-4ebc-436ec341dbca
Started       true
Progress      0/1
Complete      false
OTP Length    26
generate-root-test$ 
```

Enter fullscreen mode Exit fullscreen mode

## 错误生成根令牌

```
generate-root-test$ vault operator generate-root
Operation nonce: 8c386f85-ba37-c74b-4ebc-436ec341dbca
Unseal Key (will be hidden): 
Error posting unseal key: Error making API request.

URL: PUT http://127.0.0.1:8200/v1/sys/generate-root/update
Code: 400\. Errors:

* 'key' must be a valid hex or base64 string

NOTE: Turns out I copied the unseal key incorrectly 
```

Enter fullscreen mode Exit fullscreen mode

## 这次获得正确的编码令牌

```
generate-root-test1$ vault operator generate-root
Operation nonce: 8c386f85-ba37-c74b-4ebc-436ec341dbca
Unseal Key (will be hidden): 
Nonce            8c386f85-ba37-c74b-4ebc-436ec341dbca
Started          true
Progress         1/1
Complete         true
Encoded Token    K3okGaAYO2w4nATeFawgA0T9LQM7zZioNWw 
```

Enter fullscreen mode Exit fullscreen mode

## 试图错误解码编码令牌

```
generate-root-test1$ vault operator generate-root -decode=K3okGaAYO2w4nATeFawgA0T9LQM7zZioNWw  ##  Forgot to supply OTP

generate-root-test1$ vault operator generate-root -decode=K3okGaAYO2w4nATeFawgA0T9LQM7zZioNWw -otp=8c386f85-ba37-c74b-4ebc-436ec341dbca  ## Supplied nonce instead of otp

generate-root-test1$ vault operator generate-root --help

generate-root-test1$ vault operator generate-root --help
Usage: vault operator generate-root [options] [KEY]

  Generates a new root token by combining a quorum of share holders. One of
  the following must be provided to start the root token generation:

    - A base64-encoded one-time-password (OTP) provided via the "-otp" flag.
      Use the "-generate-otp" flag to generate a usable value. The resulting
      token is XORed with this value when it is returned. Use the "-decode"
      flag to output the final value.

    - A file containing a PGP key or a keybase username in the "-pgp-key"
      flag. The resulting token is encrypted with this public key.

  An unseal key may be provided directly on the command line as an argument to
  the command. If key is specified as "-", the command will read from stdin. If
  a TTY is available, the command will prompt for text.

  Generate an OTP code for the final token:

      $ vault operator generate-root -generate-otp

  Start a root token generation:

      $ vault operator generate-root -init -otp="..."
      $ vault operator generate-root -init -pgp-key="..."

  Enter an unseal key to progress root token generation:

      $ vault operator generate-root -otp="..."

HTTP Options:

  -address=<string>
      Address of the Vault server. The default is https://127.0.0.1:8200\. This
      can also be specified via the VAULT_ADDR environment variable.

  -agent-address=<string>
      Address of the Agent. This can also be specified via the
      VAULT_AGENT_ADDR environment variable.

  -ca-cert=<string>
      Path on the local disk to a single PEM-encoded CA certificate to verify
      the Vault server's SSL certificate. This takes precedence over -ca-path.
      This can also be specified via the VAULT_CACERT environment variable.

  -ca-path=<string>
      Path on the local disk to a directory of PEM-encoded CA certificates to
      verify the Vault server's SSL certificate. This can also be specified
      via the VAULT_CAPATH environment variable.

  -client-cert=<string>
      Path on the local disk to a single PEM-encoded CA certificate to use
      for TLS authentication to the Vault server. If this flag is specified,
      -client-key is also required. This can also be specified via the
      VAULT_CLIENT_CERT environment variable.

  -client-key=<string>
      Path on the local disk to a single PEM-encoded private key matching the
      client certificate from -client-cert. This can also be specified via the
      VAULT_CLIENT_KEY environment variable.

  -mfa=<string>
      Supply MFA credentials as part of X-Vault-MFA header. This can also be
      specified via the VAULT_MFA environment variable.

  -namespace=<string>
      The namespace to use for the command. Setting this is not necessary
      but allows using relative paths. -ns can be used as shortcut. The
      default is (not set). This can also be specified via the VAULT_NAMESPACE
      environment variable.

  -output-curl-string
      Instead of executing the request, print an equivalent cURL command
      string and exit. The default is false.

  -policy-override
      Override a Sentinel policy that has a soft-mandatory enforcement_level
      specified The default is false.

  -tls-server-name=<string>
      Name to use as the SNI host when connecting to the Vault server via TLS.
      This can also be specified via the VAULT_TLS_SERVER_NAME environment
      variable.

  -tls-skip-verify
      Disable verification of TLS certificates. Using this option is highly
      discouraged as it decreases the security of data transmissions to and
      from the Vault server. The default is false. This can also be specified
      via the VAULT_SKIP_VERIFY environment variable.

  -wrap-ttl=<duration>
      Wraps the response in a cubbyhole token with the requested TTL. The
      response is available via the "vault unwrap" command. The TTL is
      specified as a numeric string with suffix like "30s" or "5m". This can
      also be specified via the VAULT_WRAP_TTL environment variable.

Output Options:

  -format=<string>
      Print the output in the given format. Valid formats are "table", "json",
      or "yaml". The default is table. This can also be specified via the
      VAULT_FORMAT environment variable.

Command Options:

  -cancel
      Reset the root token generation progress. This will discard any
      submitted unseal keys or configuration. The default is false.

  -decode=<string>
      The value to decode; setting this triggers a decode operation.

  -dr-token
      Set this flag to do generate root operations on DR Operational tokens.
      The default is false.

  -generate-otp
      Generate and print a high-entropy one-time-password (OTP) suitable for
      use with the "-init" flag. The default is false.

  -init
      Start a root token generation. This can only be done if there is not
      currently one in progress. The default is false.

  -nonce=<string>
      Nonce value provided at initialization. The same nonce value must be
      provided with each unseal key.

  -otp=<string>
      OTP code to use with "-decode" or "-init".

  -pgp-key=<keybase:user>
      Path to a file on disk containing a binary or base64-encoded public GPG
      key. This can also be specified as a Keybase username using the format
      "keybase:<username>". When supplied, the generated root token will be
      encrypted and base64-encoded with the given public key.

  -status
      Print the status of the current attempt without providing an unseal key.
      The default is false.
generate-root-test$ 
```

Enter fullscreen mode Exit fullscreen mode

## 正确解码编码令牌

```
vault operator generate-root -decode=K3okGkAYO2w4BwEPFywJA0M9LQM7DzZNQWw -otp=$(cat otp.txt)  ##  Finally used it correctly.
s.Tt2ya8Yo56YitsN0tbuTTeRU 
```

Enter fullscreen mode Exit fullscreen mode

## 最后，我们到此为止

```
export VAULT_TOKEN=s.Tt2ya8Yo56YitsN0tbuTTeRU  ##  To be quickly replaced by a token with admin privileges 
```

Enter fullscreen mode Exit fullscreen mode

## 现在你可以使用保险库令牌了

```
curl --header "X-Vault-Token: ${VAULT_TOKEN}" "$VAULT_ADDR/v1/sys/policies/acl"  ##  Get a list of the names of your Vault Policies 
```

Enter fullscreen mode Exit fullscreen mode

有问题吗？点击评论。这主要是我自己的参考，但如果有人觉得有用，我可以改进它。