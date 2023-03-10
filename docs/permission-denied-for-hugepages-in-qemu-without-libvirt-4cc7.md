# 对 QEMU 中没有 libvirt 的大型页面的权限被拒绝

> 原文：<https://dev.to/foursixnine/permission-denied-for-hugepages-in-qemu-without-libvirt-4cc7>

所以，假设您正在运行 qemu，并决定使用 hugepages，这很好，不是吗？有助于性能和材料，但是野生墙出现！

```
QEMU: qemu-system-aarch64: can't open backing store /dev/hugepages/ for guest RAM: Permission denied 
```

这基本上意味着你正在使用令人惊奇的 *-mem-path /dev/hugepages* ，而作为非特权用户运行的 QEMU 不能在那里写东西……这是它给我的印象:

```
sudo -u _openqa-worker qemu-system-aarch64 -device virtio-gpu-pci -m 4094 -machine virt,gic-version=host -cpu host \ 
  -mem-prealloc -mem-path /dev/hugepages -serial mon:stdio -enable-kvm -no-shutdown -vnc :102,share=force-shared \ 
  -cdrom openSUSE-Tumbleweed-DVD-aarch64-Snapshot20190607-Media.iso \ 
  -pflash flash0.img -pflash flash1.img -drive if=none,file=opensuse-Tumbleweed-aarch64-20190607-gnome-x11@aarch64.qcow2,id=hd0 \ 
  -device virtio-blk-device,drive=hd0 
```

机器试图启动，但最终我得到了那个可怕的信息。您可以简单地对目录执行一个 chmod，使用 udev 规则，然后离开它，它很快就完成了工作，但是使用 libvirt 解决这个问题的选项也很少，但是如果您没有使用 [hugeadm](https://linux.die.net/man/8/hugeadm) 来管理这些池并让操作系统来处理，很可能操作系统会为您处理这些，所以您可以使用`/usr/lib/systemd/system/dev-hugepages.mount`，因为我的一个同事尝试添加 udev 规则失败了，所以我决定使用 systemd 方法，最后如下所示

```
[Unit]
Description=Systemd service to fix hugepages + qemu ram problems.
After=dev-hugepages.mount

[Service]
Type=simple
ExecStart=/usr/bin/chmod o+w /dev/hugepages/

[Install]
WantedBy=multi-user.target 
```