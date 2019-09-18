# mysql

快速，多线程，多用户关系型数据库，灵活，存储引擎和数据操作引擎分离。

**ACID**

* Atomicity（原子性）：一个事务（transaction）中的所有操作，或者全部完成，
  或者全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，
  会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
  即，事务不可分割、不可约简。

* Consistency（一致性）：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。
  这表示写入的资料必须完全符合所有的预设约束、触发器、级联回滚等。

* Isolation（隔离性）：数据库允许多个并发事务同时对其数据进行读写和修改的能力，
  隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。
  事务隔离分为不同级别，包括未提交读（Read uncommitted）、提交读（read committed）、
  可重复读（repeatable read）和串行化（Serializable）。

* Durability（持久性）：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

[特性](https://dev.mysql.com/doc/refman/5.7/en/features.html)

[架构](https://lalitvc.wordpress.com/2016/11/03/mysql-architecture-and-components/)

**系统管理**
[MySQL Server Administration](https://dev.mysql.com/doc/refman/5.7/en/server-administration.html)


**安全**
[Security](https://dev.mysql.com/doc/refman/5.7/en/security.html)

## sql教程

[教程链接](https://www.liaoxuefeng.com/wiki/1177760294764384/1179611020917408)


* DDL：Data Definition Language
  DDL允许用户定义数据，也就是创建表、删除表、修改表结构这些操作。通常，DDL由数据库管理员执行。

* DML：Data Manipulation Language
  DML为用户提供添加、删除、更新数据的能力，这些是应用程序对数据库的日常操作。

* DQL：Data Query Language 
  DQL允许用户查询数据，这也是通常最频繁的数据库日常操作。

**sql关键字不区分大小写**

**通常情况下，字段应该避免允许为NULL。不允许为NULL可以简化查询条件，加快查询速度，也利于应用程序读取数据后无需判断是否为NULL。**

### 关系模型

* 主键

  相当于每一个条记录的key，通过主键可以唯一的确定一条记录，因此主键不能重复，主键一般
  选择完全业务无关的字段。

* 联合主键

  使用多个字段的组合作为主键，这些字段的组合唯一地确定一条记录。

* 外键

  表A中的某一字段，关联到表B中的某一字段，则称该字段为表A的外键，通过外键可以实现一对一，
  一对多，多对一等数据表关系，外键可以空，外键通过添加外键约束创建。

* 索引

  使用hash对数据库某一列或者多列进行与排序，加快数据库查找，但是进行插入，删除，修改数据库
  时，同时也要修改索引，也就索引会提供读的性能，而牺牲写的性能。

* 唯一索引

  即hash后的值是唯一的，这样就要求该列的值不能有重复。唯一索引用于约束某一列的值不能
  重复。

### 查询数据

基本语法
select xxx from xxx where xxxx order by xxx group by xxx

* 逻辑条件

  not and or 

* 投影查找

  select column1, column2 from xxx where xxx;

* 排序

  order by

* 分页查找

  limit offset

* 聚合查询

  count sum avg max min

* 分组查询

  group by

* 多表查询

  select ... form A, B

  A表中的每一行和B表中的每一行的组合

* 连接查询

  inner join , left outer join, right outer join, full outer join

### 修改数据

insert update delete
