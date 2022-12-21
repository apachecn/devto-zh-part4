# 📦检查字符串是否是 CIDR 表示法中的 IP 地址

> 原文：<https://dev.to/samandar/check-if-a-string-is-an-ip-address-in-cidr-notation-4oln>

```
from functorflow import f

# Check if input is a IPv4 or IPv6 CIDR address. Returns either 4, 6 (indicating the IP version) or 0 if the string is not a CIDR. 
print(f('cidr', '192.168.0.1/24')) # 4 print(f('cidr', '1:2:3:4:5:6:7:8/64')) # 6 print(f('cidr', '10.0.0.0')) # 0 
# Check if input is a IPv6 CIDR address. Returns a boolean. 
print(f('cidr.v6', '10.0.0.0/24')) # False 
# Check if input is a IPv4 CIDR address. Returns a boolean. 
print(f('cidr.v4', '10.0.0.0/24')) # True 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/cidr?lite=true](https://repl.it/@functorflow/cidr?lite=true)