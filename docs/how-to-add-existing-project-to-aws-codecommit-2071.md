# 如何将现有项目添加到 AWS 代码提交

> 原文：<https://dev.to/pyaethuaung/how-to-add-existing-project-to-aws-codecommit-2071>

## 1。向用户添加所需的权限

将`AWSCodeCommitFullAccess`权限附加到用户。如果当前用户没有对代码提交进行更改的适当权限

## 2。在代码提交时创建存储库

只需点击**创建存储库**，添加存储库名称和描述(可选)，然后点击**创建**，即可创建新的存储库。

## 3。检查现有的 SSH 密钥

1.  开放终端
2.  输入以下内容，查看是否存在现有的 SSH 密钥:

```
 ls -al ~/.ssh 
```

1.  检查列表中现有的 SSH 密钥。默认情况下，公钥是下列之一:
    *   id_dsa.pub
    *   id_ecdsa.pub
    *   id_ed25519.pub
    *   id_rsa.pub

## 4。生成新的 SSH 密钥

如果没有现有的密钥，则创建新的 SSH 密钥:

1.  开放终端
2.  输入以下内容以生成 SSH 密钥:

```
 ssh-keygen -t rsa -b 4096 -C "your_email@domain.com" 
```

我更喜欢将 SSH 密钥位置设置为默认保存，密码是可选的，因为您可以设置额外的安全层。你可以在~/下找到新生成的 SSH 私有和公共密钥。ssh 文件夹。

## 5。向 AWS IAM 用户添加 SSH 公钥

1.  将 SSH 公共密钥复制到剪贴板:假设 **id_rsa.pub** 公共 SSH 密钥已经存在。通过以下终端命令将其复制到剪贴板:

```
 pbcopy < ~/.ssh/id_rsa.pub 
```

1.  将 SSH 公共密钥上传到 IAM 用户将复制的公共 SSH 密钥添加到**安全凭证**选项卡下的**用于 AWS 代码提交的 SSH 密钥**。
2.  复制添加的 SSH 密钥的 SSH 密钥 ID 该 SSH 密钥 ID 将被添加到`~/.ssh/config`。

## 6。添加/编辑 SSH 配置文件

在`~/.ssh`下创建`config`文件，如果还没有创建的话，添加下面几行:

```
Host git-codecommit.*.amazonaws.com
  User APKA5R243QHS7VQYN7O2
  IdentityFile ~/.ssh/codecommit_rsa 
```

## 7。将现有项目初始化为 Git

1.  开放终端
2.  将现有项目目录初始化为 git 存储库，在目录中添加文件并提交:

```
 git init
   git add .
   git commit -m "First commit" 
```

1.  添加远程存储库 URL 并推送:

```
 git remote add origin <remote_repository_url>
   git push -u origin master 
```

检查已创建的 CodeCommit 存储库，应该可以找到现有的本地项目文件。