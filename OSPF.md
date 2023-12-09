## OSPF

概述：动态路由协议，没有跳数限制，会计算最短路径。

Dijkstra algorithm

##### 三张表（table）

###### 邻居表

> dis ospf peer

###### 拓扑表

一个完整的拓扑信息

> dis ospf lsdb

###### 路由表

路由表可以通过最短路径算法（open shortest path fisrt）计算出最优路径

> dis ospf routing

dis ip routing-table

实验：

router id 1.1.1.1

ospf 110

area 0

network 10.0.12.1 0.0.0.255

##### 报文的类型

###### Hello

发现邻居，建立邻接关系

###### DBDs（database descriptor packets）

1. 选取Master， slave

2. 交换LSDB的summary（摘要）

###### LSRs（link state request）

请求发送缺失的LSA（LSDB里面的每一条记录叫LSA（link state advertisement））

###### LSA

type1： router

type2： network，link state ID是DR的ip

type3: 不同area的sum-net

type4：sum-asbr, 沟通连接自治系统的边界路由ASBR

type5：external 外部信息（比如引入rip）

type7: NSSA

###### LSU(link state update)

更新信息

##### LSAcks

确认

##### 路由器种类

![image20231207143709575](https://woniumd.oss-cn-hangzhou.aliyuncs.com/security/yangkaiming/20231207143716.png)

IR（internal router）

ABR(区域边界路由)

BR（骨干区域路由）

ASBR（自治系统边界路由） ： OSPF和其他区域

##### 区域类型：

###### 标准区域

1，2,3,4,5

###### 末梢区域（stub）

1，2，3

type5变成了一条默认路由0.0.0.0 -->外部区域，这条路由是type3

###### 完全末梢区域

1，2，【3】

type3变成了一条默认路由0.0.0.0 -->其他区域

###### 非完全末梢区域（NSSA）

1，2,3,7

带外部网络的末梢区域，跟末梢区域类似 产生type7

> 修正：type3 30， type 7 101

###### 完全NSSA

1，2，【3】,7

> 修正： type 7 101

###### 实验：

ospf 110

area 22

stub || stub no-summary

nssa || nssa no-summary

##### 建立连接：

###### 发现彼此

![image20231008145358625](https://woniumd.oss-cn-hangzhou.aliyuncs.com/security/yangkaiming/20231008145358.png)

down：

init

2-way

###### 了解彼此的缺陷

![image20231008150826414](https://woniumd.oss-cn-hangzhou.aliyuncs.com/security/yangkaiming/20231008150826.png)

EXSTART: Master/ Slave Election

EXCHANGE

###### 数据同步

![image20231008151146678](https://woniumd.oss-cn-hangzhou.aliyuncs.com/security/yangkaiming/20231008151146.png)

##### 区域内的通信过程

AR1： DR

AR2： BDR

AR3,AR4： OR（other router）

> AR3和AR4是不会彼此交换数据，只会彼此发现（停留在2-way阶段），

如果DR有更新，会发送组播报文，告诉组内的路由器更新了；（组播地址224.0.0.5）

> DR发送LSU->5，BDR发送LSAcks->5， OR发送LSAcks->6

---BDR更新类似

如果OR更新了，会告诉DR和BDR （组播地址224.0.0.6）

> OR发送LSU->6, DR发送LSU->5， BDR发送LSAcks->5
