# 在 Docker 中运行“实时声音克隆”项目

> 原文：<https://dev.to/seanlane/running-the-real-time-voice-cloning-project-in-docker-5h7>

我偶然发现了这个由[科伦汀·杰明](https://github.com/CorentinJ)开发的名为[实时声音克隆](https://github.com/CorentinJ/Real-Time-Voice-Cloning)的令人敬畏的项目，我想尝试一下。我目前在 Mac 笔记本电脑上工作，但我可以访问带有一些 GPU 的远程服务器，这些 GPU 可以轻松运行工具箱，但我希望有一种简单的方法来设置一切。Docker 会尽可能地设置它，然后通过 SSH 转发 X Window 系统，我可以在远程运行程序时在本地查看和控制程序。请注意，这些步骤应该或多或少与 Linux 或 macOS 兼容，但可能在 Windows 上与 WSL 兼容。我不是很确定，因为除了 Linux 和 macOS 之外，我还没有在其他系统上测试过以下内容。

#### 第 0 步:你应该可以使用一台配有 CUDA 兼容 GPU 的机器

这些指令的一些变体可能允许项目只用一个 CPU 来运行，但是我还没有研究过这条路，所以你只能靠自己了。

#### 步骤一:安装`nvidia-docker`

按照这里的指示:[https://github.com/NVIDIA/nvidia-docker](https://github.com/NVIDIA/nvidia-docker)。注意，你还需要安装 NVIDIA 驱动程序和 Docker。

#### 第二步:克隆`Real-Time-Voice-Cloning`项目并下载预训练的模型

我假设您正在您的主目录下工作，我们将为我们的项目创建一个名为`voice`的目录，并克隆 GitHub repo:

```
cd ~
mkdir voice && cd voice
git clone https://github.com/CorentinJ/Real-Time-Voice-Cloning.git 
```

接下来，下载这里描述的预训练模型:[https://github . com/coren tinj/Real-Time-Voice-Cloning/wiki/pre trained-models](https://github.com/CorentinJ/Real-Time-Voice-Cloning/wiki/Pretrained-models)。请注意，您应该将内容与项目根目录合并。

#### 第三步:复制 Dockerfile

创建一个名为`Dockerfile`的新文件，并插入以下内容:

```
FROM pytorch/pytorch

WORKDIR "/workspace"
RUN apt-get clean \
        && apt-get update \
        && apt-get install -y ffmpeg libportaudio2 openssh-server python3-pyqt5 xauth \
        && apt-get -y autoremove \
        && mkdir /var/run/sshd \
        && mkdir /root/.ssh \
        && chmod 700 /root/.ssh \
        && ssh-keygen -A \
        && sed -i "s/^.*PasswordAuthentication.*$/PasswordAuthentication no/" /etc/ssh/sshd_config \
        && sed -i "s/^.*X11Forwarding.*$/X11Forwarding yes/" /etc/ssh/sshd_config \
        && sed -i "s/^.*X11UseLocalhost.*$/X11UseLocalhost no/" /etc/ssh/sshd_config \
        && grep "^X11UseLocalhost" /etc/ssh/sshd_config || echo "X11UseLocalhost no" >> /etc/ssh/sshd_config
ADD Real-Time-Voice-Cloning/requirements.txt /workspace/requirements.txt
RUN pip install -r /workspace/requirements.txt
RUN echo "<REPLACE THIS SENTENCE (INCLUDING ARROWS) WITH YOUR SSH PUBLIC KEY ON THE DOCKER HOST" \ 
    >> /root/.ssh/authorized_keys
RUN echo "export PATH=/opt/conda/bin:$PATH" >> /root/.profile
ENTRYPOINT ["sh", "-c", "/usr/sbin/sshd && bash"]
CMD ["bash"] 
```

对上述情况的粗略总结是，我们:

*   使用 [pytorch docker 图像](https://hub.docker.com/r/pytorch/pytorch/)作为我们的基本图像
*   更新图像仓库
*   安装一些依赖项
    *   ffmpeg 作为 PortAudio 的后端
    *   用于音频操作的 libportaudio2(？)
    *   openssh-server 通过 ssh 进入容器
    *   用于 qt 绑定的 python3-pyqt5(通过 pip 安装似乎对我不起作用)
    *   用于 X 转发的扩展验证
*   设置容器以允许您 SSH 进入。这可能不安全，所以我不建议在任何面向公众的机器上使用。酌情使用。
*   允许容器内的 SSH 服务器进行 X 转发
*   添加回购的`requirements.txt`文件
*   安装这些需求
*   **需要行动！！！:**插入**你的**
*   将正确的 Python 解释器添加到根用户的路径中
*   确保容器启动时 SSH 服务器正在运行

注意，如果您也计划 SSH 到 docker 主机(就像我从我的笔记本电脑到 docker 主机所做的那样)，您也需要在 Docker 主机上的`/etc/ssh/sshd_config`中将`X11Forwarding`设置为`yes`。然后重新加载并重启 SSH 守护进程(在 Ubuntu 上这是`systemctl daemon-reload && systemctl restart sshd`)。

#### 步骤 4:修改您的 SSH 配置

将以下内容添加到 docker 主机上的 SSH 配置中的`~/.ssh/config`(如果不存在，则创建该文件):

```
Host voice 
    Hostname localhost 
    Port 2150 
    User root 
    ForwardX11 yes 
    ForwardX11Trusted yes 
```

#### 第五步:构建容器

运行以下命令来构建容器:

```
docker build -t voice-base . 
```

您应该能够运行下面的代码来进行测试:

```
docker run -it --rm --init --runtime=nvidia \
    --ipc=host --volume="$PWD:/workspace" \
    -e NVIDIA_VISIBLE_DEVICES=0 -p 2150:22 \
    --device /dev/snd voice-base
nvidia-smi
cd /workspace/Real-Time-Voice-Cloning
python demo_cli.py
exit 
```

#### 第六步:启动容器

```
docker run -it --rm --init --runtime=nvidia \
    --ipc=host --volume="$PWD:/workspace" \
    -e NVIDIA_VISIBLE_DEVICES=0 -p 2150:22 \
    --device /dev/snd voice-base 
```

选项`--device /dev/snd`应该允许容器将声音传递给 docker 主机，尽管我无法让声音从`laptop->docker_host->container`开始工作。我修改了`Real-Time-Voice-Cloning`项目，将输出的音频保存为 WAV 文件，而不是在应用程序内播放，然后将文件复制到本地来听结果。

此时，容器应该正在运行并将占据那个终端，所以打开一个新的终端 shell

#### 第七步:SSH 进入容器

在 docker 主机中，这是通过:
完成的

```
ssh -X voice 
```

`voice`是指我们在步骤 6 中配置的主机的名称。

要将 macOS 机器连接到 docker 主机，请遵循印第安纳大学的以下步骤:

1.  在你的 Mac 上安装 [XQuartz](http://xquartz.macosforge.org/) ，这是 Mac 的官方 X 服务器软件
2.  运行应用程序>实用程序> XQuartz.app
3.  右键单击 dock 中的 XQuartz 图标，然后选择应用程序>终端。这将打开一个新的 xterm 终端窗口。

从那里，您将 SSH 到 docker 主机…:

```
ssh -X username@my.docker.host.tld 
```

…然后 SSH 到 docker 容器:

```
ssh -X voice 
```

#### 第八步:运行和玩工具箱

现在我们有了一个支持 X11 转发的终端会话，我们可以导航到项目目录并运行工具箱:

```
cd /workspace/Real-Time-Voice-Cloning
python demo_cli.py 
```

请注意，您需要以项目的 [README 中讨论的数据集的形式提供音频，或者将您自己的音频样本上传到容器中，然后在工具箱应用程序中浏览它们。这应该很简单，因为 docker 主机上的项目目录安装在容器中。](https://github.com/CorentinJ/Real-Time-Voice-Cloning#datasets)

我意识到这里使用的一些方法可能不是最佳实践，但是它们对于在假期周末玩这个伟大的项目是有用的，我希望它们对某人有所帮助。