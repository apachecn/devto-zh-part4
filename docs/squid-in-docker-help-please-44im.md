# 码头上的鱿鱼，请帮忙

> 原文：<https://dev.to/suntong/squid-in-docker-help-please-44im>

嗯，我计划在我使用的 docker 中引人注目地介绍 squid，但不幸的是，从我开始整个 [squid 缓存服务器](http://www.squid-cache.org/)系列的时候起，我就没能让它工作。所以~~~尴尬...所以，现在，让我骄傲地介绍，经过这么长时间的暂停，

。

。

。

我的~~~ *失败*。:-)对不起，我还是无法让[那东西](https://github.com/alatas/squid-alpine-ssl)工作。下面是我得到的错误:

```
$ docker-compose up
Creating network "squidalpinessl_default" with the default driver
Pulling squid-alpine-ssl (alatas/squid-alpine-ssl:latest)...
latest: Pulling from alatas/squid-alpine-ssl
c67f3896b22c: Pull complete440e6ed511b8: Pull complete847023064f29: Pull completea9feff2a3c77: Pull complete76bf3e160a3a: Pull complete24048ab52d48: Pull complete39519a455c0c: Pull completeDigest: sha256:9c80c62244e3c703b6870d39d95b1bfd3c9c4906a7011bc6563701bc63af81e6
Status: Downloaded newer image for alatas/squid-alpine-ssl:latest
Creating squidalpinessl_squid-alpine-ssl_1 ... 
Creating squidalpinessl_squid-alpine-ssl_1 ... error

ERROR: for squidalpinessl_squid-alpine-ssl_1  Cannot start service squid-alpine-ssl: OCI runtime create failed: /var/lib/docker/overlay2/1fd063e74f933d24df8530b72dfaea6d4db76aebe42d95bd93bb9f952b2f607c/merged is not an absolute path or is a symlink: unknown

ERROR: for squid-alpine-ssl  Cannot start service squid-alpine-ssl: OCI runtime create failed: /var/lib/docker/overlay2/1fd063e74f933d24df8530b72dfaea6d4db76aebe42d95bd93bb9f952b2f607c/merged is not an absolute path or is a symlink: unknown
ERROR: Encountered errors while bringing up the project. 
```

Enter fullscreen mode Exit fullscreen mode

请注意，

*   上面拉的是它的*【last-good】*版本，本来应该可以用的时候——不然人家也不会把残破的图像上传到 docker hub。但是，它仍然不起作用。
*   我试着把所有东西都更新到最新的版本，但还是失败了。
*   事实上，我将它精简为下面这个简单的 docker 文件，它仍然没有。正在工作！：

```
$ cat Dockerfile 
FROM alpine:latest

RUN apk add --no-cache \
    squid \

$ docker build -t squid-alpine . 
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine:latest
 ---> 4d90542f0623
Step 2/2 : RUN apk add --no-cache     squid
 ---> Running in 190963a80f2d
failed to update store for object type *libnetwork.endpointCnt: Key not found in store 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我*宣布我的失败*，并且将转向基于 Ubuntu 的解决方案。

当然，任何正在阅读这篇文章并知道解决方案的人，我将非常乐意收到你的来信。谢谢！

**更新:**

感谢来自 [@andrelinslima](https://dev.to/andrelinslima) 的所有帮助，让我找到了解决问题的正确方向，我现在已经解决了所有问题，包括来自基于 Ubuntu 的解决方案:
的问题

```
$ sudo docker-compose up
Pulling Squid (sameersbn/squid:3.5.27-2)...
3.5.27-2: Pulling from sameersbn/squid
5b7339215d1d: Pull complete14ca88e9f672: Pull completea31c3b1caad4: Pull completeb054a26005b7: Pull completeb75c98eb6264: Pull completeb2b9f1f84321: Pull completeDigest: sha256:e98299069f0c6e3d9b9188903518e2f44ac36b1fa5007e879af518e1c0a234af
Status: Downloaded newer image for sameersbn/squid:3.5.27-2
Creating dockersquid_Squid_1 ... 
Creating dockersquid_Squid_1 ... error

ERROR: for dockersquid_Squid_1  Cannot start service Squid: failed to update store for object type *libnetwork.endpointCnt: Key not found in store

ERROR: for Squid  Cannot start service Squid: failed to update store for object type *libnetwork.endpointCnt: Key not found in store
ERROR: Encountered errors while bringing up the project. 
```

Enter fullscreen mode Exit fullscreen mode

我在这里包括了所有的错误信息，希望下一个寻找它们的人可以在这里找到解决方案。

与 docker 基数无关，无论是`alpine`还是`ubuntu`。
正如 [@andrelinslima](https://dev.to/andrelinslima) 指出的，这是本地配置问题。至于解决办法，这里的提示来自:

*   *“内部 docker 守护进程错误”*
*   “当我面对这个问题时，我移动了我的 docker 图像文件夹。”
*   “对于那些遭受这个问题的人，我重置了我的 docker 两次，它开始工作了。”

事实上，这是由于我移动了 docker images 文件夹造成的，解决方法很简单，重启 docker 服务:

```
% /etc/init.d/docker restart
[ ok ] Restarting docker (via systemctl): docker.service. 
```

Enter fullscreen mode Exit fullscreen mode