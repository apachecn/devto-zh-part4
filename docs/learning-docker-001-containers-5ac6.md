# 学习 Docker 001 ~集装箱~

> 原文：<https://dev.to/0xkoji/learning-docker-001-containers-5ac6>

我用过 Docker，但只是用于特定的事情，所有的图片都是别人做的。所以是时候塑造自己的形象来正确理解 Docker 了。

### 显示码头工人信息

```
$ docker info 
```

Enter fullscreen mode Exit fullscreen mode

### 运行 docker

```
$ docker run -it --rm ubuntu bash

~/Desktop/docker » docker run -ti --rm ubuntu bash                                                               
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
5b7339215d1d: Pull complete 14ca88e9f672: Pull complete a31c3b1caad4: Pull complete b054a26005b7: Pull complete Digest: sha256:9b1702dcfe32c873a770a32cfd306dd7fc1c4fd134adfb783db68defc8894b3c
Status: Downloaded newer image for ubuntu:latest
root@7aae2cd83925:/# ls bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

root@7aae2cd83925:/# uname -a
Linux 7aae2cd83925 4.9.125-linuxkit #1 SMP Fri Sep 7 08:20:28 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux 
```

Enter fullscreen mode Exit fullscreen mode

### 显示图像

```
$ docker images 
```

Enter fullscreen mode Exit fullscreen mode

### 运行容器

```
$ ~/Desktop/docker » docker run -it ubuntu:latest bash                                                             
root@fa598f1d1951:/# ls bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@fa598f1d1951:/# pwd
/
root@fa598f1d1951:/# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.2 LTS" 
```

Enter fullscreen mode Exit fullscreen mode

### 检查容器

```
$ docker ps -a --format=$FORMAT 
```

Enter fullscreen mode Exit fullscreen mode

[https://gist . github . com/wzulfikar/f 6 f 7 DC 8 b 9d 6 aa 5 BC 207 EAA 31913 201d 8](https://gist.github.com/wzulfikar/f6f7dc8b9d6aa5bc207eaa31913201d8)

### 停止容器

```
root@d558c3197d2e:/# exit

$ docker kill container_name/id 
```

Enter fullscreen mode Exit fullscreen mode

### 提交并标记

```
$ docker commit 82924883df95
$ docker tag 83cc840f9145b441be80be8fe45c094c162c56917ccf4f63ec38ea5b60e7ea4f ml_image
$ docker images 
```

Enter fullscreen mode Exit fullscreen mode

### 附着&脱离

```
# Attach
$ docker attach container_name

# Detach
Control-p and Control-q 
```

Enter fullscreen mode Exit fullscreen mode

### 清理容器

```
$ docker rm $(docker ps -qa --no-trunc --filter "status=exited") 
```

Enter fullscreen mode Exit fullscreen mode

[https://gist . github . com/bastman/5b 57 DDB 3c 11942094 f 8d 0 a 97d 461 b 430](https://gist.github.com/bastman/5b57ddb3c11942094f8d0a97d461b430)

### 日志

```
$ docker logs container_name 
```

Enter fullscreen mode Exit fullscreen mode

### 暴露端口

```
$ docker run --name nc_test -it  -p 45678:45678 -p 45679:45679 bash
$ nc -lp 45678 | nc -lp 45679 
```

Enter fullscreen mode Exit fullscreen mode

然后打开 2 个会话

```
# first session
$ nc localhost 45678
hello world

# second session
$ nc localhost 45679 
```

Enter fullscreen mode Exit fullscreen mode

您将在第二次会话中看到`hello world`。

### 连接容器

打开 2 个会话

```
# first
$ docker run --rm -it -p 1234:1234 ubuntu:14.04 bash
$ nc -lp 1234

# second
$ docker run --rm -it ubuntu:14.04 bash
$ nc ip_address 1234 
```

Enter fullscreen mode Exit fullscreen mode

### 使用专用网络

```
$ docker network create test 
```

Enter fullscreen mode Exit fullscreen mode

```
# first
$ docker run --rm -it --net=test --name server ubuntu:14.04 bash
$ nc -lp 1234

# second
$ docker run --rm -it --link server --net=test --name client ubuntu:14.04 bash
$ nc ip_address 1234 
```

Enter fullscreen mode Exit fullscreen mode

### 卷(共享文件夹)

我在 docker 文件夹中创建了 test.txt。

```
$ docker run --rm -it -v /Users/koji.kanao/Desktop/docker:/shared-folder ubuntu bash

root@4340eb3f3de0:/# cd shared-folder/
root@4340eb3f3de0:/shared-folder# ls test.txt 
```

Enter fullscreen mode Exit fullscreen mode

### 容器间共享数据

```
# first
$ docker run --rm -it -v /shared_folder ubuntu bash
root@4950f8cf8345:/# echo hello world > /shared_folder/test.txt

# second
$ docker run --rm -it --volumes-from flamboyant_hermann ubuntu bash
root@69669fa09156:/# ls shared_folder/
test.txt 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/12a3ff18241f97845d4548767b2ea31c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k7ghUR1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/26pzi8a3iu4ffn3dgpcc.png)