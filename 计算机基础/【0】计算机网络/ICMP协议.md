# ICMP协议

ICMP协议是一种面向无连接的协议，用于传输出错报告控制信息。它是一个非常重要的协议，它对于[网络安全](https://baike.baidu.com/item/网络安全)具有极其重要的意义。 [3] 它属于[网络层协议](https://baike.baidu.com/item/网络层协议)，主要用于在主机与路由器之间传递控制信息，包括报告错误、交换受限控制和状态信息等。当遇到IP数据无法访问目标、IP[路由器](https://baike.baidu.com/item/路由器)无法按当前的传输速率转发[数据包](https://baike.baidu.com/item/数据包)等情况时，会自动发送ICMP消息。

ICMP 是 TCP/IP 模型中网络层的重要成员，与 IP 协议、ARP 协议、RARP 协议及 IGMP 协议共同构成 TCP/IP 模型中的网络层。ping 和 tracert是两个常用网络管理命令，==ping 用来测试网络可达性，tracert 用来显示到达目的主机的路径。==ping和 tracert 都利用 ICMP 协议来实现网络功能，它们是把网络协议应用到日常网络管理的典型实例。 [1] 

从技术角度来说，ICMP就是一个“错误侦测与回报机制”，其目的就是让我们能够检测网路的连线状况﹐也能确保连线的准确性。当路由器在处理一个数据包的过程中发生了意外，可以通过ICMP向数据包的源端报告有关事件。

其功能主要有：侦测远端主机是否存在，建立及维护路由资料，重导资料传送路径（[ICMP重定向](https://baike.baidu.com/item/ICMP重定向)），资料[流量控制](https://baike.baidu.com/item/流量控制)。ICMP在沟通之中，主要是透过不同的[类别](https://baike.baidu.com/item/类别)(Type)与[代码](https://baike.baidu.com/item/代码)(Code) 让机器来识别不同的连线状况。

ICMP 是个非常有用的协议﹐尤其是当我们要对网路连接状况进行判断的时候。 [3] 