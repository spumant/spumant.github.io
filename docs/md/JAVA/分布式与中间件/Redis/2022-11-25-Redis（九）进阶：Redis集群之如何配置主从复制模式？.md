---
layout: post
cid: 379
title: Redis（九）进阶：Redis集群之如何配置主从复制模式？
slug: 379
date: 2022/11/25 20:53:23
updated: 2022/11/25 20:53:23
status: publish
author: 翕
categories: 
  - Redis
tags: 
customSummary: 
mathjax: auto
noThumbInfoEmoji: 
noThumbInfoStyle: default
outdatedNotice: no
parseWay: auto
reprint: standard
thumb: 
thumbChoice: default
thumbDesc: 
thumbSmall: 
thumbStyle: default
---


## 前言

默认情况下，每台Redis服务器都是主节点；  
由于个人服务器性能原因，以下的所有操作都是单机集群的概念！在实际工作中并不会这样配置，而是使用**哨兵模式**来监控！这篇文章的意义主要就是为了让大家了解主从复制这个概念！

## 一、概念

**主从复制**，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点(master/leader)，后者称为从节点(slave/follower)；数据的复制是单向的，只能由主节点到从节点。Master以写为主，Slave 以读为主。  
主要作用：  
①数据冗余：主从复制实现了数据的热备份，是持久化之外的一种**数据冗余**方式。  
②故障恢复：当主节点出现问题时，可以由从节点提供服务，实现**快速的故障恢复**；实际上是一种服务的冗余。  
③负载均衡：在主从复制的基础上，配合**读写分离**，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。  
④高可用（集群）基石：除了上述作用以外，**主从复制还是哨兵和集群能够实施的基础**，因此说主从复制是Redis高可用的基础。

## 二、环境配置（单机集群）

1、基本查看命令：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126142110768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)

```java
127.0.0.1:6379> ping  #测试是否连接成功！
PONG
127.0.0.1:6379> info replication  #查看当前redis信息
# Replication
role:master  #角色--主机
connected_slaves:0  #从机数量为0
master_replid:b9565cf2edea63b7e9860f3ef1a170d59ff7a4d4  #唯一标识的id
master_replid2:0000000000000000000000000000000000000000
#下面的这些咱不用管他是啥
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

2、开启三台服务：  
①复制三个配置文件：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126143632916.png)  
②修改以下配置：  
端口：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126143722422.png)  
pid名：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126143837459.png)  
log文件名：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012614390448.png)  
dump.rdb名：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012614393199.png)  
③全部启动并查看：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126144428690.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126144442564.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126144451166.png)  
查看所有Redis端口：证明启动成功啦！  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126144549129.png)

## 三、一主二从（单机测试）

1、认大哥大!!！  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126145631142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)

```java
127.0.0.1:6380> ping
PONG
127.0.0.1:6380> slaveof 127.0.0.1 6379  #让本机认6379的机器为大哥！
OK
127.0.0.1:6380> info replication  #查看信息
# Replication
role:slave  #从机
master_host:127.0.0.1  #主机ip
master_port:6379   #主机端口
master_link_status:up
master_last_io_seconds_ago:3
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
```

2、第二台机器同理，我们看看主机的信息：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126145902995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)

```java
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> info replication
# Replication
role:master  #主机
connected_slaves:2  #有两台从机
#从机的ip、端口等信息
slave0:ip=127.0.0.1,port=6380,state=online,offset=56,lag=1  
#从机的ip、端口等信息
slave1:ip=127.0.0.1,port=6381,state=online,offset=56,lag=1
```

出现以上内容，证明我们配置成功了！  
3、注意点：这种通过命令的配置是‘一次性的’，如果机器宕机、断电等，就需要重新认大哥大！  
在实际工作中，我们都是通过配置文件中修改指定配置的！如下图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126150653712.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
可以修改以上配置来实现主从机的配置！

4、测试读写操作：  
①主机写，从机读  
写：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126150841396.png)  
读：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012615091430.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126150943763.png)  
证明主从复制成功了！

②如果主机断开  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151029554.png)  
从机可以正常读数据：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151045629.png)  
查看从机信息：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012615111441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151127327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
证明，虽然主机断开了，但是从机还是可以正常读取原先就有的数据的！

③如果断开的主机重新连接上  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021012615123729.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
从机也可正常连接上主机，因为配置了，会自动寻找主机。

④如果从机断开重连呢？  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151429797.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151421980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151504275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
证明：如果从机断开重连，不会自动连接上主机！因为我们的配置是在从机上写的，而且是命令写的，重启时会重置！  
⑤从机能写嘛？  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126151640666.png)  
从机只能读，不能写！

5、复制原理：  
Slave 启动成功连接到 master 后会发送一个**sync**同步命令

Master 接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，并完成一次完全同步。

\*\*全量复制：\*\*而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

**增量复制：** Master 继续将新的所有收集到的修改命令依次传给slave，完成同步但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！ 我们的数据一定可以在从机中  
看到！

6、**层层链路**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126152003827.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
**这时候也可以完成我们的主从复制！**  
7、谋朝篡位  
如果主机断开了连接，我们可以使用

```java
 SLAVEOF no one
```

让自己变成主机！其他的节点就可以手动连接到最新的这个主节点（手动）！如果这个时候原来的老大修复了，那就重新连接成为小弟！！  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210126152227807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyOTQ0Mw==,size_16,color_FFFFFF,t_70)  
**注意哦！！**  
老大没挂，也可以使用这个命令直接让自己变成老大！

## 四、总结

一般来说，要将Redis运用于工程项目中，只使用一台Redis是万万不能的（宕机），原因如下：

1、从结构上，单个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大；

2、从容量上，单个Redis服务器内存容量有限，就算一Redis服务器内存容量为256G，也不能将所有内存用作Redis存储内存，一般来说，单台Redis最大使用内存不应该20G。

主从复制，读写分离！ 80% 的情况下都是在进行读操作！减缓服务器的压力！架构中经常使用！ 一主二从！

只要在公司中，主从复制就是必须要使用的，因为在真实的项目中不可能单机使用Redis！

Redis扩展：[Redis学习汇总](https://blog.csdn.net/weixin_43829443/article/details/112839985)

***路漫漫其修远兮，吾必将上下求索~***  
到此关于Redis的三大特殊类型的讲解就算告一段落了，如果你认为i博主写的不错！写作不易，请点赞、关注、评论给博主一个鼓励吧\*\*转载请注明出处哦\*\*