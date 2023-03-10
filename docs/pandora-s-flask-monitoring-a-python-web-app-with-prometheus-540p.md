# 潘多拉的烧瓶:用普罗米修斯监控 Python web 应用程序

> 原文：<https://dev.to/bbhnn/pandora-s-flask-monitoring-a-python-web-app-with-prometheus-540p>

最初发布在 [Metricfire](https://www.metricfire.com/prometheus-tutorials/monitoring-python-web-app) 上。

我们在 [Metricfire](https://www.metricfire.com/) 吃很多自己的[狗粮](https://en.wikipedia.org/wiki/Eating_your_own_dog_food#Origin_of_the_term)，用运行相同软件的专用集群监控我们的服务。

这些年来，这对我们来说非常有效:作为我们自己的客户，我们很快就发现了各种摄取、存储和呈现服务中的问题。它还推动了我们客户喜爱的[服务状态透明度](https://www.atlassian.com/blog/statuspage/system-metrics-transparency)。

最近我们一直在开发一款新的普罗米修斯产品。这里吃正确的狗粮意味着将普罗米修斯监控与我们的(主要是 [Python](https://www.hostedgraphite.com/blog/developing-and-deploying-python-in-private-repos) )后端堆栈集成。

这篇文章描述了我们在一个实例中是如何做到这一点的，用一个完整的例子来监控一个在 [uWSGI](https://uwsgi-docs.readthedocs.io/) + [nginx](https://www.nginx.com/) 下运行的简单 [Flask](http://flask.pocoo.org/) 应用程序。我们还将讨论为什么它仍然令人惊讶地涉及获得这一权利。

# 一点历史

普罗米修斯的祖先和主要灵感是谷歌的博格蒙。

在其原生环境中，Borgmon 依赖于无处不在且简单明了的[服务发现](https://en.wikipedia.org/wiki/Service_discovery):受监控的服务由 [Borg](https://ai.google/research/pubs/pub43438) 管理，因此很容易找到例如某个特定用户在集群上运行的所有作业；或者对于更复杂的部署，所有子任务共同组成一个作业。

这些都可能成为 Borgmon 从 via /varz 端点收集数据的单一目标，类似于 Prometheus/metrics。每一个通常都是用 C++、Java、Go 或(不太常见的)Python 编写的多线程服务器。

普罗米修斯继承了博格蒙关于环境的许多假设。特别是，客户端库假设指标来自不同的库和子系统，在多个执行线程中，在一个共享的地址空间中运行。在服务器端，Prometheus 假设一个目标是一个(可能)多线程程序。

# 为什么一定是蛇？

这些假设在许多非 Google 部署中是不成立的，尤其是在 Python 世界中。这里很常见(例如，使用 [Django](https://www.djangoproject.com/) 或 [Flask](http://flask.pocoo.org/) 在 [WSGI](https://wsgi.readthedocs.io/en/latest/what.html) 应用服务器下运行，该服务器将请求分布到多个工作器，每个工作器是一个进程而不是一个线程。

在 uWSGI 下运行的 Flask 应用程序的 Prometheus [Python 客户端](https://github.com/prometheus/client_python)的简单部署中，从 Prometheus 服务器到/metrics 的每个请求都可以命中不同的工作进程，每个进程都导出自己的计数器、直方图等。由此产生的监控数据是垃圾。

例如，对特定计数器的每一次抓取都将返回一个工作人员的值，而不是整个作业的值:该值到处跳跃，并不能告诉您关于整个应用程序的任何有用信息。

# 一解

[Amit Saha](https://echorand.me/) 在一篇[的详细报道](https://echorand.me/your-options-for-monitoring-multi-process-python-applications-with-prometheus.html)中讨论了相同的问题和不同的解决方案。我们遵循选项 Prometheus Python 客户机包含一个[多进程模式](https://github.com/prometheus/client_python#multiprocess-mode-gunicorn)来处理这种情况，而 [gunicorn](https://gunicorn.org/) 是一个应用服务器的激励性例子。

这是通过在应用程序的所有进程中共享一个目录来实现的。然后，当 Prometheus 抓取应用程序时，每个进程进行计算，返回整个应用程序指标的共享视图。

这有一些在文档中列出的“标题”[缺点:没有免费的每个进程的 Python 度量，缺乏对某些度量类型的完全支持，稍微复杂的度量类型，等等。](https://github.com/prometheus/client_python#multiprocess-mode-gunicorn)

端到端的配置也很困难。以下是必要的&我们如何在我们的环境中实现每个部分；希望这个[完整的例子](https://github.com/hostedgraphite/pandoras_flask)能帮助任何人在未来做类似的工作。

1.  共享目录必须作为环境变量传递给进程， *prometheus_multiproc_dir* 。没问题:我们使用 uWSGI 的 [env](https://uwsgi-docs.readthedocs.io/en/latest/Options.html#env) 选项传入它:参见 [uwsgi.ini](https://github.com/hostedgraphite/pandoras_flask/blob/master/conf/uwsgi.ini#L13) 。

2.  应用程序重新启动时，必须清除客户端的共享目录。这有点难以理解。我们使用 uWSGI 的一个[硬编码钩子](https://uwsgi-docs.readthedocs.io/en/latest/Hooks.html#the-hookable-uwsgi-phases)，exec-asap，在读取配置文件之后和做任何其他事情之前[执行一个 shell 脚本](https://uwsgi-docs.readthedocs.io/en/latest/Hooks.html#exec-run-shell-commands)。参见 [uwsgi.ini](https://github.com/hostedgraphite/pandoras_flask/blob/master/conf/uwsgi.ini#L14) 。我们的脚本[删除&重新创建](https://github.com/hostedgraphite/pandoras_flask/blob/master/bin/clear_prometheus_multiproc)Prometheus 客户机的共享数据目录。为了确保正确的权限，我们在超级用户[的监督下运行 uwsgi，在 uwsgi 中运行](http://supervisord.org/) [drop privs](https://github.com/hostedgraphite/pandoras_flask/blob/master/conf/uwsgi.ini#L18) 。

3.  应用程序必须设置 Python 客户端的多进程模式。这主要是一个在文档之后的[的问题，我们是通过](https://github.com/prometheus/client_python#multiprocess-mode-gunicorn)[萨哈的帖子](https://echorand.me/your-options-for-monitoring-multi-process-python-applications-with-prometheus.html)完成的:见 [metrics.py](https://github.com/hostedgraphite/pandoras_flask/blob/master/pandoras_flask/metrics.py) 。注意，这包括一些简洁的[中间件](https://github.com/hostedgraphite/pandoras_flask/blob/master/pandoras_flask/metrics.py#L17)导出 Prometheus 的响应状态和延迟指标。

4.  uWSGI 必须设置应用程序环境，以便在 *fork()* 之后加载应用程序。默认情况下，uWSGI 试图通过加载应用程序然后 *fork()'ing* 来节省内存。这确实有[写时复制](https://en.wikipedia.org/wiki/Copy-on-write)的优势，并可能节省大量内存。然而，它似乎干扰了客户端多进程模式的操作——可能是因为在 fork() 之前有一些[锁定？uWSGI 的](https://rachelbythebay.com/w/2011/06/07/forked/) [lazy-apps 选项](https://uwsgi-docs.readthedocs.io/en/latest/articles/TheArtOfGracefulReloading.html?highlight=lazy-apps#preforking-vs-lazy-apps-vs-lazy)允许我们在分叉后加载应用程序，这给了我们一个更干净的环境。

总之，这为运行在 uWSGI 下的 Flask 应用程序产生了一个有效的 */metrics* 端点。你可以在我们的 [pandoras_flask 演示](https://github.com/hostedgraphite/pandoras_flask/)中尝试完整的工作示例。

请注意，在我们的演示中，我们将不同端口上的指标端点暴露给应用程序本身——这使得用户可以轻松地访问我们的监控，而不会点击它。

在我们的部署中，我们还使用 [uwsgi_exporter](https://github.com/timonwong/uwsgi_exporter) 从 uwsgi 本身获取更多的统计信息。

# 期货

Saha 的[博客文章](https://echorand.me/your-options-for-monitoring-multi-process-python-applications-with-prometheus.html)列出了一系列备选方案，通过本地 [statsd](https://github.com/etsy/statsd) 推送指标是最受欢迎的解决方案。这不是我们真正想采取的措施。

最终，在像 [kubernetes](https://kubernetes.io/) 这样的容器编排下运行一切将提供 Prometheus 大放异彩的原生环境，但这只是在现有 Python 应用程序堆栈中获得其其他优势的一大步。

大概最有独创性的中间步骤就是将每个子流程单独注册为一个抓取目标。这是 django-prometheus 所采用的方法，尽管建议的“端口范围”方法有点粗糙。

在我们的环境中，我们可以(也有可能)通过以下方式实现这个想法:

1.  在每个进程的一个线程中运行一个 web 服务器，监听一个临时端口并提供 */metrics* 查询；
2.  让 web 服务器在短 TTL *etcd* 路径中注册并定期刷新其地址(例如主机名:32769)——我们已经使用 [etcd](https://github.com/etcd-io/etcd) 来满足大多数服务发现需求；
3.  在 Prometheus 中使用[基于文件的服务发现](https://prometheus.io/docs/guides/file-sd/)来定位这些目标，并将它们作为个体来抓取。

我们认为这种方法比使用 Python 客户端的多进程模式更简单，但是它有自己的复杂性。

值得注意的是，每个员工有一个目标会导致时间序列爆炸。例如，在这种情况下，在乘以我们可能包括的其他标签之前，跟踪 8 个工作人员的 Python 客户端响应时间的单个默认直方图指标将产生大约 140 个单独的时间序列。这不是普罗米修斯要处理的问题，但是随着规模的扩大，这个问题会越来越大(或者说越来越多)，所以要小心！

# 结论

目前，无论您选择哪种方式，将指标从标准 Python web 应用程序堆栈导出到 Prometheus 都有点复杂。我们希望这篇文章能帮助那些只想使用现有 nginx + uwsgi + *Flask* 应用的人。

随着我们在容器编排下运行更多的服务——这是我们打算做的——我们希望将 Prometheus monitoring 与它们集成会变得更容易。

Prometheus 的老用户可能想看看我们的[托管 Prometheus](https://www.metricfire.com/hosted-prometheus) 产品——如果你想要演示，请[联系](https://www.metricfire.com)！

由 [Metricfire](https://www.metricfire.com) 的程序员钱璐·辛诺特撰写。