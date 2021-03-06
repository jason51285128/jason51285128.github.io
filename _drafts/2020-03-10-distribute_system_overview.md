## 2PC

需要一个协调者

1. prepare

    协调者向所有参与者发送prepare消息，携带事务信息，协调者等待参与者响应
    参与者再本地对事物进行与执行（写redo、undo日志），然后向协调者返还YES、No

1. commit

  协调者如果收到参与者的NO消息，或者消息超时，直接给每个参与者发送回滚消息，
  否则发送提交消息

  参与者执行提交或者回滚，并向协调者发送ACK

优点：

原理简单、方便实现

缺点：

1. 同步阻塞，commit阶段如果协调者宕机，将导致永久阻塞

1. 单点故障，协调者单点，没办法保证高可用，commit阶段如果协调者宕机，将导致永久阻塞

1. 数据不一致，第二阶段，如果发送网络故障，commit消息只到达一部分参与者，或者参与者再收到
  commit消息之前，宕机，一段时间后再恢复，

1. 没有容错机制：第一阶段，任意一个节点宕机，讲导致事务回滚

1. 事务黑洞：协调者（在第二阶段）发出commit消息之后宕机，而唯一接收到这条消息的参与者
    同时也宕机了。那么即使协调者通过选举协议产生了新的协调者，这条事务的状态也是不确定的，没人知道事务是否被已经提交


## paxos

前提

信道安全，消息不会被篡改，消息是完整的


角色

proposer: 提出提案

acceptor：对提案做出裁决

learners：学习提案结果



为了避免单点故障，会有一个Acceptor集合，Proposer向Acceptor集合发送提案，
Acceptor集合中的每个成员都有可能同意该提案且每个Acceptor只能批准一个提案，
只有当一半以上的成员同意了一个提案，就认为该提案被选定了。


prepare

promise

propose

accept


如果：
条件1： 一个acceptor只能接受一个value
条件2： 一个value如果被选中，必须被多数派accept

那么：
一次paxos，要么有且仅有一个value被选中，要么没有value被选中

反证法： 
假设一次paxos，被选中的值大于1一个，那么根据条件2，则至少
有一个acceptor接受了2个value，这和条件1矛盾


什么情况下选不出一个value？

acceptor 多数派宕机，一个集群中有多少几点就有多少acceptor，也就是说，如果集群有2N+1个
节点，当有N+1个节点宕机时，选不出一个value，这是集群不可用


除此之外，每次paxos有且仅有一个value被选中，这就保证了集群的一致性



acceptor： 只通过编号更大的提案


## zookeeper


