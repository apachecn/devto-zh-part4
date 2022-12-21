# Systemd 安全无用户指令

> 原文：<https://dev.to/fcfn/systemd-security-w-o-user-directive-220d>

因此，其单元文件没有`User`指令的每个 systemd 服务都作为根用户运行。这有什么安全隐患吗？我是否应该不使用用户指令将不受信任的代码作为服务运行，或者 systemd 是否有某种权限沙箱，即使这些进程以 root 用户身份运行？