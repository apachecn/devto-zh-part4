# eBPF 和 XDP 以裸机速度处理数据包

> 原文：<https://dev.to/sematext/ebpf-and-xdp-for-processing-packets-at-bare-metal-speed-m5b>

# eBPF 和 XDP 以裸机速度处理数据包

XDP 或[快速数据路径](https://en.wikipedia.org/wiki/Express_Data_Path)的出现是由于 Linux 内核中对高性能数据包处理的迫切需求。几种内核旁路技术( [DPDK](https://www.dpdk.org/) 是最突出的一种)旨在通过将数据包处理转移到用户空间来加速网络运行。

这意味着抛弃由内核-用户空间边界之间的上下文切换、syscall 转换或 IRQ 请求引起的开销。操作系统将网络堆栈的控制权交给用户空间进程，这些进程通过自己的驱动程序直接与网卡交互。

尽管这种方法带来了显著的性能提升，但它也带来了一系列的缺点，包括在用户空间中重新发明 TCP/IP 堆栈以及其他网络功能，或者放弃了经过战斗考验的内核功能，这些功能为程序配备了强大的资源抽象和安全原语。

XDP 的任务是在内核中实现可编程的数据包处理，同时仍然保留网络堆栈的基本构件。事实上，XDP 代表了 eBPF 仪器能力的自然延伸。它采用了围绕地图、监督助手函数和沙盒字节码构建的编程模型，这些代码以安全的方式被检查并加载到内核中。

XDP 快速处理路径的关键点是字节码被附加在网络堆栈中尽可能早的点上，就在数据包到达网络适配器接收(RX)队列之后。在网络栈的这一阶段，还没有建立内核包特征，这有利于包处理路径中的巨大速度增益。

如果你错过了我之前关于 eBPF 要点的博文，我建议你先读一读。为了突出 XDP 在网络堆栈中的位置，让我们看看 TCP 数据包从到达网卡到到达用户空间的目的套接字的生命周期。请记住，这将是一个高层次的概述。我们将仅仅触及内核网络堆栈这个复杂巨兽的表面。

## 入口数据包流经网络堆栈

一旦网卡接收到一个帧(在应用了所有校验和以及健全性检查之后)，它将使用 DMA 将数据包传输到相应的内存区域。这意味着数据包直接从网卡的队列复制到驱动程序映射的主内存区域。当环形缓冲区接收队列的阈值达到时，NIC 发出一个硬 IRQ，CPU 将处理分派给 IRQ 向量表中的例程，以运行驱动程序代码。

由于驱动程序执行路径必须非常快，因此处理通过软 IRQ(NET _ RX _ SOFTIRQ)在驱动程序 IRQ 上下文之外延迟。假设 IRQ 在中断处理程序执行期间被禁用，内核倾向于在 IRQ 上下文之外调度长时间运行的任务，以避免丢失中断例程繁忙时可能发生的任何事件。设备的驱动程序启动 [NAPI](https://en.wikipedia.org/wiki/New_API) 循环，每个 cpu 的内核线程(ksoftirqd)消耗来自环形缓冲区的数据包。NAPI 循环的职责主要是触发软中断请求(NET_RX_SOFTIRQ ),由软中断请求处理程序处理，然后将数据发送到网络堆栈。

网络设备驱动程序分配了一个新的套接字缓冲区(sk_buff)来容纳入站数据包流。套接字缓冲区代表内核中抽象包缓冲/操作的基本数据结构。它还支撑着网络堆栈中的所有上层。

套接字缓冲区的结构有几个标识不同网络层的字段。在消耗来自 CPU 队列的缓冲区套接字之后，内核填充元数据，克隆 sk_buff 并将其推送到上游的后续网络层进行进一步处理。这是 IP 协议层在堆栈中注册的位置。IP 层执行一些基本的完整性检查，并将数据包交给 netfilter 钩子。如果数据包没有被 netfilter 丢弃，IP 层将检查高层协议，并将处理交给先前提取的协议的处理程序函数。

数据最终被复制到连接套接字的用户空间缓冲区。进程通过一系列阻塞系统调用(recv，read)或通过某种轮询机制(epoll)主动接收数据。

在 NIC 将数据包数据复制到 RX 队列后，XDP 挂钩立即被触发，此时我们可以有效地防止各种元数据结构(包括 sk_buffers)的分配。如果我们考虑最简单的可能用例，如高速网络中的包过滤或遭受 DDoS 攻击的节点，传统的网络防火墙(iptables)解决方案将不可避免地淹没机器，因为网络堆栈中的每个阶段都会引入大量的工作负载。

[![](img/39cce476089804ca49746d296c66135b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oYk7uZvu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/Lzi5bxJiaoikueEuaHATbPDuo-cRr4gsmNJkfAw17JFeDskVDlCjZhbMORRI6X-t5Xw9r-W2BiKLb1zngwJVPdwKHd2O42IdBmmvPCe8VUqQpOJaX9Yefxwqrz3EjXt9j4GtpqFc)

网络堆栈中的 XDP·胡克

具体来说，iptables 规则在它们自己的 softirq 任务中进行调度，但也按顺序进行评估，它们将在 IP 协议层进行匹配，以决定来自特定 IP 地址的数据包是否将被丢弃。相反，XDP 将直接对从 DMA 支持的环形缓冲区获得的原始以太网帧进行操作，因此丢弃逻辑可以提前发生，从而将内核从会导致网络堆栈延迟并最终导致资源匮乏的大量处理中解救出来。

## XDP 构念

正如您可能已经知道的，eBPF 字节码可以附加在各种战略点上，比如内核函数、套接字、跟踪点、cgroup 层次结构或用户空间符号。因此，每种类型的 eBPF 程序都在特定的上下文中运行 kprobes 情况下 CPU 寄存器的状态、套接字程序的套接字缓冲区等等。按照 XDP 的说法，生成的 eBPF 字节码的主干是围绕 XDP 元数据上下文(xdp_md)建模的。XDP 上下文包含访问原始数据包所需的所有数据。

为了更好地理解 XDP 计划的关键部分，让我们来剖析下面这一节:

```
#include <linux/bpf.h>

#define  SEC(NAME) __attribute__((section(NAME), used))

SEC("prog")

int xdp_drop(struct xdp_md *ctx) {

   return XDP_DROP;

}

char __license[] SEC("license") = "GPL"; 
```

这是最小的 XDP 程序，一旦连接到网络接口上，就会丢弃每个数据包。我们从导入 bpf 头开始，它引入了各种结构的定义，包括 [xdp_md](https://elixir.bootlin.com/linux/latest/source/include/uapi/linux/bpf.h#L2799) 结构。接下来，我们声明 SEC 宏，将映射、函数、许可证元数据和其他元素放在由 eBPF loader 自省的 ELF 部分中。

现在是我们的 XDP 程序中最相关的部分，处理数据包的处理逻辑。XDP 附带了一组预定义的判断，用于确定内核如何转移数据包流。例如，我们可以将数据包传递到常规网络堆栈，丢弃它，将数据包重定向到另一个网卡等等。在我们的例子中，XDP_DROP 产生一个超快速的数据包丢弃。还要注意，我们在函数中锚定了 eBPF loader 预计会遇到的 prog 部分(如果发现不同的部分名称，程序将无法加载，但是我们可以指示 ip 使用非标准的部分名称)。我们来编译一下上面的程序，试一试。

`$ clang -Wall -target bpf -c xdp-drop.c -o xdp-drop.o`

可以使用不同的用户空间工具(iproute2 套件的一部分)将二进制对象加载到内核中，tc 或 ip 是使用最广泛的工具。XDP 支持 veth(虚拟以太网)接口，所以要看到我们的程序运行的一个即时方法是把它卸载到一个现有的容器接口上。我们将旋转一个 nginx 容器，并在接口上附加 XDP 程序之前和之后启动几个 curls。第一次尝试卷曲 nginx 根上下文的结果是成功的 HTTP 状态代码:

```
$ curl --write-out '%{http_code}' -s --output /dev/null 172.17.0.4:80

200 
```

加载 XDP 字节码可以用下面的命令来完成:

`$ sudo ip link set dev veth74062a2 xdp obj xdp-drop.o`

我们应该在 veth 接口中看到 xdp 标志被激活:

```
veth74062a2@if16: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 xdp/id:37 qdisc noqueue master docker0 state UP group default  

  link/ether 0a:5e:36:21:9e:63 brd ff:ff:ff:ff:ff:ff link-netnsid 2

  inet6 fe80::85e:36ff:fe21:9e63/64 scope link  

     valid_lft forever preferred_lft forever 
```

后续的 curl 请求在失败之前会挂起一段时间，并显示如下错误消息，这有效地确认了 XDP 钩子正在按预期工作:

`curl: (7) Failed to connect to 172.17.0.4 port 80: No route to host`

当我们完成实验后，可以通过以下方式卸载 XDP 程序:

`$ sudo ip link set dev veth74062a2 xdp off`

## 在围棋中编程 XDP

前面的代码片段演示了一些基本概念，但为了利用 XDP 的超级能力，我们将使用 Go 语言制作一个稍微复杂一点的软件——一个围绕一种典型用例构建的小工具:为特定的黑名单 IP 地址丢弃数据包。完整的源代码以及如何构建该工具的说明可以在这里的[库](https://github.com/sematext/oxdpus)中找到。正如在[之前的博文](https://sematext.com/blog/linux-kernel-observability-ebpf/)中，我们利用了 [gobpf](https://github.com/iovisor/gobpf) 包，该包提供了与 eBPF VM 交互的支柱(将程序加载到内核中，访问/操作 eBPF 映射等等)。大量的 eBPF 程序类型可以直接用 C 语言编写并编译成 ELF 目标文件。不幸的是，基于 XDP ELF 的程序还没有被覆盖。作为一种选择，通过 BCC 模块附加 XDP 程序仍然是可能的，代价是处理 libbcc 依赖项。

然而，BCC 映射中有一个重要的限制，它阻止它们被固定在 bpffs 上(事实上，您可以从用户空间固定映射，但是在 BCC 模块的引导过程中，它很乐意忽略任何固定的对象)。我们的工具需要检查黑名单映射，而且在 XDP 程序附加到网络接口和主进程退出后，还需要能够添加/删除其中的元素。

这足以促使我们考虑在 ELF objects 中支持 XDP 程序，所以我们提交了 [pull 请求](https://github.com/iovisor/gobpf/pull/188),希望将其合并到上游回购中。我们认为这是一个有价值的增加，有利于 XDP 程序的可移植性，类似于内核探针如何分布在机器上，即使它们没有附带 clang、LLVM 和其他依赖项。

事不宜迟，让我们从 XDP [代码](https://github.com/sematext/oxdpus/blob/master/pkg/xdp/prog/xdp.c) :
开始浏览最重要的片段

```
SEC("xdp/xdp_ip_filter")

int xdp_ip_filter(struct xdp_md *ctx) {

   void *end = (void *)(long)ctx->data_end;

   void *data = (void *)(long)ctx->data;

   u32 ip_src;

   u64 offset;

   u16 eth_type;

   struct ethhdr *eth = data;

   offset = sizeof(*eth);

   if (data + offset > end) {

       return XDP_ABORTED;

   }

   eth_type = eth->h_proto;

   /* handle VLAN tagged packet */

 if (eth_type == htons(ETH_P_8021Q) || eth_type == htons(ETH_P_8021AD)) {

 struct vlan_hdr *vlan_hdr;

vlan_hdr = (void *)eth + offset;

offset += sizeof(*vlan_hdr);

if ((void *)eth + offset > end)

return false;

eth_type = vlan_hdr->h_vlan_encapsulated_proto;

}

   /* let's only handle IPv4 addresses */

   if (eth_type == ntohs(ETH_P_IPV6)) {

       return XDP_PASS;

   }

   struct iphdr *iph = data + offset;

   offset += sizeof(struct iphdr);

   /* make sure the bytes you want to read are within the packet's range before reading them */

   if (iph + 1 > end) {

       return XDP_ABORTED;

   }

   ip_src = iph->saddr;

   if (bpf_map_lookup_elem(&blacklist, &ip_src)) {

       return XDP_DROP;

   }

   return XDP_PASS;

} 
```

这可能看起来有点吓人，但例如，让我们忽略负责处理 VLAN 标记数据包的代码块。我们从访问 XDP 元数据上下文中的包数据开始，并将指针指向 ethddr 内核结构。您可能还会注意到检查数据包中字节边界的几种情况。如果忽略它们，验证器将拒绝加载 XDP 字节码。如果代码引用无效指针或违反安全策略，这将强制执行保证运行 XDP 程序而不会在内核中造成混乱的规则。代码的剩余部分从 IP 报头中提取源 IP 地址，并检查它是否出现在黑名单映射中。如果查找成功，数据包将被丢弃。

[钩子](https://github.com/sematext/oxdpus/blob/master/pkg/xdp/hook.go)结构负责在网络栈中附加/分离 XDP 程序。它从目标文件实例化并加载 XDP 模块，并调用 AttachXDP 或 RemoveXDP 方法。

IP 地址的[黑名单](https://github.com/sematext/oxdpus/blob/master/pkg/blacklist/map.go)通过标准 eBPF 映射来管理。我们调用 UpdateElement 和 DeleteElement 分别注册或删除条目。黑名单管理器还包含了一种在地图中列出可用 IP 地址的方法。

其余的代码将所有部分粘合在一起，提供了一个很好的 CLI 体验，用户可以利用它来执行 XDP 程序附加/删除和 IP 黑名单的操作。欲知详情，请前往[来源](https://github.com/sematext/oxdpus)。

## 结论

XDP 正逐渐成为 Linux 内核中快速数据包处理的标准。在这篇博文中，我解释了构成包处理生态系统的基本构件。尽管网络堆栈是一个复杂的主题，但是由于 eBPF/XDP 的可编程特性，创建 XDP 程序相对来说并不困难。