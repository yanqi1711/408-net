# 网络层

## 网络层的功能
异构网络互联：
- “异构”：每个网络的拓扑结构不同、物理层&链路层的实现不同、主机类型也各不相同
- 重要设备：`路由器（Router）`，在TCP/IP文献中也称为`网关（Gateway）`

路由与转发：
- 路由：`多个路由器间相互配合`，规划IP数据报（分组）的最佳转发路径（注：各个路由器之间运行`路由协议`，生成各自的`路由表`）
- 转发：`一台路由器`，根据自己的`转发表`，将收到的IP数据报（分组）从合适的接口转发出去（注：转发表=精简版路由表，有利于快速检索）

拥塞控制：
<p align="center"><img src="./img/拥塞控制.png" style="width:70%!important"></p>

**⚠️一些概念和错题知识点整理**<br>
- 在`路由器`互联的多个局域网的结构中，`要求每个局域网物理层、数据链路层、网络层协议可以不同`（连接IPv4或IPv6的网络），而`网络层以上的高层协议必须相同`
- 在因特网中，IP分组的传输需要经过源主机和中间路由器到达目的主机，通常`源主机和中间路由器都不知道IP分组到达目的主机需要经过的完整路径`
- `流量控制`和`拥塞控制`的区别：`流量控制是抑制发送方的的速率，拥塞控制是全局性的`，涉及网络中所有的主机

---

## IPv4
### IPv4分组
IP数据报（IP分组）的格式：
<p align="center"><img src="./img/IP分组格式.png" style="width:70%!important"></p>
`首部长度以4B为单位`，最大为60B<br>
`总长度以1B为单位`<br>

真题中会给图，首部格式不用记忆：
<p align="center"><img src="./img/首部格式.png" style="width:70%!important"></p>
`以太网MAC帧记忆口诀：6 6 2 N 4，收发协数验（N为46~1500）`<br>
重要概念：一个链路层数据帧能承载最大数据量称为最大传送单元`MTU`，以太网中为1500B<br>
如果`一个IP数据报的总长度超出了下一段链路的MTU，就需要分片`<br>

`标识`字段作用：如果数据接收方收到好几个标识相同的IP数据报分片，说明他们来自同一个数据报，接收方需要将他们重新组装成原来的数据报<br>

`标志`字段作用：`最低位MF（More Fragment）`、`次低位DF（Don't Fragment）`<br>
- MF=1，表示后面还有分片，MF=0表示最后一个分片
- DF=1，表示该数据报不允许分片，DF=0表示允许分片<br>
如果路由器想要将一个不允许分片的IP数据报发送给下一个节点，但是下一段链路的MTU小于该数据报的长度，那么路由器必须丢弃该数据报，向源主机发送一个`ICMP`差错报文，告诉源主机`数据报太长`<br>

IP数据报分片：
<p align="center"><img src="./img/IP数据报分片.png" style="width:70%!important"></p>
注意：`片偏移以8B为单位`
<p align="center"><img src="./img/IP数据报分片2.png" style="width:70%!important"></p>

`生存时间`：`TTL`，数据报在网络中可通过的路由器的最大值。每经过一个路由器，TTL减1，当TTL为0时，路由器丢弃该数据报，并向源主机发送一个`ICMP`差错报文，告诉源主机`TTL超时`<br>
`协议`：TCP、UDP等<br>
`首部校验和`：每个路由器仅校验首部，不校验数据部分，如果该字段设为全0，则表示不校验（方法与UDP相同）<br>

总结：
<p align="center"><img src="./img/IP数据报总结.png" style="width:70%!important"></p>

### IP地址
单播地址、多播地址：<br>
<p align="center"><img src="./img/单播地址.png" style="width:70%!important"></p>
上图记住A类地址、B类地址、C类地址他们的组成（网络号分别占8、16、24bit）<br>

32bit的IP地址组成：<网络号> + <主机号><br>

- 在那个年代，要求每台主机、每个路由器接口被分配的IP地址是全球唯一的
- 路由器和路由器连接的接口可以不分配IP地址，但路由器和其他节点的接口必须分配IP地址
- 从属于`同一个网络`的所有主机、路由器接口的IP地址`”网络号“都相同`
- 当一台`新主机接入网络时`，需要给它`分配一个IP地址`，并配置一个`默认网关`（让主机知道将数据传给互联网需要通过哪一个接口）

路由器的转发表：目的网络号和转发接口<br>
通过ARP协议知道同一个网络内的路由器、主机的IP地址对应的MAC地址<br>
在发送IP数据报的过程中，IP数据报的源地址和目的地址都是没有变的，但是它封装成帧后的帧MAC地址每一跳都会变<br>
从属于同一网络（网络号一致）的主机间的IP数据报的转发是不需要发给默认网关的<br>
王道的两个例子：转发给本网络，转发给其他网络
<p align="center"><img src="./img/IP数据报的转发.png" style="width:70%!important"></p>

一些特殊用途的IP地址：
网络号为Y，主机号全为0：表示一个网络本身，如192.168.1.0<br>
网络号为Y，主机号全为1：表示向网络号为Y的网络广播IP分组，只能用于目的地址，如192.168.1.255<br>
网络号全为0，主机号非全Y：表示本网络中的主机号为Y的主机，只能做源地址，如0.0.0.1<br>
网络号和主机号全为0：表示本网络上的本主机，只能做源地址，0.0.0.0（当主机刚接入网络中，没有IP地址时使用）<br>
网络号和主机号全为1：向本网络广播IP分组，只能用于目的地址，255.255.255.255<br>
网络号为127，主机号非全0或非全1：环回自检地址，127.0.0.1<br>
<p align="center"><img src="./img/特殊IP地址.png" style="width:70%!important"></p>

### 子网划分与子网掩码
<p align="center"><img src="./img/子网划分.png" style="width:70%!important"></p>
王道的几个训练：同一子网下的主机间、同一路由器下的不同子网间、使用子网划分技术和不使用子网划分技术的路由器下的主机间、主机给互联网发送数据报<br>
如果是同一子网下的主机间，主机对比源地址和目的地址与子网掩码相与的结果一致，就直接发送给目的主机，不需要经过默认网关
<p align="center"><img src="./img/同一子网下.png" style="width:70%!important"></p>
如果是不同子网下就需要转发给默认网关了，路由器根据目的地址与不同子网掩码相与的结果，转发给对应的接口
<p align="center"><img src="./img/不同子网下.png" style="width:70%!important"></p>
如果是主机给互联网发送数据报，使用默认路由0.0.0.0，一定可以匹配上
<p align="center"><img src="./img/主机给互联网.png" style="width:70%!important"></p>
200.1.1.2/24就是指地址为200.1.1.2，子网掩码为255.255.255.0的IP地址<br>

总结：
1. 主机发送IP数据报的过程：
<p id="a" align="center"><img src="./img/主机发送IP数据报.png" style="width:70%!important"></p>

2. 路由器转发IP数据报的过程
<p align="center"><img src="./img/路由器转发IP数据报.png" style="width:70%!important"></p>

### 无分类编址CIDR⭐️⭐️⭐️
<p align="center"><img src="./img/CIDR总结.png" style="width:70%!important"></p>
无分类编址CIDR（Classless Inter-Domain Routing）、变长子网划分的应用<br>
<p align="center"><img src="./img/CIDR.png" style="width:70%!important"></p>
<p align="center"><img src="./img/CIDR2.png" style="width:70%!important"></p>
定长子网划分例题：
<p align="center"><img src="./img/定长子网划分.png" style="width:70%!important"></p>
变长子网划分例题：
<p align="center"><img src="./img/变长子网划分.png" style="width:70%!important"></p>

### 路由聚合
路由聚合：
<p align="center"><img src="./img/路由聚合.png" style="width:70%!important"></p>
tips：如果在路由表中查找到的接口与数据帧发来的接口一致就不需要转发了，直接丢弃<br>
路由聚合的作用：减少路由表中的路由项，减少路由表所占用的空间，加快路由表查询速度<br>
使用路由聚合可能引入无效的地址，但并影响网络系统正常工作，但会带来第二个问题：可能使多个表项可匹配该地址，需要根据`最长前缀匹配原则`选择表项<br>
<p align="center"><img src="./img/最长前缀匹配原则.png" style="width:70%!important"></p>
采用`CIDR技术`和`路由聚合`后，主机发送IP数据报过程与<a href="#a">上面</a>相同<br>
而路由器转发一个IP数据报的过程为：
<p align="center"><img src="./img/查转发表.png" style="width:70%!important"></p>

### 网络地址转换NAT
网络层实现了`主机到主机`的通信。即网络层在IP数据报的首部，指明源主机和目的主机的IP地址<br>
传输层实现了`端到端`（进程到进程）的通信。即传输层在TCP（或UDP）报文段的首部，指明`源端口号和目的端口号`<br>
如何缓解IP地址不够用的问题？<br>
局域网共享一个公网IP地址，局域网内的主机通过`NAT`技术访问互联网<br>
NAT原理示意图：
<p align="center"><img src="./img/NAT原理.png" style="width:70%!important"></p>
总结要点：
<p align="center"><img src="./img/NAT.png" style="width:70%!important"></p>

### 地址解析协议ARP
训练：
<p align="center"><img src="./img/ARP训练.png" style="width:70%!important"></p>
ARP协议：
<p align="center"><img src="./img/ARP.png" style="width:70%!important"></p>

### 动态主机配置协议DHCP
DHCP是应用层协议，基于UDP，客户端口号68，服务器端口67<br>
在一个大型网络内运行多台DHCP服务器，但他们管理各自的IP地址池，客户端从任意一个DHCP服务器获取IP地址<br>
家用路由器兼职DHCP服务器，为局域网内的主机分配IP地址<br>
记住发现报文、提供报文、请求报文、确认报文的细节：
<p align="center"><img src="./img/DHCP.png" style="width:70%!important"></p>

**⚠️一些概念和错题知识点整理**<br>
- 把IP网络`划分成子网`，这样做的好处是`减少广播域的大小`
- `CIDR技术的作用是把小的网络汇聚成大的子网`
- NAT的表项需要管理员添加，收到`NAT表项中没有的分组直接丢弃`
- `ICMP报文`的传输方式是放在IP数据报的`数据字段`中传送

---

## IPv6（没出过题）
IPv6数据报格式：
<p align="center"><img src="./img/IPv6数据报格式.png" style="width:70%!important"></p>
注意红字就行了，其他了解：
<p align="center"><img src="./img/v4v6.png" style="width:70%!important"></p>
表示形式：
<p align="center"><img src="./img/IPv6表示形式.png" style="width:70%!important"></p>
IPv6基本地址类型：单播（一对一通信）、多播（一对多通信）、任播（一对多中的一个通信）<br>
过渡策略：双协议栈（同时开启v4和v6）、隧道技术（v6路由器 - v4路由器 - 互联网/xx - v4路由器 - v6路由器）<br>

---

## 路由算法和路由协议
直播P3

---

## IP组播
IP数据报三种传输方式：单播、组播（多播）、广播<br>
IP组播地址：
<p align="center"><img src="./img/IP组播地址.png" style="width:70%!important"></p>
硬件组播：
<p align="center"><img src="./img/硬件组播.png" style="width:70%!important"></p>
IGMP协议：1GMP只让连接到本地局域网上的组播路由器知道局域网上是否有主机参加或退出了某个组播组<br>
组播路由选择协议常用的三种算法：距离-向量路由选择算法、链路状态路由选择算法、协议无关组播（稀疏/密集）<br>

---

## 移动IP（没出过题）
移动IP概念：
<p align="center"><img src="./img/移动IP.png" style="width:70%!important"></p>
通信过程：
<p align="center"><img src="./img/移动IP通信过程.png" style="width:70%!important"></p>

**⚠️一些概念和错题知识点整理**<br>
- 移动IP为移动主机设置了两个IP地址，即主地址和辅地址（不同网段）
- 主机到了一个不属于他的网络中，所有的通信都要通过外部代理

---

## 网络层设备
<p align="center"><img src="./img/网络层设备.png" style="width:70%!important"></p>

**⚠️一些概念和错题知识点整理**<br>
- 如果一个存储转发设备实现了某个层次的功能，那么它就可以互联两个在该层次上使用不同协议的网段
- 分组转发直接查找转发表而不是路由表

---

## 路由算法
<p align="center"><img src="./img/路由算法知识总览.png" style="width:70%!important"></p>

路由算法和路由协议之间的关系
<p align="center"><img src="./img/路由协议与路由算法的关系.png" style="width:70%!important"></p>
路由协议会制定一种规则，度量路由器、网络间的"权值（代价）"<br>
当一台路由器转发IP数据报时，本质就是找到路由器到达目的网络的"最短路径"

路由算法的分类
<p align="center"><img src="./img/路由算法的分类.png" style="width:70%!important"></p>
距离向量路由算法考研真题：
<p align="center"><img src="./img/距离向量路由算法考研真题.png" style="width:70%!important"></p>
总结：
<p align="center"><img src="./img/路由算法总结.png" style="width:70%!important"></p>

---

## 分层次的路由协议
全世界数以亿计的路由器和网络，不肯能让每个路由器都了解所有网络信息所以需要分层次的路由协议
<p align="center"><img src="./img/分层次的路由协议.png" style="width:70%!important"></p>
- 内部只知道“自治系统内部的详细路由”
- 外部只知道“自治系统的整体路径”，不必知道内部细节

---

## RIP协议(routing information protocol, 路由信息协议)
RIP属于应用层协议，基于UDP，端口号520<br>
RIP的规定
<p align="center"><img src="./img/RIP的规定.png" style="width:70%!important"></p>
运行RIP协议的路由器是如何交换信息的
<p align="center"><img src="./img/RIP交换信息.png" style="width:70%!important"></p>

### RIP的工作示例（从启动到收敛）
<p align="center"><img src="./img/RIP工作过程.png" style="width:70%!important"></p>
0时刻
<p align="center"><img src="./img/RIP工作过程0时刻.png" style="width:70%!important"></p>
30时刻
<p align="center"><img src="./img/RIP工作过程30时刻.png" style="width:70%!important"></p>
60时刻
<p align="center"><img src="./img/RIP工作过程60时刻.png" style="width:70%!important"></p>

### RIP的工作示例（动态适应网络拓扑变化）
100秒时新增R1-R4链路
<p align="center"><img src="./img/RIP动态适应.png" style="width:70%!important"></p>

### RIP的优缺点
<p align="center"><img src="./img/RIP的优缺点.png" style="width:70%!important"></p>

---

## OSPF协议(open shortest path first, 开放最短路径优先)
OSPF在协议栈中所处的位置
<p align="center"><img src="./img/OSPF位置.png" style="width:70%!important"></p>

### OSPF协议的特点
<p align="center"><img src="./img/OSPF特点.png" style="width:70%!important"></p>

### OSPF工作原理
<p align="center"><img src="./img/OSPF工作原理.png" style="width:70%!important"></p>
几个混淆术语LSDB、LSA、LSI
<p align="center"><img src="./img/LSDB.png" style="width:70%!important"></p>
- LSI(Link State Information)：链路状态信息，指链路的状态和代价，广义的概念，指一切描述链路状态的信息
- LSA(Link State Advertisement)：链路状态通告，指路由器之间交换，即该路由器自身探测到的链路信息（逻辑上是一个链表）
- LSDB(Link State Database)：链路状态数据库，指路由器存储的所有路由器的链路信息

### OSPF分组类型
5种分组类型
<p align="center"><img src="./img/OSPF分组类型.png" style="width:70%!important"></p>
