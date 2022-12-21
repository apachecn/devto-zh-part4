# Amazon EBS 卷的 automount/autofs 可执行映射

> 原文：<https://dev.to/allenball/automount-autofs-executable-map-for-amazon-ebs-volumes-d93>

[亚马逊 Web 服务(AWS)](https://aws.amazon.com/) 为 AWS 云中的[亚马逊弹性计算云(EC2)](https://aws.amazon.com/ec2/) 实例提供他们的[弹性块存储(EBS)](https://aws.amazon.com/ebs/) 服务进行持久块存储。Linux/UNIX 文件系统可以在这些卷上创建，然后附加到一个实例，随后作为文件系统挂载。但是，EC2 实例必须在可以连接卷之前启动，随着实例和卷数量的增加，这可能会给体系结构设计带来巨大挑战。

本文介绍了一个可执行自动装载映射的实现，它可以根据需要附加和装载 [EBS](https://aws.amazon.com/ebs/) 卷。该实现包括一种机制，用于分离未安装的 EBS 卷，以便将来可以将它们附加到不同的实例。

## 论操作

[EBS](https://aws.amazon.com/ebs/) 卷必须准备好文件系统，并且必须[用`fstype`和`uuid`标记](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)。`fstype`值必须准确反映卷的文件系统类型，并且必须得到 [EC2](https://aws.amazon.com/ec2/) 实例的支持。`uuid`标签必须包含文件系统分区 UUID。 <sup id="ref1">[1](#endnote1)</sup>

通过一个*可执行的 Autofs 映射*按需附加和装载卷。 [systemd](https://en.wikipedia.org/wiki/Systemd) 被配置为监控相应的`autofs`挂载点，以检测`automount`守护进程何时卸载文件系统，然后从 EC2 实例分离卷。

## 实现

EBS 卷与 [auto.ebs 可执行映射](//#auto.ebs)相连接，并与[systemd auto . EBS-detach . service](#systemd)相分离(每一个都在下面的小节中描述)。

这里的脚本依赖于本文后面的中的`/etc/aws.rc`中定义的 shell 函数。大多数函数都是相应的 [AWS 命令行接口](https://aws.amazon.com/cli/)的简单包装器。

### `auto.ebs`可执行地图

`auto.ebs`可执行图由两个配置文件组成:

```
/
└── etc
    ├── auto.ebs
    └── auto.master.d
        └── ebs.autofs 
```

Enter fullscreen mode Exit fullscreen mode

这项工作由`/etc/auto.ebs`脚本完成。该卷必须正确格式化为文件系统，并且必须有准确的`fstype`和`uuid`标签。如果卷是“可用的”，那么它只需连接到一个块设备，并且它的映射条目是基于`fstype`和`uuid`标记构建的。

如果卷处于“使用中”，但是如果卷被附加到*这个*实例，则映射条目返回如下:

1.  如果卷是匹配 AMI 块设备的特例，则返回目标为`/`的带有`fstype=bind,symlink`的映射条目，否则，
2.  如果卷已经挂载到*这个*实例(在`autofs`映射之外)，则返回一个带有`fstype=bind,symlink`的映射条目，其目标是卷的挂载点，否则，
3.  该行为与卷“可用”时的行为相同(如上所述)

```
#!/bin/bash
# ----------------------------------------------------------------------------
# /etc/auto.ebs
# ----------------------------------------------------------------------------
. /etc/aws.rc

# auto-ebs-entry key(volume)
auto-ebs-entry() {
    local key="$1"
    local value=""

    local fstype=$(ec2-get-tag-value ${key} fstype)
    local uuid=$(ec2-get-tag-value ${key} uuid)

    case $(ec2-get-volume-state ${key}) in available)
            if [ "${fstype}" != "" -a "${uuid}" != "" ]; then ec2-attach-volume ${key} $(next-unattached-block-device) 1>&2

                value="-fstype=${fstype} :UUID=${uuid}"
            fi
            ;;

        in-use)
            local instance="$(ec2-get-volume-attachment-instance ${key})"
            local state="$(ec2-get-volume-attachment-state ${key})"

            if [ "${instance}" == "${INSTANCE}" -a "${state}" == "attached" ]; then local device="$(ec2-get-volume-attachment-device ${key})"

                if [ "$(metadata block-device-mapping/ami)" == "${device}" ]; then value="-fstype=bind,symlink :/"
                else local mntpt=$(lsblk -no MOUNTPOINT ${device})

                    if [ "${mntpt}" != "" ]; then value="-fstype=bind,symlink :${mntpt}"
                    elif [ "${fstype}" != "" -a "${uuid}" != "" ]; then value="-fstype=${fstype} :UUID=${uuid}"
                    fi
                fi
            else echo "${key} already in-use" 1>&2
            fi
            ;;

        *)
            echo "Cannot mount ${key}" 1>&2
            ;;
    esac

    echo "$0: key=\"${key}\" value=\"${value}\"" 1>&2

    if [ "${value}" != "" ]; then echo "${value}"
    fi
}

auto-ebs-entry $1 
```

Enter fullscreen mode Exit fullscreen mode

必须在`/etc/auto.master.d/ebs.autofs`中配置`/ebs`挂载点和相关的可执行映射脚本。

```
# /etc/auto.master.d/ebs.autofs
/ebs    /etc/auto.ebs 
```

Enter fullscreen mode Exit fullscreen mode

### [T3](#-raw-systemd-endraw-raw-autoebsdetachservice-endraw-)T0`auto.ebs-detach.service`

[systemd](https://en.wikipedia.org/wiki/Systemd) 被配置为监控`/ebs`映射挂载目录，并在目录发生变化时调用`/etc/auto.ebs-detach.sh`。该脚本负责分离任何不再挂载的卷。`/usr/lib/systemd/system/auto.ebs-detach.service`配置`/etc/auto.ebs-detach.sh`为服务，`/usr/lib/systemd/system/auto.ebs-detach.path`配置 systemd 监控`/ebs`。

```
/
├── etc
│   └── auto.ebs-detach.sh
└── usr
    └── lib
        └── systemd
            └── system
                ├── auto.ebs-detach.path
                └── auto.ebs-detach.service 
```

Enter fullscreen mode Exit fullscreen mode

因为唯一可监控的可靠事件 <sup id="ref2">[2](#endnote2)</sup> 是对`/ebs`目录的更改，所以在每次调用中必须检查每个附加的卷。

```
#!/bin/bash
# ----------------------------------------------------------------------------
# /etc/auto.ebs-detach.sh
# ----------------------------------------------------------------------------
. /etc/aws.rc

# detach-volume-if-not-mounted key(volume)
detach-volume-if-not-mounted() {
    local key="$1"
    local instance="$(ec2-get-volume-attachment-instance ${key})"
    local state="$(ec2-get-volume-attachment-state ${key})"

    if [ "${instance}" == "${INSTANCE}" -a "${state}" == "attached" ]; then local device="$(ec2-get-volume-attachment-device ${key})"

        if [ "$(metadata block-device-mapping/ami)" != "${device}" ]; then local mntpt=$(lsblk -no MOUNTPOINT ${device})

            if [ "${mntpt}" == "" ]; then ec2-detach-volume ${key}
            fi
        fi
    fi
}

for volume in $(list-attached-volumes); do detach-volume-if-not-mounted ${volume}
done exit 0 
```

Enter fullscreen mode Exit fullscreen mode

`/usr/lib/systemd/system/auto.ebs-detach.service`将脚本定义为服务。

```
# /usr/lib/systemd/system/auto.ebs-detach.service [Unit]
Description=/etc/auto.ebs-detach.sh
After=autofs.service

[Service]
Type=oneshot
ExecStart=/etc/auto.ebs-detach.sh

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

`/usr/lib/systemd/system/auto.ebs-detach.path`配置`systemd`监控`/ebs`。

```
# /usr/lib/systemd/system/auto.ebs-detach.path [Unit]
Description=PathModified=/ebs/ /etc/auto.ebs-detach.sh

[Path]
PathModified=/ebs/

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

### 可变角色

[可负责的](https://www.ansible.com/)任务和处理程序配置了`autofs`图。

```
# tasks/main.yml
--------
- name: auto.ebs config files
  template:
    src: "{{  item.path  }}"
    dest: "/{{  item.path  }}"
    mode: "{{  item.mode  }}"
  with_items:
    - { path: etc/auto.master.d/ebs.autofs, mode: "0644" }
    - { path: etc/auto.ebs, mode: "0755" }
    - { path: etc/auto.ebs-detach.sh, mode: "0755" }
    - { path: usr/lib/systemd/system/auto.ebs-detach.service, mode: "0644" }
    - { path: usr/lib/systemd/system/auto.ebs-detach.path, mode: "0644" }
  notify: [ 'reload  systemd', 'restart  autofs' ]

- name: enable auto.ebs-detach.service
  service:
    name: "{{  item  }}"
    enabled: yes
    state: started
  with_items:
    - auto.ebs-detach.service
    - auto.ebs-detach.path
  notify: [ 'reload  systemd' ] 
```

Enter fullscreen mode Exit fullscreen mode

```
# handlers/main.yml
--------
- name: reload systemd
  systemd: daemon_reload=yes

- name: restart autofs
  service: name=autofs enabled=yes state=restarted 
```

Enter fullscreen mode Exit fullscreen mode

### SELinux 策略

如果安装并启用了安全增强的 Linux (SELinux ),则需要调整安全策略以允许脚本访问某些资源。

```
module local-automount 1.0;

require {
    type ldconfig_exec_t;
    type automount_t;
    type fixed_disk_device_t;
    class blk_file getattr;
    class file { execute execute_no_trans open read };
}

#============= automount_t ==============
allow automount_t fixed_disk_device_t:blk_file getattr;
allow automount_t ldconfig_exec_t:file { execute execute_no_trans open read }; 
```

Enter fullscreen mode Exit fullscreen mode

这些策略可以结合使用`checkmodule` / `semodule_package` / `semodule`命令进行安装。以下添加到[ansi ble](https://www.ansible.com/)T3。

```
- name: SELinux Policies
  set_fact:
    policies:
      - local-automount

- name: SELinux (*.te)
  template:
    src: "selinux/{{  item  }}.te"
    dest: "/etc/selinux/tmp/{{  item  }}.te"
  with_items:
    - "{{  policies  }}"
  register: te
  when: >
    ansible_selinux is defined
    and ansible_selinux != False
    and ansible_selinux.status == 'enabled'

- name: SELinux - checkmodule
  command: >
    chdir=/etc/selinux/tmp creates={{ item }}.mod
    checkmodule -M -m -o {{ item }}.mod {{ item }}.te
  with_items:
    - "{{  policies  }}"
  register: mod
  when: te.changed

- name: SELinux - semodule_package
  command: >
    chdir=/etc/selinux/tmp creates={{ item }}.pp
    semodule_package -o {{ item }}.pp -m {{ item }}.mod
  with_items:
    - "{{  policies  }}"
  register: pp
  when: mod.changed

- name: SELinux - semodule
  command: >
    chdir=/etc/selinux/tmp
    semodule -i {{ item }}.pp
  with_items:
    - "{{  policies  }}"
  when: pp.changed 
```

Enter fullscreen mode Exit fullscreen mode

开发 SELinux 策略将是未来条目的主题。

### [T1】T2/etc/AWS . RC](#etcawsrc)

`/etc/aws.rc`提供了上述脚本使用的通用 shell 函数。此外，`volume-mkfs`和`new-volume-mkfs` shell 函数演示了由`/ebs`图管理的文件系统卷的必要标记。

```
#!/bin/bash
# ----------------------------------------------------------------------------
# /etc/aws.rc
# ----------------------------------------------------------------------------
# Functions
# ----------------------------------------------------------------------------
# metadata data
metadata() {
    curl -s http://169.254.169.254/latest/meta-data/$1
}

export ZONE=$(metadata placement/availability-zone)
export REGION=$(echo ${ZONE} | sed 's/\(.*\)[a-z]/\1/')
export INSTANCE=$(metadata instance-id)

# ec2 command [argument ...]
ec2() {
    aws ec2 --region ${REGION} "$@"
}

# ec2-get-tag-value resource-id key
ec2-get-tag-value() {
    ec2 describe-tags \
        --filters Name=resource-id,Values="$1" Name=key,Values="$2" \
        --output text --query 'Tags[*].Value'
}

# ec2-set-tag-value resource-id key value
ec2-set-tag-value() {
    ec2 delete-tags --resources "$1" --tags Key="$2"
    ec2 create-tags --resources "$1" --tags Key="$2",Value="$3"
}

# ec2-get-volume-state volume-id
ec2-get-volume-state() {
    ec2 describe-volumes \
        --volume-ids "$1" \
        --output text --query 'Volumes[].State'
}

# ec2-get-volume-attachment-instance volume-id
ec2-get-volume-attachment-instance() {
    local value=$(ec2 describe-volumes \
                      --volume-ids "$1" \
                      --query 'Volumes[].Attachments[].InstanceId' \
                      --output text)

    echo ${value}
}

# ec2-get-volume-attachment-state volume-id
ec2-get-volume-attachment-state() {
    local value=$(ec2 describe-volumes \
                      --volume-ids "$1" \
                      --query 'Volumes[].Attachments[].State' \
                      --output text)

    echo ${value}
}

# ec2-get-volume-attachment-device volume-id
ec2-get-volume-attachment-device() {
    local value=$(ec2 describe-volumes \
                      --volume-ids "$1" \
                      --query 'Volumes[].Attachments[].Device' \
                      --output text)

    echo ${value}
}

# ec2-attach-volume volume-id device
ec2-attach-volume() {
    echo $(ec2 attach-volume \
               --instance-id ${INSTANCE} --volume-id "$1" --device "$2" \
               --output text) 1>&2
    ec2 wait volume-in-use --volume-ids "$1"

    while [ "$(ec2-get-volume-attachment-state $1)" != "attached" ]; do sleep 15
    done
}

# ec2-detach-volume volume-id
ec2-detach-volume() {
    echo $(ec2 detach-volume \
               --instance-id ${INSTANCE} --volume-id "$1" \
               --output text) 1>&2
    ec2 wait volume-available --volume-ids "$1"
}

# list-attached-volumes
list-attached-volumes() {
    local value=$(ec2 describe-instances \
                      --instance-ids ${INSTANCE} \
                      --output text \
                      --query 'Reservations[].Instances[].BlockDeviceMappings[].Ebs.VolumeId')

    echo ${value}
}

# next-unattached-block-device
next-unattached-block-device() {
    local attached=($(lsblk -ndo NAME))
    local available=($(echo -e ${attached[0]:0:-1}{a..z}\\n))

    for name in "${attached[@]}"; do available=(${available[@]//*${name}*})
    done echo /dev/${available[0]}
}

# volume-mkfs volume-id device fstype
volume-mkfs() {
    mkfs -t "$3" "$2"

    local uuid=""
    while [ -z "${uuid}" ]; do uuid=$(lsblk -no UUID ${device})
        sleep 10
    done ec2-set-tag-value "$1" fstype "$3"
    ec2-set-tag-value "$1" uuid ${uuid}
}

# new-volume-mkfs volume-type size fstype
new-volume-mkfs() {
    local volume=$(ec2 create-volume \
                       --availability-zone ${ZONE} \
                       --volume-type "$1" --size "$2" \
                       --output text --query 'VolumeId')

    ec2 wait volume-available --volume-ids ${volume}

    local device=$(next-unattached-block-device)

    ec2-attach-volume ${volume} ${device} 1>&2

    volume-mkfs ${volume} ${device} "$3" 1>&2

    ec2-detach-volume ${volume} 1>&2

    echo ${volume}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [来源](https://github.com/allen-ball/ball-ansible/tree/trunk/roles/auto.ebs)
*   [亚马逊网络服务(AWS)](https://aws.amazon.com/)
*   [AWS 弹性块存储(EBS)](https://aws.amazon.com/ebs/)
*   [AWS 弹性计算云(EC2)](https://aws.amazon.com/ec2/)
*   [可回答的](https://www.ansible.com/)

**【1】**本文末尾的 [`aws.rc`脚本](//#aws.rc)包含了用于将 EBS 卷分配、格式化和标记为文件系统的 shell 函数。 [↩](#ref1)

**【2】**作者研究了基于 [incrond(8)](https://linux.die.net/man/8/incrond) 和 [inotifywait(1)](https://linux.die.net/man/1/inotifywait) 的实现，但最终选择了`systemd`，因为它似乎是 Linux 发行版的最大公分母。 [↩](#ref2)