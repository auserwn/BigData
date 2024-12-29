# BigData_sql

## 学习文档

```SQL
背景：该文档针对的主要是hive IceBerg Doris等OLAP分布式数据库，不是OLTP查询语句。
```

## 1 

```SQL

```

## 2 lateral view

```SQL
SELECT  distinct_id
           ,get_json_object(params,'$.version') AS cur_ver_code
           ,get_json_object(params,'$.province')     AS province
           ,get_json_object(params,'$.city')         AS city
    FROM aa lateral view json_tuple
    (params, 'version', 'download_speed', 'download_duration', 'exp_id'
    ) AS version, download_speed, download_duration, exp_id
    WHERE date = ${date-1}
    
    lateral view json_tuple来将 params 列中的多个 JSON 字段展平成多个单独的列
```

## 3 UDF

```SQL
CREATE temporary function Fun1 AS 'com.wn.test.udf.Fun1';
--测试使用UDF 
WITH user_profile_tb AS
```

## 4 sql

```SQL
date_format(from_unixtime(unix_timestamp(CAST(int(date) AS STRING),'yyyyMMdd')),'yyyy-MM-dd') AS formatted_date

DATEDIFF(a.formatted_date, b.formatted_date) = 1 前者-后者

size(usage_dates) 函数求某个数组列或集合列中包含的元素个数

regexp_replace(book_month,'^0+(?!$)','')
regexp_replace(input_string, pattern, replacement)

NVL 函数是 SQL 中一个非常常用的函数，尤其是在处理 NULL 值时。它用于检查一个表达式是否为 NULL，如果是 NULL，则返回一个指定的替代值。否则，它返回该表达式的原始值。
NVL(expression1, replacement_value) 如果 expression1 的值为 NULL，则 NVL 函数返回replacement_value 如果不为 NULL，则返回原始值。

IF(condition, true_value, false_value)
condition返回的boolean如果为true则是第一个否则是第二个值

Cast(days_after_launch AS INT)
Cast(duration AS LONG)    
CAST('123.45' AS DECIMAL(10, 2)) 该数字总共允许 10 位数字（包括整数部分和小数部分），小数点后最多可以有两位数字

CEIL(expression) CEIL（或 CEILING）函数用于 返回大于或等于指定数字的最小整数。

GREATEST(value1, value2, ..., valueN) 返回一组给定值中的最大值。这个函数接受两个或多个参数，并返回其中最大的那个值。

ROUND(expression, decimal_places) decimal_places指定四舍五入后的位数。如果是正整数，则表示四舍五入到小数点后几位；如果是负整数，则表示四舍五入到小数点前几位。

get_json_object(params, '$.item_type')

concat(round(sd_pv*100/e_pv,2),'%')

COALESCE(expression1, expression2, ..., expressionN) 返回 第一个非 NULL 的值。如果所有传递的参数都是 NULL，则返回 NULL。

map的使用：
map(1, '是', 0, '不是')[is_commuter] as is_commuter,
map(0, '农村地区', 1, '普通小区', 2, '中等小区', 3, '高级小区', 4, '豪华小区')[house_level] as house_level        

DISTRIBUTE BY int(10 * rand())
distribute by cast(rand() * 5 as int)
类似于 Hive 中的 DISTRIBUTE BY，它确保数据在不同的节点/分区之间均匀分布。这有助于打乱数据的顺序，并进行更均匀的负载均衡，从而提高并行执行的效率。
常见用途：
优化并行度：当查询的数据量很大且不均匀时，使用 DISTRIBUTE BY 可以将数据均匀地分布到各个分区上，避免某些分区过载而其他分区空闲的情况。
数据打散：在某些情况下，可能希望打乱数据的顺序，减少热点数据的影响，或者消除某些特定模式的偏倚。


sum(if(ifnull(dd_pv,0) = 0, open_pv, null))


INSERT OVERWRITE TABLE new_table 
SELECT * FROM my_table 
DISTRIBUTE BY int(10 * rand());
DISTRIBUTE BY int(10 * rand()) 会使得 my_table 中的数据根据每行随机生成的整数（0 到 9）分布到新表 new_table 的不同分区或节点上。这样可以避免数据在某些分区上过于集中，优化分布式数据处理系统中的并行处理能力和负载均衡。



cache TABLE etl AS ();
select * from etl;
uncache TABLE etl;
```

## 5  Grouping sets

```SQL
GROUP BY  date
         ,a
         ,b
         ,c
GROUPING SETS(
    (date, a), 
    (date, a, b), 
    (date, a, c), 
    (date, a, b, c)
)

GROUPING SETS 会分别计算这四个分组集的聚合结果，而不是仅仅依赖单一的 GROUP BY 列组合.
在查询结果中，每一行 都会展示每个分组集合的聚合值。这样你就可以在同一个查询中获取多个不同粒度的聚合结果，而无需使用多个查询或 UNION。
```

与 `CUBE` 和 `ROLLUP` 的区别

**`CUBE`**：计算所有列的所有可能组合的聚合结果。`CUBE` 会为你自动生成所有列的所有子集的组合，计算每个组合的聚合。

```SQL
SELECT col1, col2, ..., aggregation_function(col3) 
FROM table_name
GROUP BY CUBE(col1, col2, ..., colN);

CUBE(col1, col2, ..., colN)：这里 CUBE 后面列出的所有列会进行多维组合，计算每一维度的不同层次的聚合。
```

**`ROLLUP`**：计算列的各个层次的聚合结果，通常是从详细级别开始，逐渐汇总到更高层次。例如，按 `A, B`、`A`、`全体` 等层次进行聚合。

```SQL
SELECT col1, col2, ..., aggregation_function(col3)
FROM table_name
GROUP BY ROLLUP(col1, col2, ..., colN);

ROLLUP(col1, col2, ..., colN)：ROLLUP 会依次按给定列组合进行分组，并计算每一层次的聚合结果。
```

**`GROUPING SETS`**：允许你显式指定哪些列的组合需要进行聚合，因此它提供了更多的灵活性，允许你定义自己需要的分组，而不像 `CUBE` 和 `ROLLUP` 那样自动生成。

## 6 Partition 窗口函数

```SQL
first_value(if(nvl(trim(install_source),'') = '',null,install_source),true) over (partition by a.did ,package_name ORDER BY server_time desc) 

lag(event_name) over(PARTITION by distinct_id,package_name ORDER BY time)
LAG(expression, offset, default) OVER (PARTITION BY partition_expression ORDER BY order_expression)
LAG 是一个 窗口函数，用于 访问当前行之前的某一行的值。它通常与 OVER 子句结合使用，可以在查询结果中按某个顺序（例如按时间）返回之前某行的数据。
expression：这是你想要获取前一行值的列或计算表达式。
offset：指定你希望访问的前几行的数据。默认为 1，表示前一行。如果指定为 2，则表示两行前的数据，依此类推。
default：可选参数。如果请求的行不存在（例如，数据集的第一行），则返回此默认值。如果省略，则默认返回 NULL。
```

## 7 索引

```SQL
SELECT  /*+ mapjoin(b) */ a.date 
这个提示告诉数据库优化器使用 mapjoin 策略来执行涉及表 b 的连接操作。
在 SparkSQL 中，MapJoin（也称为 Broadcast Join）是一种优化的连接操作，主要用于提高大数据处理过程中的效率。MapJoin 通过将较小的表广播到所有节点，以避免 Shuffle 操作，从而减少了连接操作的开销。数据库将该小表加载到内存中，然后在执行连接时直接通过内存中的数据进行连接，避免了磁盘 I/O 操作，从而提高查询性能。
```

## 8 sql优化

> 减少连续解析Json字段

```SQL
减少连续解析Json字段
```

## 9 优化案例

### 9.1 

以下单sql执行需要一分钟，单天数据量平均800w，查询较慢，怎么做？

```SQL

```