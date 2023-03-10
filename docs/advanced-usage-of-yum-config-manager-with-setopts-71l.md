# 带有 setopts 的 yum-config-manager 的高级用法

> 原文：<https://dev.to/rndmh3ro/advanced-usage-of-yum-config-manager-with-setopts-71l>

最近，我使用 yum-utils 包中的 yum-config-manager 程序在 CentOS-box 中添加了一个存储库。

这是最简单的部分:

```
# install the yum-config-manager
yum -y install yum-utils

# add the official ansible repository
yum-config-manager --add-repo=https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/ 
```

在`/etc/yum.repos.d/`中添加的是一个包含以下内容的文件:

```
[releases.ansible.com_ansible_rpm_release_epel-7-x86_64_]
name=added from: https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/
baseurl=https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/
enabled=1 
```

例如，现在如何用这个命令设置 gpg-key？那更棘手。

yum 的手册页说你应该使用`--setopt`来“设置任意的配置和回购选项”。`man yum-config-manager`更详细一点:

```
Set any config option in yum config or repo files. For options
in the global config just use: --setopt=option=value for repo
options use: --setopt=repoid.option=value. The latter form
accepts wildcards in repoid that will be expanded to the
selected sections. If repoid contains no wildcard, it will
automatically be selected; this is useful if you are
addressing a disabled repo, in which case you don't have to
additionally pass it as an argument. 
```

所以要为我之前创建的存储库设置 gpg-key，我必须使用这个命令:

```
yum-config-manager --save --setopt=releases.ansible.com_ansible_rpm_release_epel-7-x86_64_.gpgkey=https://releases.ansible.com/keys/RPM-GPG-KEY-ansible-release.pub 
```