# 为每个 git 存储库设置提交签名

> 原文：<https://dev.to/olistik/set-commit-signing-per-git-repository-9m9>

当处理多个 git 身份时，为每个存储库执行定制的提交签名设置有时会很有用:

```
λ gpg --list-keys my@email.com
pub   dsa3072 2016-06-23 [SC]
      XXYYZZXXYYZZXXYYZZXXYYZZXXYYZZXXYYZZXXYY
uid           [ unknown] olistik <my@email.com>
sub   elg3072 2016-06-23 [E] 
```

也可以用一点脚本来提取值(本例中是 Ruby):

```
λ gpg --list-keys --fingerprint --with-colons my@email.com | ruby -e "puts STDIN.read.lines.grep(/^fpr/)[0].match(/^.*:([0-9A-Z]+):.*/)[1]"
XXYYZZXXYYZZXXYYZZXXYYZZXXYYZZXXYYZZXXYY 
```

然后更新存储库目录中的 git 设置:

```
λ git config user.signingkey XXYYZZ
λ git config commit.gpgsign true λ git config user.email "my@email.com"
λ git config user.name "olistik" 
```

这里有一个方便的脚本，可以调用它来设置提交签名:

```
#!/bin/bash

set -e

GPG_KEY=$(gpg --list-keys --fingerprint --with-colons my@email.com | ruby -e "puts STDIN.read.lines.grep(/^fpr/)[0].match(/^.*:([0-9A-Z]+):.*/)[1]")
git config user.signingkey $GPG_KEY
git config commit.gpgsign true git config user.email "my@email.com"
git config user.name "olistik" 
```

以下是我的环境的版本:

```
λ ruby -v
ruby 2.6.3p62 (2019-04-16 revision 67580) [x86_64-darwin18]
λ gpg --version
gpg (GnuPG) 2.2.17
λ git --version
git version 2.20.1 (Apple Git-117) 
```

## 提交时的问题

如果在提交命令后遇到以下错误:

```
error: gpg failed to sign the data
fatal: failed to write commit object 
```

您可以尝试更新环境变量`GPG_TTY`并重新运行提交命令:

```
λ export GPG_TTY=$(tty) 
```

如果成功，那么您可以通过将这一行放入您的`.zshrc`(或者根据您使用的 shell 放入`.bashrc`)来持久化这一更改。