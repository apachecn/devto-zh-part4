# 使用 PostgreSQL 处理 IP 地址

> 原文：<https://dev.to/spitsman/handling-ip-addresses-using-postgresql-2ke5>

PostgreSQL 提供了`inet`和`cidr`两种数据类型来存储网络地址并对其进行操作。

主机地址及其子网可以与`inet`一起存储，而`cidr`只能包含网络地址:

```
select inet '192.168.0.1/24';
      inet
----------------
 192.168.0.1/24 
```

```
select cidr '192.168.0.0/24'; -- valid cidr
      cidr
----------------
 192.168.0.0/24 
```

```
select cidr '192.168.0.1/24'; -- invalid: cidr must not be a host address
ERROR:  invalid cidr value: "192.168.0.1/24"
LINE 1: select cidr '192.168.0.1/24';
                    ^
DETAIL:  Value has bits set to right of mask. 
```

如果`cidr`地址中斜杠后没有数字，则网络掩码等于 32:

```
select cidr('127.0.0.1');
     cidr
--------------
 127.0.0.1/32 
```

上面的值代表一个子网地址，而传递给`inet`的相同值代表一个主机:

```
select inet('127.0.0.1');
   inet
-----------
 127.0.0.1 
```

可以用`>>=`和`<<=`运算符:
来检查包含或相等

```
select inet '192.168.0.1/24' >>= inet '192.168.0.0'; -- returns true
select cidr '192.168.0.0/24' >>= inet '192.168.0.0/12'; -- returns false
select cidr '192.168.0.0' >>= cidr '192.168.0.0'; -- returns true 
```

通过网络地址获取网络掩码可以通过`netmask` :
来实现

```
select netmask(inet('192.168.0.0/24')); -- returns 255.255.255.0
select netmask(cidr('127.0.0.1')); -- returns 255.255.255.255 
```