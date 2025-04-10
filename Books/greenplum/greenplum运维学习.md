数据库存储：

磁盘IO要求较高，数据基本没有缓存，文件系统选择上，linux下最好是XFS，使用`df -T`查看

一台服务器上CPU的数量决定了该机器上segment实例的数量

1. 硬件配置：Segment 实例数量应该与硬件资源相匹配。每个 Segment 实例通常需要至少 8GB 的内存和 2个 CPU 核心。因此，如果集群中有 8 个节点，每个节点有 64GB 内存和 16 个 CPU 核心，则可以将每个节点上的 Segment 实例数量设置为 8。
2. 数据量：Segment 实例数量也应考虑到要处理的数据量。如果数据量很大，可能需要增加 Segment 实例数量以提高性能。
3. 查询负载：查询负载也会影响 Segment 实例数量。如果集群使用复杂的查询，可能需要增加 Segment 实例数量以加快查询速度。

查看机器内存及cpu配置

查看cpu详细信息 cat /proc/cpuinfo

查看cpu简单信息lscpu | grep '^CPU.s'

查看内存配置free -h

实验室机器为16G 32核CPU 所以单节点上可以设置4的segment实例

服务器系统参数调整，一般需要修改以下三种类型参数 共享内存 网络  系统对用户的限制(用户占用资源限制)

查看磁盘使用情况iostat  或者 iostat -x 1 第二个命令最后一个参数表示使用率需要关注

或者iostat storagepool 1

查看网络

ifstat  或者 sar  或者 sar -n DEV 1 100

监控cpu 

mpstat -P ALL 1

监控内存

free 主要查看buffers/cache是否已经快没有了，如果是，说明内存吃紧了。还有就是查看swap是否已经使用。如果swap开始使用，说明操作系统内存已经不足



下载地址：

(https://network.pivotal.io/products/vmware-greenplum)`

create table as 与 select into 在临时分析数据时十分方便

时间函数中interval 

```sql
select (CURRENT_TIMESTAMP - a.query_start) as use_time,a.* from pg_stat_activity a
where CURRENT_TIMESTAMP - query_start > INTERVAL '1 min';
```

监控工具，需要安装Greenplum Performance Monitor

select oid,* from pg_class;
select 17014::regclass; --查看oid到底对应啥

pg/gp中，基本所有的表、表空间、索引、函数等都有自己的唯一标识oid，oid全局递增。如果知道oid可以使用上述sql查看对应

--节点失败时，主备节点切换记录
select * from gp_configuration_history;

--查看统计信息

select * from pg_statistic;
select * from pg_stats;

磁盘容量：使用不超过70% 记得在update delete和数据加载后进行VACUUM（空间回收）由于系统是MVCC架构

vacuum配置参数

max_fsm_relations = tables + indexs + system tables  数量

max_fsm_pages = 16*max_fsm_relations



数据库统计ANALYZE

以下操作执行结束后执行ANALYZE：数据装载、从备份中恢复、模式的改变-添加索引等、拆入更新删除数据

数据分布：

greenplum中数据分布情况直接影响到查询的效率，原因是greenplum架构是由性能最差的segment实例决定，所以数据分布不均匀会影响整个集群的性能。

检查数据的偏斜/倾斜情况：

SELECT * FROM gp_toolkit.gp_skew_coefficients WHERE skcrelname = 'td_gov_company_abnormal'; --查看skccoeff偏差系数
SELECT * FROM gp_toolkit.gp_skew_idle_fractions WHERE skcrelname = 'td_gov_company_abnormal';--查看空闲偏差系数：越小越好

重新平衡表数据：

如果表的分布键合理，直接重分布数据：alter table table_name set with(reorganize=true);

否则重新调整分布键再执行重分布：alter table table_name set distributed by (col1,col2...);



参数有三种类型：local global master-only

local -每台segment实例都有自己的独立参数，都设置才生效。所有修改local变量，需要在所有的segment上修改

global-master上设置，影响所以local

master-only-只在master上生效

设置本地化参数：

必须修改所有的postgresql.conf文件才能生效，命令 gpconfig -c gp_vmem_protect_limit -v 4096MB

然后重启greenplum数据库使修改的配置生效 gpstop -rfa -r防止提示f强制a免提示

查看系统参数

[gpadmin@gsgp60 bin]$ gpconfig -s shared_buffers
Values on all segments are consistent
GUC          : shared_buffers
Master  value: 4GB
Segment value: 4GB





gpcc学习：

【Greenplum集群监控：论一个合格DBA的自我修养】https://www.bilibili.com/video/BV1sJ411h7R2?vd_source=7c697f39b63eb04e938b11b9ddb12ae8



[root@gsgp60 ~]# postgres --version
bash: postgres: 未找到命令...

[root@gsgp60 ~]# yum install postgresql-contrib
已加载插件：fastestmirror, langpacks
Determining fastest mirrors

 * base: mirrors.bupt.edu.cn
 * extras: mirrors.bupt.edu.cn
 * updates: mirrors.jlu.edu.cn
base                                                                                                    | 3.6 kB  00:00:00     
extras                                                                                                  | 2.9 kB  00:00:00     
updates                                                                                                 | 2.9 kB  00:00:00     
updates/7/x86_64/primary_db                                                                             |  21 MB  00:00:02   
