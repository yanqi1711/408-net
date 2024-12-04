# 数据链路层

## 数据链路层的功能
<p align="center"><img src="./img/数据链路层的功能.png" style="width:70%!important"></p>

**⚠️一些概念和错题知识点整理**<br>
数据链路层协议的功能包括：`定义数据格式（组帧）`、`提供节点之间的可靠传输`、`控制对物理传输介质的访问`

---

## 组帧
`字符计数法、字节填充法、零比特填充法、违规编码法`
<p align="center"><img src="./img/组帧.png" style="width:70%!important"></p>

## 差错控制
### 检错编码（奇偶校验码）
奇校验：在信息为前添加1或0，使得整个信息的二进制表示中1的个数为奇数个<br>
偶校验（常用，使用模2加就可求得应该添加的校验位）：在信息为前添加1或0，使得整个信息的二进制表示中1的个数为偶数个<br>
模2加：信息位依次进行异或运算，所求结果就是偶校验位<br>
<p align="center"><img src="./img/奇偶校验.png" style="width:70%!important"></p>

### 检错编码（循环冗余校验码）
信息位K位、校验位R位
<p align="center"><img src="./img/循环冗余1.png" style="width:60%!important"></p>
<p align="center"><img src="./img/循环冗余2.png" style="width:60%!important"></p>
<p align="center"><img src="./img/循环冗余3.png" style="width:60%!important"></p>
<p align="center"><img src="./img/循环冗余校验码.png" style="width:70%!important"></p>

### 纠错编码（海明码）
<p align="center"><img src="./img/海明码.png" style="width:70%!important"></p>
分组偶校验的由来：每一个校验位都代表所有信息位每一位分组的权重：
<p align="center"><img src="./img/分组偶校验.png" style="width:70%!important"></p>
纠错（S由下标从大到小排序做成的数字为出错的海明码位）
<p align="center"><img src="./img/纠错.png" style="width:70%!important"></p>
信息位由下标从大到小或从小到大排序的不同做法：
<p align="center"><img src="./img/信息位排序.png" style="width:70%!important"></p>
`一位纠错能力`和`两位检错能力`无法区分需要添加全校验位进行偶校验（如果是两位错了需重传）：
<p align="center"><img src="./img/全校验位.png" style="width:70%!important"></p>

---

## 流量控制与可靠传输机制
### 流量控制、可靠传输与滑动窗口机制
<p align="center"><img src="./img/滑动窗口.png" style="width:70%!important"></p>

### 停止等待协议
