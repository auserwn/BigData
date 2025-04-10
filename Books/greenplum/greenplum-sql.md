```sql
--插入测试数据
insert into "public".wn_test_stu(name,id)
select 'stu-'||n,n from generate_series(1,2000) n;
--查询执行计划
explain
select * from wn_test_stu order by id asc;

--查看测试表数据在segment节点分布
explain
SELECT gp_segment_id, count(1) FROM wn_test_stu 
GROUP BY gp_segment_id
order by gp_segment_id;

--查看节点信息
--status='d'表示segment失败，status='u'表示segment正常 
select * from gp_segment_configuration order by content,hostname,port;
  

在主节点上，想获取子节点的信息， 需要使用gp_dist_random. 
select gp_segment_id, count(1) from gp_dist_random('pg_class') group by 1 order by 1; 

select * from pg_filespace_entry;

explain analyze
select * from wn_test_stu 
where id::text like '%5';


---------对比分布模式

--hash分布
create table wn_test_stu_hash(
name varchar(20),
id int
)DISTRIBUTED BY(id);

insert into "public".wn_test_stu_hash(name,id)
select 'stu-'||n,n from generate_series(1,3000) n;

--random分布
create table wn_test_stu_random(
name varchar(20),
id int
)DISTRIBUTED RANDOMLY;

insert into "public".wn_test_stu_random(name,id)
select 'stu-'||n,n from generate_series(1,3000) n;

--查看对比
select hash.*,gp_segment_id from wn_test_stu_hash hash order by id;
select random.*,gp_segment_id from wn_test_stu_random random order by id;

SELECT gp_segment_id, count(1) FROM wn_test_stu_random 
GROUP BY gp_segment_id
order by gp_segment_id;

--查看节点信息
select * from gp_segment_configuration order by content,hostname,port;

---gp_segment_configuration解释
select 
dbid as "节点标识(1-master,primary-mirror-standby master)",
content as "数据库节点标识(primary=mirror)",
case role
when 'p' then 'p-primary'
when 'm' then 'm-mirror' 
else '未知' end as "节点当前的角色",
case preferred_role
when 'p' then 'p-primary'
when 'm' then 'm-mirror' 
else '未知' end as "节点被定义的角色",
case mode
when 's' then 's-已同步'
when 'n' then 'n-不同步' 
else '未知' end as "主备同步状态",
case status
when 'u' then 'u-节点正常'
when 'd' then 'd-节点失败' 
else '未知' end as "节点状态",
port as "端口",
hostname,address,datadir
from gp_segment_configuration order by content,hostname,port;

--节点间同步状态
select * from gp_stat_replication;

```

