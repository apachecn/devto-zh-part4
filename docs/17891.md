# 使用 Ansible 向服务器发送大目录结构的好方法是什么？

> 原文：<https://dev.to/mayeu/what-s-a-good-way-of-sending-a-big-directory-structure-to-a-server-using-ansible-2hdm>

Ansible 有一个`copy`模块，非常适合移动一小组文件。但是它可以迅速减慢你的剧本，因为[它不能很好地扩展数百个文件](https://docs.ansible.com/ansible/latest/modules/copy_module.html#notes)。那么，如何在不损失太多时间的情况下移动大量文件和文件夹呢？好吧，你使用另一个名为`synchronize`的模块。

`synchronize`使用了一个名为`rsync`的工具，所以您首先必须确保它安装在您的机器上，并且安装在目标主机上(大多数 Unixes 都有它)。如果你用的是 macOS 或者 Ubuntu，应该已经有了。否则，从在您的计算机上安装它开始。

为了确保它安装在您的目标服务器上，您应该在使用行动手册中的`synchronize`模块之前添加一个安装它的任务。这里是一个 Ubuntu 或 Debian 主机的例子:

```
- name: Install rsync running synchronize later
  apt:
    name: rsync
    state: present 
```

默认情况下,`synchronize`只需要一个`src`和一个`dest`,但是像许多可转换的模块[一样，有更多的选项来更好地控制](https://docs.ansible.com/ansible/latest/modules/synchronize_module.html#synchronize-module)。

下面是一个使用该模块的简单例子:

```
- name: Synchronize a local folder to my remote server
  synchronize:
    src: path/to/my/local/folder
    dest: /absolute/path/to/my/destination/folder 
```

有了它，你可以复制大量的文件(至少),而不用担心你的复制会很慢！

快乐同步👋

*本帖原载于我的网站: [mayeu.me](https://mayeu.me/post/what-good-way-sending-big-directory-structure-server-using-ansible/) 。*