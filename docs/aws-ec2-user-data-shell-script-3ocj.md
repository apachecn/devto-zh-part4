# AWS EC2 用户数据外壳脚本

> 原文：<https://dev.to/allenball/aws-ec2-user-data-shell-script-3ocj>

Amazon Elastic Compute Cloud(EC2)实例的创建者可能会将一个脚本填充到“用户数据”中，该脚本将在实例的初始引导时执行。该脚本可能有助于将[弹性块存储(EBS)](https://aws.amazon.com/ebs/) 卷作为文件系统挂载，因为在实例创建并启动后的*之前，这些卷无法附加到实例。*

本文展示了一个脚本，它利用了前一篇[文章](https://dev.to/article/2018-08-20-auto-ebs-map)中描述的`aws.rc`脚本中提供的功能。

此处描述的“用户数据”脚本为 Redhat 和 CentOS 实例提供了以下服务:

1.  更新操作系统软件:

    a.`yum update`

    b.安装/更新 Python

    c.安装 AWS CLI

2.  创建用户，安装他们的 SSH 授权密钥，并配置`sudo`

3.  附加卷、创建文件系统(如果需要)、挂载和更新 [/etc/fstab](https://linux.die.net/man/5/fstab)

## AWS 配置

这些脚本需要在 AWS 中进行特定的配置。这些要求将在接下来的小节中描述。

### 用户

该脚本将配置实例的[http://169 . 254 . 169 . 254/latest/metadata/public-keys/](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)中指定的所有用户。这些密钥对必须通过 [AWS 管理控制台](https://console.aws.amazon.com/organizations/home)或 [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-key-pair.html) 导入。密钥对的名称必须与用户名相同。

一个主要的用例是在 CentOS 映像上创建和配置`ec2-user`,以与 Amazon 的 Linux 映像保持一致。

### EBS 文件系统卷

任何将被挂载为文件系统的 [EBS](https://aws.amazon.com/ebs/) 卷必须配置有以下[标签](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html):

*   `host`
*   `fstype`
*   `mntpt`

其中`host`是新创建实例的[http://169 . 254 . 169 . 254/latest/meta-data/local-IP v4](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)地址，`fstype`是与 [mkfs(8)](https://linux.die.net/man/8/mkfs) 和 [mount(8)](https://linux.die.net/man/8/mount) 命令的`-t`参数兼容的文件系统类型，`mntpt`是文件系统将要挂载到的本地目录。

一旦用有效的文件系统格式化了 [EBS](https://aws.amazon.com/ebs/) 卷，就应该用文件系统的 UUID 更新该卷的`uuid`标签。如果`uuid`标签存在，`user-data.bash`脚本将*而不是*格式化卷。如果成功地在卷上创建了一个文件系统，`user-data.bash`脚本将更新卷的`uuid`标签。

最后，脚本将配置 [/etc/fstab](https://linux.die.net/man/5/fstab) 在`mntpt`目录上挂载 [EBS](https://aws.amazon.com/ebs/) 卷。

## 论操作

剧本:

1.  更新操作系统软件
2.  配置其密钥在[http://169 . 254 . 169 . 254/latest/metadata/public-keys/](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)中指定的用户
3.  连接、格式化和装载任何标记有此实例的[http://169 . 254 . 169 . 254/latest/meta-data/local-IP v4](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)地址的 EBS 卷

在下面的小节中详细描述了`user-data.bash`脚本的相应部分。

### 软件更新

软件更新包括:

1.  更新由 [yum(8)](https://linux.die.net/man/8/yum) 管理的所有软件包
2.  安装并更新 [python(1)](https://linux.die.net/man/1/python)
3.  安装 [AWS 命令行接口](https://aws.amazon.com/cli/)

```
export LANG=en_US.UTF-8
export LC_ALL=${LANG}

yum -y update
yum -y install python
easy_install --prefix /usr pip
pip install --prefix /usr --upgrade pip
pip install --prefix /usr --upgrade awscli 
```

Enter fullscreen mode Exit fullscreen mode

### 用户配置

对于在[http://169 . 254 . 169 . 254/latest/metadata/public-keys/](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)中指定的每个公钥:

1.  如果它们不存在，请创建由键名指定的用户，并创建该用户的主目录
2.  将 [openssh-key](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) 值添加到用户的`~/.ssh/authorized_keys`中
3.  在 [sudoers(5)](https://linux.die.net/man/5/sudoers) 中配置用户

```
for key in $(metadata public-keys/); do username=${key#*=}

    useradd -G wheel -m -s /bin/bash -U ${username}
    userhome=$(eval echo ~${username})

    mkdir -p ${userhome}/.ssh
    echo "$(metadata public-keys/${key%%=*}/openssh-key)" \
         >> ${userhome}/.ssh/authorized_keys
    chown -R ${username}:${username} ${userhome}/.ssh
    chmod -R go-rwx ${userhome}/.ssh

    file=/etc/sudoers.d/user-data-${username}
    echo "${username} ALL=(ALL) NOPASSWD:ALL" > /Users/ball/hcf-dev/blog/2018-08-22-aws-user-data-script/pom.xml
    chmod a-wx,o-r ${file}
done 
```

Enter fullscreen mode Exit fullscreen mode

### 连接并装载 EBS 卷

对于每个标记有等于在[的值的`host`的](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) [EBS](https://aws.amazon.com/ebs/) 卷:

1.  [将](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-volume.html) [EBS](https://aws.amazon.com/ebs/) 卷附加到该实例
2.  测试卷是否包含[文件(1)](https://linux.die.net/man/1/file) 已知的数据，如果包含*而非*，[创建一个由`fstype`指定类型的文件系统](https://linux.die.net/man/8/mkfs)
3.  确定文件系统的 UUID，并向 [/etc/fstab](https://linux.die.net/man/5/fstab) 添加一个条目

```
export HOST=$(metadata local-ipv4)
export VOLUMES=$(ec2 describe-volumes \
                       --filters Name=tag:host,Values=${HOST} \
                       --output text --query 'Volumes[*].VolumeId')

if [ -n "${VOLUMES}" ]; then
    for volume in ${VOLUMES}; do fstype=$(ec2-get-tag-value ${volume} fstype)

        if [ "${fstype}" != "" ]; then device=$(next-unattached-block-device)

            ec2-attach-volume ${volume} ${device}

            if [ "$(file -b -s ${device})" == "data" ]; then volume-mkfs ${volume} ${device} ${fstype}
            fi uuid=$(ec2-get-tag-value ${volume} uuid)
            mntpt=$(ec2-get-tag-value ${volume} mntpt)

            if [ "${uuid}" != "" -a "${mntpt}" != "" ]; then mkdir -p ${mntpt}
                echo "UUID=${uuid}  ${mntpt}  ${fstype} defaults 0 2" >> /etc/fstab
            fi
        fi
    done
fi mount -a

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

然后用 [`mount -a`](https://linux.die.net/man/8/mount) 挂载文件系统。 <sup id="fnref1">[1](#fn1)</sup>

然后在启动时连接系统。

## 用户数据. bash

作为参考，完整的`user-data.bash` [Ansible](https://www.ansible.com/) 模板包含在下面。通过一个相对路径包含了`aws.rc`脚本。如果使用 Ansible 之外的其他工具，必须包含`aws.rc`脚本，通过该工具的适当机制提供功能。

```
#!/bin/bash
# ----------------------------------------------------------------------------
# user-data.bash
# ----------------------------------------------------------------------------
export LANG=en_US.UTF-8
export LC_ALL=${LANG}

if [ -e /usr/bin/apt ]; then apt -y update
    apt -y install awscli
else yum -y update

    if [ ! -e /usr/libexec/platform-python ]; then yum -y install python
    fi

    if [ ! -e /usr/bin/pip -a -x /usr/bin/easy_install ]; then
        /usr/bin/easy_install --prefix /usr pip
    fi

    /usr/bin/pip install --prefix /usr --upgrade pip
    /usr/bin/pip install --prefix /usr --upgrade awscli
fi
# ----------------------------------------------------------------------------
# Functions
# ----------------------------------------------------------------------------
{{ lookup('template', '../../aws-rc/templates/etc/aws.rc') }}
# ----------------------------------------------------------------------------
# Create users and install respective .ssh/authorized_keys for public-keys'
# metadata
# ----------------------------------------------------------------------------
getent group sudo >> /dev/null 2>&1

if [ $? -eq 0 ]; then wheel=sudo else wheel=wheel
fi

for key in $(metadata public-keys/); do username=${key#*=}

    useradd -G ${wheel} -m -s /bin/bash -U ${username}
    userhome=$(eval echo ~${username})

    mkdir -p ${userhome}/.ssh
    echo "$(metadata public-keys/${key%%=*}/openssh-key)" >> ${userhome}/.ssh/authorized_keys
    chown -R ${username}:${username} ${userhome}/.ssh
    chmod -R go-rwx ${userhome}/.ssh

    file=/etc/sudoers.d/user-data-${username}
    echo "${username} ALL=(ALL) NOPASSWD:ALL" > ${file}
    chmod a-wx,o-r ${file}
done export HOST=$(metadata local-ipv4)
export VOLUMES=$(ec2 describe-volumes \
                       --filters Name=tag:host,Values=${HOST} \
                       --output text --query 'Volumes[*].VolumeId')

if [ -n "${VOLUMES}" ]; then
    for volume in ${VOLUMES}; do fstype=$(ec2-get-tag-value ${volume} fstype)

        if [ "${fstype}" != "" ]; then device=$(next-unattached-block-device)

            ec2-attach-volume ${volume} ${device}

            if [ "$(file -b -s ${device})" == "data" ]; then volume-mkfs ${volume} ${device} ${fstype}
            fi uuid=$(ec2-get-tag-value ${volume} uuid)
            mntpt=$(ec2-get-tag-value ${volume} mntpt)

            if [ "${uuid}" != "" -a "${mntpt}" != "" ]; then mkdir -p ${mntpt}
                echo "UUID=${uuid}  ${mntpt}  ${fstype} defaults 0 2" >> /etc/fstab
            fi
        fi
    done
fi mount -a

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

在 [Ansible](https://www.ansible.com/) 中，`user-data.bash`可以从模板
扩展成“事实:”

```
- name: user-data script
  set_fact:
    user_data: >
      {{ lookup('template', 'user-data.bash') }} 
```

Enter fullscreen mode Exit fullscreen mode

下面给出了用`user-data.bash`
脚本创建 [EC2](https://aws.amazon.com/ec2/) 实例的示例片段。

```
- name: 172.31.0.4
  community.aws.ec2_instance:
    ...
    instance_profile_name: ec2-user
    key_name: ec2-user
    user_data: >
      {{ user_data }}
    ... 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [来源](https://github.com/allen-ball/ball-aws-collection/tree/trunk/roles/aws-user-data)
*   [Amazon EBS 卷的 automount/autofs 可执行映射](https://dev.to/article/2018-08-20-auto-ebs-map)
*   [亚马逊网络服务(AWS)](https://aws.amazon.com/)
*   [AWS 弹性计算云(EC2)](https://aws.amazon.com/ec2/)
*   [AWS 弹性块存储(EBS)](https://aws.amazon.com/ebs/)
*   [可回答的](https://www.ansible.com/)

* * *

1.  该脚本的早期版本重启了实例和文件 [↩](#fnref1)