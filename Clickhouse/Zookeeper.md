#### 一：Zookeeper 的工作机制

###### Zookeeper = 文件系统 + 通知机制

> 从设计模式角度来说理解：Zookeeper 是一个基于  **观察者模式** 设计的分布式服务框架，它负责 **存储和管理** 大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生了改变，Zookeeper 就负责通知已经在 Zookeeper 上注册的那些观察者做出相应的反应。

---

###### Zookeeper 的特点

- Zookeeper：由一个领导者（``leader``）和多个跟随者（``follower``）组成的集群；

- 集群中只有要 **半数以上** 的节点存活，Zookeeper 集群就能正常服务。所以 Zookeeper 适合安装 **奇数** 台服务器；

- 数据全局一致性：每个 Server 保存一份相同的数据副本，Client 连接到任意一个 Server，数据都是一致的；

- 更新请求顺序执行，来自同一个Client的更新请求按其 **发送顺序** 依次执行
- 数据更新原子性，一次数据更新要么成功，要么失败
- 实时性：在一定的时间范围内，Client 能读到最小数据

---

###### Zookeeper 的数据结构

> Zookeeper 数据模型结构与 Unix 文件系统类似，整体上可以看作是一棵树，每个节点称之为一个 ZNode。每个 ZNode 默认能够存储 **1MB** 的数据，每个 ZNode 都可以通过 **路径** 进行唯一标识

<img src="C:\Users\ld\Pictures\Saved Pictures\Zookeeper数据结构.PNG" style="zoom:75%;" />

---

###### Zookeeper 的应用场景

- 统一命名服务
- 统一配置管理
- 统一集群管理
- 服务器节点动态上下线
- 软负载均衡

----

###### Zookeeper 配置参数解读

- tickTime=2000

  > 通信心跳时间（**单次**），Zookeeper 服务器与客户端心跳时间，单位是毫秒

- dataDir=/var/zookeeper/data 

- dataLogDir=/var/zookeeper/log

- clientPort=2181

- initLimit=5

  > Leader 和 Follower 初始化（第一次建立通信连接）时的通信时限。总的连接时间不超过 **tickTime * initLimit**

- syncLimit=2

  > Leader 和 Follower 之间通信时间如果超过 **syncLimit * tickTime**,  Leader 几点则认为 Follower 节点挂掉，从而将该 Follower 从 服务列表中剔除

- maxClientCnxns=60

---

###### Zookeeper 集群配置模式

- 在 zoo.cfg 配置项dataDir 【我机器是 /opt/module/zookeeper-3.5.7/zkData】下新建myid 文件，并赋值一个数字

  > 【192.168.93.135】 cat /opt/module/zookeeper-3.5.7/zkData/myid 1 【192.168.93.135】
  >
  > 【192.168.93.136】 cat /opt/module/zookeeper-3.5.7/zkData/myid 2 【192.168.93.135】
  >
  > 【192.168.93.137】 cat /opt/module/zookeeper-3.5.7/zkData/myid 3 【192.168.93.135】

- server.A=B:C:D

  > A: 表示第几号服务器
  >
  > B: 服务器地址
  >
  > C: Leader服务器与Follower节点信息交换端口
  >
  > D: 原Leader挂时重新执行选举新leader时通信的端口

```
# 在zoo.cfg 末尾新增如下配置
server.1=192.168.93.135:2888:3888
server.2=192.168.93.136:2888:3888
server.3=192.168.93.137:2888:3888
```

---

###### Zookeeper选举机制

![Zookeeper选举机制](C:\Users\ld\Pictures\Saved Pictures\zookeeper选举机制.PNG)

1）当Zookeeper集群中的一台服务器出现以下两种情况之一时，就会开始进入leader 选举

> a：服务器初始化启动

> b：服务器运行期间无法和 Leader 保持连接

2）当一台机器进入Leader选举流程时，当前的集群可能会处于以下两种状态

> 集群中本来就已经存在一个Leader
>
> > 对于已经存在 leader 的情况，服务节点A试图选举 Leader 时，会被告知当前服务集群的Leader 信息。对于服务节点A机器来说，仅仅需要和 Leader 机器建立连接，并进行状态同步即可

> 集群中确实时不存在 Leader



---

###### Zookeeper 启动停止脚本编写

执行下面的脚本 ./zk.sh start 

```bash
# /bin/bash

case $1 in
"start") {
	for i in 192.168.93.135 192.168.93.136 192.168.93.137
	do
		echo --- zookeeper $i start ----
		ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh start" 
	done
}
;;
"stop") {
	for i in 192.168.93.135 192.168.93.136 192.168.93.137
	do 
		echo --- zookeeper $i stop ----
		ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh stop" 
	done
}
;;
"status") {
	for i in 192.168.93.135 192.168.93.136 192.168.93.137
	do 
		echo --- zookeeper $i status ---
		ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh status" 
	done
}
;;
esac
```

---

###### Zookeeper 客户端命令行

> ./zkCli.sh -server 192.168.93.135:2181

 ```sql
 [zk: 192.168.93.135:2181(CONNECTED) 2] ls / -s
 [zookeeper]cZxid = 0x0
 # znode 被创建的毫秒数
 ctime = Thu Jan 01 08:00:00 CST 1970
 # znoe 最后更新事务的 zxid
 mZxid = 0x0
 # znode 最后修改的毫秒数
 mtime = Thu Jan 01 08:00:00 CST 1970
 # znode 最后更新的子节点zxid
 pZxid = 0x0
 # znode 子节点变化号，znode子节点修改次数
 cversion = -1
 # znode 数据变化号
 dataVersion = 0
 # znode 访问控制列表的变化号
 aclVersion = 0
 # 临时节点，这是znode 拥有者的sessionid, 如果不是临时节点则是0
 ephemeralOwner = 0x0
 # znode的数据长度
 dataLength = 0
 # znode的子节点数量
 numChildren = 1
 
 
 ```

---

###### Zookeeper 节点类型

- 持久(persistent)：客户端与服务端 `断开连接` 后，创建的节点 **不删除**
  1. 持久化目录节点：客户端与Zookeeper断开连接后，该 **节点依旧存在**【eg：znode1】
  2. 持久化顺序编号目录节点：客户端与Zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号【eg：znode1_001】
  3. 备注：znode名称后面附加的顺序号是一个由父节点维护的单调递增的计数器。在分布式系统中，顺序号可以被用来未所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序
- 短暂(ephemeral)：客户端与服务端 `断开连接` 后，创建的节点 **自己删除**



----

###### Zookeeper 节点操作

1：创建持久节点

> 语法：createe nodeName "节点对应的值"
>
> 示例：create sanguo "diaochan"
>
> 查看：ls sanguo/
>
> 查值：get -s /sanguo

2：创建带顺序号的持久节点

> create -s  /sanguo/weiguo/zhangliao "zhangliang"

3：创建临时节点

> create -e /sanguo/wuguo "zhouyu"

4：修改节点值

> get /sanguo/weiguo ==> caocao
>
> set /sanguo/weiguo "simayi"
>
> get /sanguo/weiguo ==> simayi

5：删除节点

> delete /sanguo/wuguo/zhouyu
>
> deletea /sanguo/

----

###### Zookeeper 监听器原理

1：监听节点数据

> get -w /sanguo
>
> 备注：注册一次就只能监听一次，如果向再次监听，需要再次注册

2：监听节点数量增减

> ls -w /sanguo
>
> 备注：注册一次就只能监听一次，如果向再次监听，需要再次注册

![监听器原理](C:\Users\ld\Pictures\Saved Pictures\Zookeeper监听原理.PNG)

















---

[1.0 Zookeeper 教程](https://www.runoob.com/w3cnote/zookeeper-tutorial.html)

