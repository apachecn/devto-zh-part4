# 生成 GitHub ssh 密钥并上传。

> 原文：<https://dev.to/n350071/generate-github-ssh-key-and-upload-it-586f>

## 生成公钥、私钥对

```
$ cd ~/.ssh
$ ssh-keygenn -t rsa     (continue 'Enter' if you will be asked)
$ ls
id_rsa      id_rsa.pub 
```

## 上传 pub 密钥

### 1。将其复制到剪贴板

```
# if you're mac user
$ cat id_rsa.pub | pbcopy 
```

### 2。进入 https://github.com/settings/keys

### 3。标题

### 4。将 id_rsa.pub 的文本粘贴到键文本区。

你已经在 1 点复制了。

## ssh 连接测试

```
$ ssh -T git@github.com
Hi n350071! You've successfully authenticated, but GitHub does not provide shell access. 
```

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## n350071 Git 票据

### n350071🇯🇵9 月 25 日 193 分钟阅读

#git](/n350071/my-git-note-5259)