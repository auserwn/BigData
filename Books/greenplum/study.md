##### 表分布策略

create table (a int,b...) distributed by (a);

create table (a int,b...) distributed randomly;

###### Hash-哈希分布：

要使用这一策略，需要在创建表使用 “DISTRIBUTED BY（column，[...]）” 子句。

散列算法使分布键将每一行分配给特定segment。相同值的键将始终散列到同一个segment。

column选取规则：

- 尽量选择唯一的分布键（例如Primary Key）将确保较均匀的数据分布。
- 分布键最好是经常使用作为关联的字段

哈希分布是表的默认分布策略。

###### 随机分布/循环分布：

要使用这一策略，需要在创建表使用 “DISTRIBUTED RANDOMLY” 子句。

随机分布会将数据行按到来顺序依次循环发送到各个segment上。与哈希分布策略不同，具有相同值的数据行不一定位于同一个segment上。虽然随机分布确保了数据的平均分布，但只要有可能，应该尽量选择哈希分布策略，哈希分布的性能更加优良。

###### 复制分布：

这种分布策略是GPDB 6的新增特性。

Greenplum数据分布和分区策略

要使用这一策略，需要在创建表使用 “DISTRIBUTED REPLICATED” 子句。

Greenplum数据库将每行数据分配到每个segment上。这种分布策略下，表数据将均匀分布，因为每个segment都存储着同样的数据行。适合于小表。



### **表的存储模式**

GPDB提供一系列的存储模式：堆存储Heap  和  只追加存储Append-only

堆存储适合数据经常变化的小表，比如维度表

只追加存储适合仓库中大表，批量装载数据进行只读查询操作，不支持update delete操作

缺省模式-堆表-create table (a int,b...) distributed by (a);

只追加模式 -create table (a int,b...) with(appendly=true);

### **SQL查询处理机制**

Master中的QD process : Query Dispatcher 查询分发器进程

Segment中QE process : Query Executer 查询执行进程

slice  QD会将执行

### **Motion**

由于数据是根据某种策略分散在集群中的各个节点上，那么进行查询时就会进行数据的移动，包括主节点和子节点 及 子节点与子节点间的数据移动。GP引入了Motion算子来实现数据在不同节点间的传输。

三种算子：

- Gather Motion
- Redistribute Motion
- Broadcast Motion

### **常用的并发技术：**

MVCC- Multi version COncurrency Control 多版本并发控制

S2PL-Strict two-phase Locking 严格的两阶段锁

OCC-optimistic Concurrency Control 乐观锁

MVCC特点

- 每个写操作会创建数据项目的新版本，同时保留旧版本
- 允许GP在读写的同时仍然提升并发特性
- 读操作不会阻塞写操作，写操作也不会阻塞读操作
- 





### **角色权限及客户端认真管理**

角色role = 用户user + 分组group

role是定义在GPDB系统级别的

初始化superuser role : gpadmin

创建role及修改role：

create role wn01 with login;

alter role wn01 with password 'asdfgg';

alter role wn01 set search_path to public,wn_test;

\h alter role ;  :查看帮助

创建Group Role

create role admin createrole createdb;

grant admin to wn01;

revoke admin from wn01;

grant all on TABLE mytable to admin;

grant all on SCHEMA wn_test to admin;

管理对象权限

grant 

不支持行列级别的权限控制

基于时间的登录认证

deny可以配置登录时间

配置客户端认证

show max_ 展示相关命令

exit 退出终端

#### 创建与管理数据库

数据库基于模板创建

缺省数据库模板为template1

GP系统内部使用 template0 postgres

克隆一个数据库：create database cp_wntest template wntest;

\h create database 	：查看帮助命令

#### 创建与管理模式：Schema

create schema wn_test authorization wn;   --指定owner 

show search_path;  查询搜索路径

alter database wn_test set search_path to sc01,public,pgadmin; --设置DB模式搜索路径

alter role dylan set search_path to sc01,public,pgadmin; --设置Role用户搜索路径

select current_schema();  --查看当前模式

drop schema wn_test cascade;  --删除模式及相关的所有对象

#### 创建表

create table products(

price numeric check(price>0),

name text not NULL, --非空约束

id integer unique, --唯一约束

id integer primary key, --主键约束

)

#### **表的压缩存储**

只支持Append-only表

两种压缩方式：表级别压缩 及 列级别压缩

选择压缩方式和级别的考虑因素：cpu性能	压缩比	压缩速度	解压速度和查询效率

创建压缩表：create table rb_zlib_01 (a int,b text) with (appendonly=true,compresstype=zlib,compresslevel=5); zlib有1-9可选，quicklz只有一种压缩级别

展示AppendOnly表的分布情况：get_ao_distribution(name/oid)

展示AO表的压缩率情况：get_ao_compression_ratio(name/oid)

#### **分区表**

对分区表接结构的修改需要通过 父表使用partition结合alter table命令完成

两种分区类型：range分区(日期 价格)   list分区(地区 产品)

决定表分区策略：表是否足够大-大表适合做分区、查询性能低于预期使用分区、查询条件匹配分区条件、数据均匀拆分

创建分区(通过start end every字句定义分区增量让GP自动产生分区)：

create table tb_cp_01(id int,date date...) distributed by (id) partition by range(date)

(start (date '2021-01-01') inclusive end (date '2023-01-01') exclusive every (intervel '1 day'));--inclusive 包含 exclusive 不包含

定义多级分区：



#### **索引**

GPDB中默认创建B-tree索引

##### BITMAP索引

create index bmidx_01 on tb_cp_02 using bitmap(date); --创建位图索引

show enable_seqscan;

##### 检查索引使用

在创建和更新索引之后运行analyze

使用真是数据测试，使用小的数据量测试是致命的错误

索引没有被使用时，必要情况下可以强制使用索引

重建索引 reindex



删除数据：truncate快于delete，原因是gp中delete会将表数据暂时存放在临时空间，临时空间可以清理也可以用于数据的回滚，而truncate不会将数据放在临时空间，速度较快。

##### **回收空间与分析**

回收空间-指的是：事务id管理，每个任务都对应一个事务，事务会有自己的id，事务不断增多，id会迅速上升，事务id会存在上限400多w，到达400多w会循环使用，会造成之前不可见事务变成可见。因此建议 每个数据库有200w事务时，对每张表执行VACUUM操作-即回收过期空间（系统目录维护，大量的create drop 导致系统表数据迅速增长，影响系统性能；MVCC事务并发模型，已经删除或者更新的记录会占据磁盘空间；大量更新删除，产生大量的过期记录。）

定期执行VACUUM命令可以删除过期记录： vacuum tb01;

vacuum full ;可以对所有表进行空间回收；

##### **日志文件** ： -数据库服务日志文件

master和每个segment都存在日志文件，随着数据库操作不断增加，日志文件默认按天滚动

服务器日志文件存在每个实例下边数据目录的pg_log目录下，文件格式：gpdb_yyyy-mm-dd_time.csv

程序(程序指-gpstart gpstop)日志文件，位于~/gpAdminLogs目录下

##### **查询**

关联子查询 ：GP中关联子查询有两种方式：

1.关联子查询可以被拆解为关联join操作

2.关联子查询为外部的查询每行执行一次

非关联子查询

日期函数

extract(daylmonthlyear from date '2013-01-01');

date '2013-01-01' + interval'1 day'

date pak('day',timestamp '2013-01-16 20:38:40');

date trunc('hour',timestamp '2013-02-16 20:38:40');

pg_ sleep(seconds);

##### **工作负载及资源管理**

##### **备份与恢复**

pg_dump 

pg_restore

【PostgreSQL数据备份与迁移的完美实践】https://www.bilibili.com/video/BV1Wa411a78B?vd_source=7c697f39b63eb04e938b11b9ddb12ae8

#### **外部表**

外部表数据来源于外部文件，而不是segment上数据库内容。

需要结合 并行文件分配程序 gpfdist 从外部表访问文件

也可以利用Hadoop分布式文件系统并行访问文件



#### **备份与恢复**

并行备份-gp_dump 

gp同时备份master和所以活动的segment实例

由于是并行的，所以备份消耗的时间与活动的segment数量无关系

master主机上备份所有的ddl文件和相关的数据字典表，每个segment备份各自的数据。所有备份文件组成一个完整的备份集合，通过唯一14位数字的时间戳来备份。

常规postgresql备份命令pg_dump和pg_dumpall        ( 注意！前边是gp_dump!!)

pg_dump不适合全部数据备份，适合小部分数据的迁移或备份（单表或者小表）

并行恢复-gp_restore

通过gp_dump产生的时间戳来辨识备份集合，恢复数据库对象和数据到分布式数据库，每个segment并行恢复各自的数据文件，被恢复的gp系统应该与备份的系统同构   （同构是segment数量一致）

非并行的实现异构系统恢复 -pg_restore

对应的它使用pg_dump和pg_dumpall  生成的备份文件进行恢复









p-35开始看

系统表会有

报错原因，低版本的pg_dump工具不能从高版本的server中导出数据，需要将pg_dump版本升级到对应版本或者更高版本







操作：

启动gp

su - gpadmin 

ps -ef | grep postgres

gpstate

连接: psql -u gpadmin

\du 	:dislpay user 查看有哪些用户

\? 	:查看帮助





unix 查看-zss文件系统

### 执行计划查看

```
https://explain.depesz.com/

https://explain.dalibo.com/

https://tatiyants.com/pev/#/plans/new

https://explain.tensor.ru/
```

