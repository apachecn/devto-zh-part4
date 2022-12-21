# 使用 Ubuntu 服务器和 SSH over wifi 设置 Raspberry Pi 3 模型 B+

> 原文：<https://dev.to/hipsterdev/setup-raspberry-pi-3-model-b-with-ubuntu-server-and-ssh-over-wifi-4d41>

有些事情要先说:

*   在测试命令和写这篇文章的时候，我使用的是 ubuntu 18.04.3。
*   我没有以太网电缆`¯\_㋡_/¯`
*   我的树莓是 3 型号 B+，我的 sd 卡是三星 EVO Select microSDXC 128 GB
*   这是我在 dev.to community 上的第一篇帖子*(更像是一篇“共享笔记”)*🎉

1.  下载 ubuntu 镜像[18 . 04 . 3-预装-服务器-arm64+raspi3.img.xz](http://cdimage.ubuntu.com/ubuntu/releases/bionic/release/ubuntu-18.04.3-preinstalled-server-arm64+raspi3.img.xz) *(链接已找到[此处](https://wiki.ubuntu.com/ARM/RaspberryPi#Download) )*
2.  使用 [balenaEtcher](https://www.balena.io/etcher/) 刷新 sd 卡
3.  `sudo nano /media/wesley/writable/etc/netplan/50-cloud-init.yaml`
    它应该包含:

    ```
    network:
        version: 2
        ethernets:
            eth0:
                optional: true
                dhcp4: true
        wifis:
            wlan0:
                optional: true
                access-points:
                    "YOUR-SSID-NAME":
                        password: "YOUR-NETWORK-PASSWORD"
                dhcp4: true 
    ```

    > 确保已经导航到`writable`分区，否则`nano`会报错文件夹不存在。

4.  `sudo nano /media/wesley/writable/etc/cloud/cloud.cfg`

    应该是这样:

    ```
    # The top level settings are used as module
    # and system configuration.

    # A set of users which may be applied and/or used by various modules
    # when a 'default' entry is found it will reference the 'default_user'
    # from the distro configuration specified below
    users:
       - default
    # If this is set, 'root' will not be able to ssh in and they
    # will get a message to login instead as the default $user
    disable_root: false

    # This will cause the set+update hostname module to not operate (if true)
    preserve_hostname: false
    hostname: YOUR-RASPBERRYPI-NAME

    # Example datasource config
    # datasource:
    #    Ec2:
    #      metadata_urls: [ 'blah.com' ]
    #      timeout: 5 # (defaults to 50 seconds)
    #      max_wait: 10 # (defaults to 120 seconds)

    # The modules that run in the 'init' stage
    cloud_init_modules:
     - migrator
     - seed_random
     - bootcmd
     - write-files
     - growpart
     - resizefs
     - disk_setup
     - mounts
     - set_hostname
     - update_hostname
     - update_etc_hosts
     - ca-certs
     - rsyslog
     - users-groups
     - ssh

    # The modules that run in the 'config' stage
    cloud_config_modules:
    # Emit the cloud config ready event
    # this can be used by upstart jobs for 'start on cloud-config'.
     - emit_upstart
     - snap
     - snap_config  # DEPRECATED- Drop in version 18.2
     - ssh-import-id
     - locale
     - set-passwords
     - grub-dpkg
     - apt-pipelining
     - apt-configure
     - ubuntu-advantage
     - ntp
     - timezone
     - disable-ec2-metadata
     - runcmd
     - byobu

    # The modules that run in the 'final' stage
    cloud_final_modules:
     - snappy  # DEPRECATED- Drop in version 18.2
     - package-update-upgrade-install
     - fan
     - landscape
     - lxd
     - ubuntu-drivers
     - puppet
     - chef
     - mcollective
     - salt-minion
     - rightscale_userdata
     - scripts-vendor
     - scripts-per-once
     - scripts-per-boot
     - scripts-per-instance
     - scripts-user
     - ssh-authkey-fingerprints
     - keys-to-console
     - phone-home
     - final-message
     - power-state-change

    # System and/or distro specific settings
    # (not accessible to handlers/transforms)
    system_info:
       # This will affect which distro class gets used
       distro: ubuntu
       # Default user name + that default users groups (if added/used)
       default_user:
         name: pi
         lock_passwd: True
         gecos: Pi
         groups: [adm, audio, cdrom, dialout, dip, floppy, lxd, netdev, plugdev, sudo, video]
         sudo: ["ALL=(ALL)  NOPASSWD:ALL"]
         shell: /bin/bash
         ssh_authorized_keys:
           - "CONTENT-OF-YOUR-SSH-PUB-KEY-HERE"
       # Automatically discover the best ntp_client
       ntp_client: auto
       # Other config here will be given to the distro class and/or path classes
       paths:
          cloud_dir: /var/lib/cloud/
          templates_dir: /etc/cloud/templates/
          upstart_dir: /etc/init/
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://%(ec2_region)s.ec2.archive.ubuntu.com/ubuntu/
               - http://%(availability_zone)s.clouds.archive.ubuntu.com/ubuntu/
               - http://%(region)s.clouds.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [arm64, armel, armhf]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
           search:
             primary:
               - http://%(ec2_region)s.ec2.ports.ubuntu.com/ubuntu-ports/
               - http://%(availability_zone)s.clouds.ports.ubuntu.com/ubuntu-ports/
               - http://%(region)s.clouds.ports.ubuntu.com/ubuntu-ports/
             security: []
         - arches: [default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
       ssh_svcname: ssh 
    ```

5.  从电脑中弹出，将 SD 卡插入树莓，连接电源，等待大约一分钟。

6.  `ssh pi@YOUR-RASPBERRYPI-NAME`

> ℹ️第一次登录时，它会要求输入密码(键入`ubuntu`)，然后更改为新密码。

非常感谢 stackoverflow 上的用户。这篇帖子的步骤 90%来自他的回答[这里](https://askubuntu.com/a/1143594)。另外 10%来自[的云初始化文档](https://cloudinit.readthedocs.io/)