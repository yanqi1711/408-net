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
