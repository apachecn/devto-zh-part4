# Terraform:将 centos 虚拟机转变为 linux 路由器

> 原文：<https://dev.to/drewmullen/terraform-turn-a-centos-vm-into-a-linux-router-3pgf>

在构建您的虚拟机时，启用 IP 转发并通过这个远程执行

```
 provisioner "remote-exec" {
    inline = [
      "echo \"net.ipv4.ip_forward=1\" | sudo tee /etc/sysctl.d/ip_forward.conf",
      "sudo /usr/sbin/sysctl --system",
      "sudo systemctl start firewalld",
      "sudo systemctl enable firewalld",
      "sudo firewall-cmd --permanent --direct --passthrough ipv4 -I FORWARD -i eth0 -j ACCEPT",
      "sudo firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o eth0 -j MASQUERADE -s ${var.network["subnet"]}",
      "sudo firewall-cmd --reload",
    ] 
```

Enter fullscreen mode Exit fullscreen mode