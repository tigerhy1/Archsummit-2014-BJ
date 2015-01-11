# Archsummit-2014-BJ

上交所的陈晨，干货比较多的分享。涉及了比较多分布式的东西

比较有意思的点：
             1. 定序层
                  第一个问题，是为什么要定序器？

之后蚂蚁金服的阮征分享了阿里的系统怎么做的资金转账的，
主要两种方式，一种是分布式事务，另一种是基于可靠消息的。而且是逐渐的转向基于可靠消息的方式。
 
听他的描述， 可以判断可靠消息和事件驱动模型基本是一个东西：两次输入的变更状态指令序列是一样的话，最后状态也会是一样的。
所以只要持久化变更状态的指令序列后，不管系统怎么crash，最后总能恢复到正确的状态。

在这里，上交所的，变更状态的指令序列，就是用户的下单序列。所以系统想记录下来所有用户的下单序列。

那么问题来了：在分布式的环境下，不同的机器同时接到订单，怎么判断先后顺序呢？
（我当时直接想到的是用时间戳，后来查到Cassandra用的是基于时间戳的方法，具体怎么做的没有仔细研究）
不同的机器必须对订单的顺序达成一致。提到著名的一致性算法paxos.

举例子

consistancy的问题：

定义：写成功后，是否马上能读到最新的数据。

强一致性，是写成功后，马上能读到最新数据
最终一致性，是写成功后，一段时间后能读到数据。

强一致性的代表：
 google chubby  （据说是唯一一个完全实现了paxos的项目）  
 yahoo zookeeper (hadoop子项目， 实现了简化版的zab，但据用过的人说500台左右的机器的时候，问题开始出现)
 之后的分享里面还会继续出现。
 
 还有raft算法

最终一致性的代表：
还有dynamo(amazon), 
cassandra(facebook, base on big table and dynamo), 
和akka cluster-(base on dynamo)这些的一致性是通过vector lock来做的

2. 集群锁（主从，怎么选取主节点）
    上交所利用的是OpenVMS提供的集群锁。在这里又提到zookeeper，zookeeper是干什么用的呢？
zookeeper做在HDFS里，主要做的事情是选取主节点，保证只有一个Namenode。在HBase里主要的作用也是选取主HMaster.
和这个地方的集群锁起到相同的作用。

    还有豌豆荚的redis集群解决方案里，是利用zookeeper来存储路由表的（redis instance id -> address）

3. 易拓展，高可用，高性能，三者间的平衡
                   著名的CAP理论，
              
4. 高可用性的保障方案（0丢失），原因应该是用了多层次的冗余.

分享我对高可用性的理解：
1.一个进程，或者一台机器down掉，数据会丢失吗？服务会中断吗？能自动恢复吗？
2.局部的service升级，需要停止服务吗？
3.加机器或者减机器，是否方便？是否需要停止服务？

相关资料：Paxos算法(zookeeper-hadoop-yahoo, chubby-google)，虚拟同步(isis-纽交所, dynamo-amazon, akka cluster-based on dynamo,  cassandra-based on big table and dynamo)




19号上午
 
首先是belly的CTO对移动设备上Web方式的推崇：
先是介绍了历史上新技术的发展，并举了很多很fancy的例子。由此引出的是在移动设备上Web的方式会逐渐取代原生。HTML5,和javascript是主角。
在他的分会场的演讲中，则较为细节的介绍了belly是怎么运用HTML5和javascript的
 
第二场，CA Tech的技术主管Mike Amundsen，用三个人的三个故事阐述了他的想法：冯诺依曼-self-replication（另一种基于2d grid的cpu的设计思路，自组织的机器人）, conway-the game of life（非常简单的三个规则，生成变化多端的结果）, Theo Jansen-strandbeest(定义简单的规则，让计算机去计算，演化出合适的结构).
 
分会场的演讲，将的是团队的结构和conway定理。
 
第三场，intel中国的技术主管吴甘沙，讲的是大数据的十个前沿方向。
先是介绍了不少硬件相关的东西：存储，flash, DRAM,NVRAM
当然基本上把大数据的个方面都大概有所涉及，  亮点是intel研发的安全云。
 
19号下午
 
上交所的陈晨，干货比较多的分享。
比较有意思的：1. 定序数据）器（Paxos算法，虚拟同步)）
              2. 集群锁（主从，怎么选取主节点）
              3. 易拓展，高可用，高性能，三者间的平衡
              4. 高可用性的保障方案（0丢失），原因应该是用了多层次的冗余，
 
分享我对高可用性的理解：
1. 一个进程，或者一台机器down掉，数据会丢失吗？服务会中断吗？能自动恢复吗？
2. 局部的service升级，需要停止服务吗？
3. 加机器或者减机器，是否方便？是否需要停止服务？
 
相关资料：Paxos算法(zookeeper-hadoop-yahoo, chubby-google)，虚拟同步(isis-纽交所, dynamo-amazon, akka cluster-based on dynamo,  cassandra-based on big table and dynamo)
 
想到豌豆荚分享的，他们redis集群怎么做的。他们否定redis官方的sentinel（我觉得应该是一个简化版的Paxos方法），而直接用单进程探测的方式，猜测是这个原因：1.觉得sentinel down掉一个进程太慢
 
NICE程钢的分享，比较坑。。
狂喷青云
 
 
 
 
维金的陆怡的分享，还是很赞的：比较多思想层面上的阐述。
1.传统金融系统和互联网金融系统的不同对比
2.借鉴现代会计学，用多频度，多层次的记账，和对账来保证资金的正确性。
 
对比传统的，比如上交所和互联网企业的不同地方。
 
 
 
 
 
 
 
 
 
蚂蚁金服（阿里集团）的阮征，最开始的分享有点跑题：
阿里用的资金正确性的保证方案有两个：
1. 分布式事务， 2. 可靠消息。而且从1慢慢偏向2的方案
然后才讲怎么解决客服的问题,因为阿里这方面的需求太大了，不能完全靠人工来解决。用一部分自然语言处理和人工智能来提供很大比例的客服服务。
 
 
李申申的分享，很赞：但可惜的是只听到后半部分。
从ppt上看到的内容总结：
1. Redis shading，是根据redis作者的一篇blog实现的
2. 日志系统，对比了scribe-facebook, kafka-linkedin-apache, flume-cloudera-apache,说明为什么自行开发kids. 从ppt的描述上看，是因为用java, scala这样的语言写的，而不用。
3. Event-deven arch, 运用消息队列（beanstalkd）
4. 为什么rpc框架从pb(改动后需要重新生成，我到认为没有太大的问题，可能因为团队人数多了之后的问题)到json再到avro-apache
5. 总体架构
6. AngularJS:代码复用，快速开发
7. 前后端分离，产品和设计师都可以改代码了。
8. 各种工具
9. 刺激技术人员的创新能力。
 
 
至此，第一天的会议结束。
 
 
 
 
 
 
 
 
20号上午：
 
第一场， 豌豆荚，刘琦，多机redis的方案。（不是正在准备做这个吗，看看豌豆荚的做法）
 
对redis cluster的看法：还没到prod阶段，太复杂
对twemproxy（业界现在用的很多的）的看法：不容易运维
 
自己开发的codius和twemproxy不同的地方：
1.按照单key迁移。
2.比twemproxy慢20%
3.集群外部，提供codius API,让client管理master-slave的切换。不用redis的sentinel.
 
 
最后一场：知道创宇，是讲安全方面的
 
比较有意思的点：
1. 用专门不设防的虚拟机做危险操作，让病毒和木马感染，从而拿到病毒和木马的特征。
2. 安全是关注协议之外的问题：例子用TCP/IP协议规定之外的行为，来获取系统的特征。
3. 安全方面的大数据架构
