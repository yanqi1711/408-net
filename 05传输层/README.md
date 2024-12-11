# 传输层

## 传输层提供的服务
<p align="center"><img src="./img/传输层.png" style="width:70%!important"></p>
一些特殊应用程序的端口号：FTP：20、21，TELNET：23，SMTP：25，DNS：53，TFTP：69，HTTP：80，SNMP：161

## UDP协议
### UDP数据报
UDP数据报理论最大长度是65535字节，但是实际应用中，由于IP数据报的限制，UDP数据报的长度不能超过65515字节，因为IP数据报的长度限制是65535字节，而IP数据报首部长度为20字节，所以UDP数据报的最大长度为65535-20=65515字节。

UDP数据报首部格式：源端口、目的端口、长度、检验和（均为2字节）

<p align="center"><img src="./img/UDP数据报.png" style="width:70%!important"></p>

### UDP检验
一种新的差错检验方法：将数据部分以16bit为一组进行二进制加和，中间结果如果最高位有进位就回卷加到最低位，得到结果逐位取反就是校验和<br>
检验：将接收到的内容以16bit为一组进行二进制加和，中间结果如果最高位有进位就回卷加到最低位，如果加法结果全1没有差错，如果不是全1有差错<br>

例子：
<p align="center"><img src="./img/UDP校验.png" style="width:70%!important"></p>
UDP检验（发送方）：
<p align="center"><img src="./img/UDP发送方.png" style="width:70%!important"></p>
UDP检验（接收方）：
<p align="center"><img src="./img/UDP接收方.png" style="width:70%!important"></p>
在IP数据报的首部有首部校验和，他只需要对IP数据报首部进行二进制加和（最高位进位回卷到最低位）、取反计算，不需要添加伪首部

## TCP协议
### TCP协议的框架梳理
TCP报文段（TCP段）：TCP首部+TCP数据<br>
由于TCP协议支持报文拆分、重装，因此应用层的报文较长也是可以的

建立连接（三次握手）是客服端发起连接，而释放连接（四次挥手）可以是客户端发起，也可以是服务器发起
<p align="center"><img src="./img/TCP三次握手.png" style="width:70%!important"></p>

TCP是面向字节流的，可以双向传输，TCP段的数据长度不超过MSS（最大段长）就行
<p align="center"><img src="./img/TCP连接.png" style="width:70%!important"></p>

### TCP报文段⭐️⭐️⭐️
<p align="center"><img src="./img/TCP.png" style="width:70%!important"></p>
最重要的6个字段：`序号（seq）`、`确认号（ack或ack_seq）`、`窗口（rwnd或rcvwnd）`、`ACK`、`SYN`、`FIN`<br>
序号（seq）：用于标记数据部分第一个字节在原始字节流中的位置（注意起始序号是发送发自己设的不一定从0开始）<br>
确认号（ack或ack_seq）：用于反馈，表示序号在该确认号之前的所有字节都已正确收到（累计确认，例如ack=2500表示2500序号前（不包括2500）所有字节都收到了）<br>
ACK：1bit，表示确认号（ack）字段是否有效，只有握手①的ACK=0，其他所有TCP报文段ACK=1<br>
窗口（rwnd或rcvwnd）：16bit，表示接收窗口的大小，即从本报文段首部中的确认号算起，接收方还有多大的缓冲区可以用于接收数据，是实现流量控制的关键（例如在接收方缓冲区有2500B，在接收了两个1000B的报文段后，缓冲区只有500B了，这时接受方给发送方返回的报文段就有rcvwnd=500这个信息）<br>
SYN和FIN：
<p align="center"><img src="./img/SYN和FIN.png" style="width:70%!important"></p>
其余字段：
<p align="center"><img src="./img/TCP报文段.png" style="width:70%!important"></p>
数据偏移：4bit，表示TCP首部长度，以4B为单位，因此TCP首部最大长度为60B（没有TCP数据部分长度的字段，`IP数据报总长度` - `IP数据报首部长度` - `TCP首部长度` = `TCP数据部分长度`）<br>
URG、PSH、RST、紧急指针：
<p align="center"><img src="./img/URG.png" style="width:70%!important"></p>
校验和：原理与UDP雷同，计算检验和之前也需要添加12B伪首部（只需将UDP伪首部的协议字段的17改成6，UDP长度字段改成TCP长度）<br>
选项字段本身不重要，但是可以用来协商MSS（最大段长）很重要<br>
<p align="center"><img src="./img/TCP选项.png" style="width:70%!important"></p>

### TCP连接管理（三次握手、四次挥手）⭐️⭐️⭐️
#### 建立连接
三次握手中握手①和握手②表示请求连接和同意请求，SYN为1（SYN段），同时虽然握手①和握手②不携带数据仍会消耗一个序号<br>
只有握手①的ack（确认号）无效，ACK=0<br>
起始序号由服务器自己决定<br>
<p align="center"><img src="./img/12.png" style="width:70%!important"></p>
握手③要重点懂得，当它不携带数据的时候不消耗序号，当它携带数据的时候消耗序号<br>
<p align="center"><img src="./img/3.png" style="width:70%!important"></p>
例题：
<p align="center"><img src="./img/TCP1.png" style="width:70%!important"></p>
状态转换图：// todo
<p align="center"><img src="./img/状态转换.png" style="width:70%!important"></p>
建立连接阶段耗时分析（客户端是从收到握手②才可以发送数据，服务端是收到握手③才可以发送数据）：
<p align="center"><img src="./img/建立连接耗时.png" style="width:70%!important"></p>

#### 释放连接
四次挥手中挥手①和挥手③的FIN为1（FIN段），同时虽然挥手①和挥手③即使不携带数据仍会消耗一个序号<br>
四次挥手中只有挥手④不可以携带数据<br>
<p align="center"><img src="./img/13.png" style="width:70%!important"></p>
状态转换图：// todo
<p align="center"><img src="./img/状态转换2.png" style="width:70%!important"></p>
例题：
<p align="center"><img src="./img/TCP2.png" style="width:70%!important"></p>
改题后：
<p align="center"><img src="./img/TCP3.png" style="width:70%!important"></p>
MSL（最长报文段寿命）：由TCP协议规定的一个固定时间长度（它的存在是因为挥手④可能为送达，导致服务端/客户端认为挥手③丢失，会重发挥手③报文）<br>
释放连接阶段耗时分析：
<p align="center"><img src="./img/释放连接耗时.png" style="width:70%!important"></p>
例题：
<p align="center"><img src="./img/TCP4.png" style="width:70%!important"></p>

### TCP可靠传输与流量控制（高频考点）
可靠传输：
<p align="center"><img src="./img/可靠传输.png" style="width:70%!important"></p>
流量控制：
<p align="center"><img src="./img/流量控制.png" style="width:70%!important"></p>
TCP传输的底层原理：缓冲区本质上是char数组
<p align="center"><img src="./img/TCP传输.png" style="width:70%!important"></p>
累计确认：（可以最多等0.5秒，服务端用一个报文段累计确认发送方传来的多个报文段）
<p align="center"><img src="./img/累计确认.png" style="width:70%!important"></p>
捎带确认：
<p align="center"><img src="./img/捎带确认.png" style="width:70%!important"></p>
超时重传机制：（超时重传计时器）
<p align="center"><img src="./img/超时重传.png" style="width:70%!important"></p>
由于超时重传机制可能会传回重复的报文段，所以有了另一种重传机制：快重传（配套机制：立即确认）<br>
<p align="center"><img src="./img/快重传.png" style="width:70%!important"></p>
<p align="center"><img src="./img/快重传机制.png" style="width:70%!important"></p>

### 拥塞控制
TCP拥塞控制与TCP流量控制的区别：
<p align="center"><img src="./img/拥塞控制与流量控制.png" style="width:70%!important"></p>
发送窗口的上限值=Min{rwnd，cwnd}（接收窗口、拥塞窗口）<br>
慢开始算法 -> cwnd到达阈值(ssthresh)后 -> 拥塞避免算法(+1) -> 发生超时重传 -> 慢开始算法，同时阈值降低 -> 慢开始算法的cwnd不能超过新的阈值 -> 拥塞避免算法 -> ...<br>
规则：（TCP规定ssethresg不能小于2）
<p align="center"><img src="./img/拥塞控制.png" style="width:70%!important"></p>
例题1：
<p align="center"><img src="./img/拥塞控制1.png" style="width:70%!important"></p>
例题2：
<p align="center"><img src="./img/拥塞控制2.png" style="width:70%!important"></p>

#### 快重传和快恢复
收到3个重复的ACK，导致快重传，说明有点拥塞，此时我们使用快恢复算法（即此时cwnd=ssthresh=cwnd/2）
<p align="center"><img src="./img/快恢复.png" style="width:70%!important"></p>
<p align="center"><img src="./img/快恢复2.png" style="width:70%!important"></p>
