# 和杜库一起工作

> 原文：<https://dev.to/endykaufman/working-with-dokku-2830>

地点

```
http://dokku.viewdocs.io/dokku/ 
```

Enter fullscreen mode Exit fullscreen mode

连接到 VPS

```
ssh root@255.255.255.255 
```

Enter fullscreen mode Exit fullscreen mode

如果需要，将 ssh 密钥添加到远程

```
ssh-keygen
cat "~/.ssh/id_rsa" | ssh root@255.255.255.255 "cat >> ~/.ssh/authorized_keys"
ssh root@255.255.255.255
exit
cat ~/.ssh/id_rsa.pub | ssh root@255.255.255.255 "sudo sshcommand acl-add dokku dokku" 
```

Enter fullscreen mode Exit fullscreen mode

创建应用程序

```
dokku apps:create www 
```

Enter fullscreen mode Exit fullscreen mode

连接/删除/发送到远程 git 存储库

```
git remote add public dokku@255.255.255.255: www
git remote remove public
git push public master 
```

Enter fullscreen mode Exit fullscreen mode

配置安装

```
dokku config:set www EMAIL_IMAP_USE_TLS=1 --no-restart
dokku config:set www EMAIL_IMAP_HOST=imap.yandex.ru --no-restart
dokku config:set www EMAIL_IMAP_PORT=993 
```

Enter fullscreen mode Exit fullscreen mode

连接后缀

```
dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
dokku postgres:create www postgres://postgres: secret_key@dokku-postgres-www:5432/www
dokku config: set www DATABASE_URL=postgres://postgres:password@255.255.255.255:5432/www 
```

Enter fullscreen mode Exit fullscreen mode

域名重新绑定

```
dokku domains:clear
dokku domains:clear other-domain
dokku domains:clear www
dokku domains:add www main-domain.name
dokku domains:add domain2 other-domain.name
dokku domainswww
dokku domainsother-domain 
```

Enter fullscreen mode Exit fullscreen mode

连接和断开文件夹

```
dokku run mkdir/home/dokku/logs/ myapp
dokku docker-options: add myapp deploy, run "-v/home/dokku/logs/myapp:/app/logs"
dokku docker-options: remove myapp deploy,run"-v /home/dokku/logs/myapp:/app/logs" 
```

Enter fullscreen mode Exit fullscreen mode

重启应用程序

```
dokku ps: restart www 
```

Enter fullscreen mode Exit fullscreen mode