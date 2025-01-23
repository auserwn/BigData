# Doris



```
学习视频：【【尚硅谷】大数据Apache Doris教程（基于实际开发环境安装部署配置）】https://www.bilibili.com/video/BV15S4y1h7Kt?p=4&vd_source=f4fa55dcba9415699434901e9c870565

第二章 4-14未看部署
20250123 看到了18
```



之前叫百度Palo，2018年贡献到Apache社区，更名Doris

现代化的MPP分析型数据库产品

- 亚秒级响应，有效地支持实时数据分析。
- 架构简单方便运维，弹性扩容
- 支持10PB以上的超大数据集



## 第一章 Doris简介

### 1.1 Doris概述

MPP(Massively Parallel Processing)，即大规模并行处理。

其他MPP：ClickHouse GreenPlum

![](D:\mygitcode\BigData\image\doris_image\d1.png)

支持flink 

支持odbc支持外表

支持rollup预聚合

支持物化视图

### 1.2 Doris架构



![](image\doris_image\d2.png)

Doris 的架构很简洁，只设 FE(Frontend)、 BE(Backend)两种角色、两个进程，不依赖于外部组件，方便部署和运维，FE、BE都可线性扩展。

**FE(Frontend)**：存储、维护集群 元数据 ；负责接收、解析查询请求，规划查询计划，调度查询执行，返回查询结果。主要有三个角色：

- Leader 和 Follower：主要是用来达到元数据的高可用，保证单节点宕机的情况下元数据能够实时地在线恢复，而不影响整个服务。
- Observer：用来扩展查询节点 同时起到元数据备份的作用。 如果在发现集群压力非常大的情况下，需要去扩展整个查询的能力，那么可以加 observer 的节点。 observer 不参与任何的写入，只参与读取。



**Follower 和 Observer 的主要区别**：

| 特性           | **Follower**                                               | **Observer**                                                 |
| -------------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| **参与选举**   | 参与 Leader 的选举（Leader 挂掉后，可以成为新的 Leader）。 | 不参与 Leader 的选举，只是元数据的被动同步者。               |
| **元数据同步** | 与 Leader 实时同步元数据，确保一致性。                     | 从 Leader 获取元数据，但可能存在一定延迟。                   |
| **查询能力**   | 支持处理元数据查询和管理操作（例如 DDL、DML）。            | 支持查询，但仅作为只读节点，不允许执行管理操作（例如 DDL、DML）。 |
| **写操作**     | 可以执行写操作（DML 和 DDL）。                             | 仅用于读取操作，不支持写操作（DML 和 DDL）。                 |
| **用途**       | 用于高可用性，增加服务的可靠性。                           | 用于读扩展，减少主节点的查询压力。                           |



**BE(Backend)**：负责物理数据的存储和计算 ；依据 FE 生成的物理计划，分布式地执行查询。数据的可靠性由数据的可靠性由 BE 保证，BE 会对整个数据存储多副本或者是三副本。副本数可根据需求动态调整。



**MySQL Client**：Doris借助 MySQL协议，用户使用任意 MySQL的 ODBC/JDBC以及 MySQL的客户端，都可以直接访问 Doris。



**Broker**：为一个独立的无状态进程。封装了文件系统接口，提供 Doris读取远端存储系统中文件的能力，包括 HDFS S3 BOS等 。



## 第二章 编译与安装

安装Doris，需要先通过源码编译，主要有两种方式：使用 Docker开发镜像编译（推荐）、直接编译。

```
https://doris.apache.org/zh-CN/download
```



### 2.1 Docker安装





## 第三章 数据表的创建



### 3.1 创建用户和数据库



### 3.2 基本概念



#### 3.2.1 Row & Column

行列注意：

- 在默认的数据模型中， Column只分为排序列和非排序列。存储引擎会按照排序列对数据进行排序存储，并建立稀疏索引，以便在排序数据上进行快速查找。
- 而在聚合模型中， Column可以分为两大类： Key和 Value。从业务角度看， Key 和Value可以分别对应**维度列和指标列**。从聚合模型的角度来说， Key 列相同的行，会聚合成一行。其中 Value列的聚合方式由用户在建表时指定。



#### 3.2.2 Partition & Tablet

在Doris的存储引擎中，用户数据首先被划分成若干个分区（ Partition），划分的规则通常是按照用户指定的分区列进行范围划分，比如按时间划分。而在每个分区内，数据被进一步的按照 Hash的方式分桶，分桶的规则是要找用户指定的分桶列的值进行 Hash后分桶。**每个分桶就是一个数据分片（ Tablet）**，也是数据划分的**最小逻辑单元**。

- Tablet之间的数据是没有交集的，独立存储的。 **Tablet也是数据移动、复制等操作的最小物理存储单元。**
- **Partition可以视为是逻辑上最小的管理单元。**数据的导入与删除，都可以或仅能针对一个 Partition 进行。



### 3.3 建表



### 3.4 数据划分



#### 3.4.1 列定义

```
CREATE TABLE IF NOT EXISTS example_db.expamle_range_tbl
`user_id` LARGEINT NOT NULL COMMENT 用户 id",
`date` DATE NOT NULL COMMENT 数据灌入日期时间
`timestamp` DATETIME NOT NULL COMMENT 数据灌入的时间戳
`city` VARCHAR(20) COMMENT 用户所在城市
`age` SMALLINT COMMENT " 用户年龄
`sex` TINYINT COMMENT 用户性别
`last_visit_date` DATETIME REPLACE DEFAULT "1970 01 01
00:00:00" COMMENT " 用户最后一次访问时间
`cost` BIGINT SUM DEFAULT "0" COMMENT 用户总消费
`max_dwell_time` INT MAX DEFAULT "0" COMMENT 用户最大停 留时间
`min_dwell_time` INT MIN DEFAULT "99999" COMMENT 用户最小停留时间
ENGINE=olap
AGGREGATE KEY(`user_id`, `date`, `timestamp`, `city`, `age`, `sex`)
PARTITION BY RANGE(`date`)
PARTITION `p201701` VALUES LESS THAN ("2017 02 01"),
PARTITION `p20 1702` VALUES LESS THAN ("2017 03 01"),
PARTITION `p201703` VALUES LESS THAN ("2017 04 01")
DISTRIBUTED BY HASH(`user_id`) BUCKETS 16
PROPERTIES
"replication_num" = "
"storage_medium" = "
storage_cooldown_time" = "2018 01 01 12:00:00"
```

AGGREGATE KEY数据模型中，Key列和Value列。

比如AGGREGATE KEY(`user_id`, `date`, `timestamp`, `city`, `age`, `sex`) 为key列。

定义key列需要参照以下范式：

- Key 列必须在所有 Value列之前。
- 尽量选择整型类型。因为整型类型的计算和查找比较效率远高于字符串。
- 所有列的总字节长度（包括 Key和 Value）不能超过 100KB。



#### 3.4.2 分区与分桶

Doris支持两层的数据划分。第一层是 Partition，支持 Range和 List的划分方式。第二层是 Bucket(Tablet），仅支持 Hash的划分方式。也可以仅使用一层分区。使用一层分区时，只支持Bucket划分。



##### 3.4.2.1 Partition

- Partition列可以指定一列或多列。分区类必须为 KEY列。
- 不论分区列是什么类型，在写分区值时，都需要加 双引号 。
- 分区数量理论上没有上限。
- 当不使用 Partition建表时，系统会自动生成一个和表名同名的，全值范围的Partition。该 Partition对用户不可见，并且不可删改。

###### 1.Range分区

分区列通常为时间列，以方便的管理新旧数据。不可添加范围重叠的分区。

Partition指定范围的方式：

- VALUES LESS THAN (...) 仅指定上界，系统会将 前一个分区的上界作为该分区的下界 ，生成一个 左闭右开 的区间。 分区的删除不会改变已存在分区的范围。删除分区可能出现**空洞** 。
- VALUES [...) 指定同时指定上下界，生成一个左闭右开的区间。

###### 2.List分区

分区值为枚举值。只有当数据为目标分区枚举值其中之一时，才可以命中分区。不可添加范围重叠的分区。

```
p_cn: ( ("Beijing", "Shanghai", "Hong
p_usa: ("New York", "San Francisco")
p_jp: ("Tokyo")
```



##### 3.4.2.2 Bucket

常见语句如下：

```
CREATE TABLE test.table1 (
  `date` int(11) NULL COMMENT "日期",
  `media` varchar(255) NULL COMMENT "媒体",
  `suitable_type` varchar(255) NULL COMMENT "设备平台",
  `dau` int(11) NULL COMMENT "日活",
) ENGINE=OLAP
UNIQUE KEY(`date`, `media`, `suitable_type`)
COMMENT "测试表"
PARTITION BY RANGE(`date`)
(PARTITION p20241118 VALUES [("20241118"), ("20241119")),
PARTITION p20241119 VALUES [("20241119"), ("20241120")),
)
DISTRIBUTED BY HASH(`media`,`suitable_type`) BUCKETS 4
PROPERTIES (
"replication_num" = "3",
"dynamic_partition.enable" = "true",
"dynamic_partition.time_unit" = "DAY",
"dynamic_partition.time_zone" = "Asia/Shanghai",
"dynamic_partition.start" = "-90",
"dynamic_partition.end" = "1",
"dynamic_partition.prefix" = "p",
"dynamic_partition.replication_num" = "3",
"dynamic_partition.buckets" = "4",
"in_memory" = "false",
"storage_format" = "V1"
);
```

1.如果使用了 Partition，则 DISTRIBUTED ... 语句描述的是数据在各个分区内的划分规则。如果不使用 Partition，则描述的是对整个表的数据的划分规则。

2.分桶列可以是多列，但 必须为 Key 列 。分桶列可以和 Partition 列相同或不同。

3.分桶列的选择，是在 查询吞吐 和 查询并发 之间的一种权衡：

- 如果选择多个分桶列，则数据分布更均匀。但是如果一个查询条件不包含所有分桶列的等值条件，那么该查询会触发所有分桶同时扫描，这样查询的吞吐会增加，单个查询的延迟随之降低。这个方式适合大吞吐低并发的查询场景。
- 如果仅选择一个或少数分桶列，则对应的点查询可以**仅触发一个分桶扫描**。此时，当多个点查询并发时，这些查询有较大的概率分别触发不同的分桶扫描，各个查询之间的 IO影响较小（尤其当不同桶分布在不同磁盘上时 ），所以这种方式**适合高并发的点查询**场景。
- 举例，如果上述表DISTRIBUTED BY HASH(`media`,`suitable_type`)这样做了，但是查询条件为media=..，其实还是要扫描所有分桶。 如果上述表DISTRIBUTED BY HASH(`media`)，查询条件为media=..，则会触发点查。

##### 3.4.2.3 使用复合分区的场景

以下场景推荐使用复合分区：

- 有时间维度或类似带有有序值的维度，可以以这类维度列作为分区列。分区粒度可以根据导入频次、分区数据量等进行评估。
- 历史数据删除需求：如有删除历史数据的需求（比如仅保留最近 N 天的数据）。使用复合分区，可以通过删除历史分区来达到目的。也可以通过在指定分区内发送 DELETE 语句进行数据删除。
- 解决数据倾斜问题：每个分区可以单独指定分桶数量。如按天分区，当每天的数据量差异很大时，可以通过指定分区的分桶数，合理划分不同分区的数据 ,分桶列建议选择区分度大的列。



#### 3.4.3 PROPERTIES

主要是针对上述建表语句最后的properties

```
PROPERTIES (
"replication_num" = "3", --副本数，默认为3。由于半数机制，强烈建议为奇数。类似greenplum中副本的个数。
"dynamic_partition.enable" = "true",
"dynamic_partition.time_unit" = "DAY",
"dynamic_partition.time_zone" = "Asia/Shanghai",
"dynamic_partition.start" = "-90",
"dynamic_partition.end" = "1",
"dynamic_partition.prefix" = "p",
"dynamic_partition.replication_num" = "3",
"dynamic_partition.buckets" = "4",
"in_memory" = "false",
"storage_format" = "V1"
);
```

