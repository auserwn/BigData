# spark  --3.3.1



windows下spark环境配置：

```

```



```
项目路径：D:\code\mySpark\mymi
```







spark是基于MR开发的

分布式计算：切分数据 减小数据规模 

![](image\spark_image\sparkjichu-2.png)

框架：写完jar包，需要submit提交到context进行执行。



![](image\spark_image\sparkjichu-1.png)





## 第一章 RDD 

RDD 弹性分布式数据集 Resilient Distributed DataSet

数据模型：

数据结构：采用特殊的结构组织和管理数据。比如数组 链表

RDD 一定是个对象，封装了大量的方法和属性，适合分布式处理

分布式集群中心化的架构：主从架构 master-slave架构 



![](image\spark_image\sparkcore-2.png)

是Driver端将数据和计算逻辑封装传递给Executor端，计算逻辑是在master端产生的。

其中 数据+计算逻辑 其实就是**Task**。

### 1.1 RDD的理解

RDD和字符串的区别：

- 字符串的操作是单点操作 RDD是分布式的，功能调用但不会马上执行。

多个RDD如何组合在一起实现复杂逻辑：RDD类似IO流 装饰者设计模式来实现功能的



![](image\spark_image\rdd1.png)

RDD适合分布式计算,存在多个分区-切片计算.



### 1.2 RDD的创建

RDD的创建方式有三种：集合中创建、外部创建、从其他RDD创建

```
// 从内存集合中创建
List<String> names = Arrays.asList("zhangsan", "lisi");
JavaRDD<String> parallelize = jsc.parallelize(names);

//从磁盘disk读取文件
JavaRDD<String> rdd = jsc.textFile("data/test.txt");
List<String> collect = rdd.collect();
collect.forEach(System.out::println);

//从其他rdd创建
```



#### 1.2.1 RDD的分区

以saveAsTextFile为例，输出保存的分区数和sparkConf.setMaster("local[*]");相关。

##### 1.2.1.1 内存读取数据的分区数

local中分区数量和环境的核数相关。

分区数量==切成几块推荐手动设置。parallelize可以传递参数numSlices代表切片分区的数量。

JavaRDD<String> rdd = jsc.parallelize(names);如果不传递则从sparkConf获取，如果不存在则取值为totalCores 当前环境的默认虚拟核数

```
SparkConf sparkConf = new SparkConf();
sparkConf.setMaster("local[*]");
sparkConf.setAppName("sparkrdd");
final JavaSparkContext jsc = new JavaSparkContext(sparkConf);

// 构建rdd处理模型 对接数据源
List<String> names = Arrays.asList("zhangsan", "lisi");
JavaRDD<String> rdd = jsc.parallelize(names);

rdd.saveAsTextFile("out");

jsc.close();

---------------------增加分区为3 
sparkConf.setMaster("local");
...
JavaRDD<String> rdd = jsc.parallelize(names,3);

----------！ spark在读取集合数据时，分区设定存在3种不同的场合
1.优先使用jsc.parallelize(names,3);方法参数
2.使用配置参数SparkConf sparkConf = new SparkConf();
3.使用默认参数值
```



##### 1.2.1.2 磁盘读取数据的分区数

文件数据源的分区设定也存在多个位置：

```
---!
1.textFile可以传递第二个参数
minPartitions = math.min(defaultParallelism,2)
JavaRDD<String> rdd = jsc.textFile("data/test.txt",minPartitions); 

2.textFile不传递 逻辑同上1.2.1.1
math.min(参数,2)

3.采用环境默认总核数
math.min(总核数,2)
```

文件为什么至少是2呢？  spark基于MR开发，文件的操作也是基于mr的库hdfs实现的，读取文件的切片数量不是由spark决定的，而是由hadoop实现的。

Hadoop的切片规则：

- totalsize：总字节数
- goalsize：总字节数/分区数
- part-num：totalsize/goalsize  想出剩余/goalsize>10% 则增加一个分区，否则不增加

spark存储数据的规则：需要对每个分区尽可能平均分配。算每个分区的start-end 代码中计算逻辑

spark是不支持文件操作的，文件操作都是由hadoop操作的，hadoop进行文件切片数量的计算和文件数据存储计算规则不一样：1.分区数量计算时，尽可能的平均，按照字节来计算的 2.分区数据的存储是考虑业务数据的完整性：按照行来进行读取。读取数据时要考虑数据的偏移量，从0开始的，相同的偏移量不重复读取。



### 1.3 RDD的转换算子

算子：operate

RDD的方法主要分为两大类：

- Transformation转换：可以将数据向下流转
- Action行动：可以控制数据的流转。行动算子是触发了整个作业的执行。因为转换算子都是懒加载，并不会立即执行。类似水龙头的开关，没有行动算子不会进行数据流转。

RDD处理数据的分类：

- 单值类型：
- 键值类型：KV类型

#### 1.3.1 map

map方法[映射转换]：

- map是将RDD的数据一条条处理，返回新的RDD

- map是对单值数据做处理：[1,2,3,4]->[2,4,6,8]

如果java中使用注解FunctionalInterface声明，那么接口的使用可以采用JDK提供的函数式编程语法-lambda实现

 ![](image\spark_image\sparkrdd-1.png)

![](image\spark_image\sparkrdd-2.png)

示例代码如下：

```
map的几种实现方法：

package com.atguigu.bigdata.rdd.suanzi;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.Function;
import java.util.Arrays;
import java.util.List;

public class Transform_Map {
    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);
        List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

        JavaRDD<Integer> rdd = jsc.parallelize(nums, 2);
//        JavaRDD<Object> newRdd = rdd.map(new Function<Integer, Object>() {
//            @Override
//            public Object call(Integer in) throws Exception {
//                return in * 2;
//            }
//        });

//        JavaRDD<Integer> newRdd = rdd.map(num -> num * 2);

        JavaRDD<Integer> newRdd = rdd.map(FunctionTest::nums2);
        newRdd.collect().forEach(System.out::println);

    }
}

class FunctionTest{

    public static int nums2(Integer num){
        return num*2;
    }
}
```



#### 1.3.2 filter

过滤：如果满足过滤规则，返回结果为True，则保留数据。否则过滤掉。

filter可能存在的问题：

- 不同分区过滤之后，剩余的数据差距较大-数据倾斜。

案例如下，其中FilterFunctions::myFun1为一个过滤方法。

```
SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        jsc.parallelize(Arrays.asList(1, 2, 3, 4, 5), 2)
                .filter(FilterFunctions::myFun1)
                .collect()
                .forEach(System.out::println);

        jsc.close();
```



#### 1.3.3 flatMap

flat扁平化 数据扁平化：把整体数据拆分成个体来使用的操作称为扁平化操作。

flatMap = 先flat+map

flatMap对RDD执行map操作,然后执行**解除嵌套**操作

```
# 嵌套的List
list = [[1,2,3],[4,5,6]]
# 解除嵌套
list = [1,2,3,4,5,6]
```

所以flatMap可以成为扁平映射，扁平+映射。

map不能扁平化映射，所以需要使用flatMap，比如每行按照空格拆分进行统计，这时候就需要使用flatMap。



#### 1.3.4 groupBy

将RDD的数据进行分组

rdd.groupBy(func)

```
rdd = sc.parallelize([('a',1),('a',11),('b',1)])
# 通过这个函数确认按照谁来分组(返回谁即可)
print(rdd.groupBy(lambda x:x[0]).collect())
print(rdd.groupBy(lambda x:x[0]).collect())
# 结果为:
```



```
-- 示例代码如下：
public class Transform_groupBy {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        List<Integer> nums = Arrays.asList(1, 2, 3, 4);
        JavaRDD<Integer> rdd = jsc.parallelize(nums);

        JavaPairRDD<Object, Iterable<Integer>> grdd = rdd.groupBy(new Function<Integer, Object>() {
            @Override
            public Object call(Integer num) throws Exception {
            	// 这里return的值实际上就是组的名称
                return "组别："+num;
            }
        });

        grdd.collect().forEach(System.out::println);
    }
}
-------------------------------------------
返回代码如下：
(组别：3,[3])
(组别：2,[2])
(组别：1,[1])
(组别：4,[4])



JavaPairRDD<Object, Iterable<Integer>> grdd = rdd.groupBy(new Function<Integer, Object>() {
            @Override
            public Object call(Integer num) throws Exception {
                int temp = num%2;
                if(temp==1){
                    return "奇数分组";
                }
                else{
                    return "偶数分组";
                }
            }
        });
返回如下:
(偶数分组,[2, 4])
(奇数分组,[1, 3])
```



默认情况下,数据处理后,所在的分区不发生变化,但是groupBy方法例外.Spark在数据处理时,要求同一个组的数据必须在同一个分区中.所以分组操作会将数据分区打乱重新组合.

![](image\spark_image\sparkrdd-3.png)

上述操作在spark中称为shuffle.

spark要求所有的数据必须分组后才能继续执行后续操作.

![](image\spark_image\sparkrdd-4.png)



#### 1.3.5 distinct 

去重 hashSet是单点去重 distinct是分布式去重

分布式去重采用分组+shuffle方式



#### 1.3.6 sortBy

sortBy按照指定的规则进行排序。需要三个参数

sortBy 有shuffle

```
JavaRDD<Integer> rdd_sort = rdd.sortBy(
                num -> num,true,2
        );
需要三个参数，第一个是排序规则，第二个t/f代表升序t还是降序f，第三个代表分区数量
排序规则：spark会为每个数据增加一个标记，按照标记进行排序

```



#### 1.3.7 KV类型

kv类型一般表示元组，scala中用()表示，java中为Tuple2

spark对整体类型的数据处理称为单值类型的数据处理

spark对KV数据类型的个体进行处理称为KV数据处理，KV分开，不是整体

```
Tuple2<String,Integer> a = new Tuple2("a",1);
```

示例代码如下：

##### 1.3.7.1 mapValues

mapValues方法只针对KV中的V进行处理，K不做任何操作

代码示例如下：

```
package com.atguigu.bigdata.rdd.suanzi;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import scala.Tuple2;

import java.util.Arrays;
import java.util.List;

/**
 * @author:auserwn
 * @create:--
 */
public class Transform_KV {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        Tuple2<String, Integer> a1 = new Tuple2<>("a", 1);
        Tuple2<String, Integer> a2 = new Tuple2<>("b", 2);
        Tuple2<String, Integer> a3 = new Tuple2<>("c", 3);

        List<Tuple2<String, Integer>> tuple2s = Arrays.asList(a1, a2, a3);

//        这不是KV类型处理，而是整体处理
//        JavaRDD<Tuple2<String, Integer>> rdd = jsc.parallelize(tuple2s);
//
//        JavaRDD<Tuple2<String, Integer>> rdd2 = rdd.map(
//                t -> new Tuple2<>(t._1, t._2 * 2)
//        );

        JavaPairRDD<String, Integer> rdd_kv = jsc.parallelizePairs(tuple2s);

        JavaPairRDD<String, Integer> rdd_mv = rdd_kv.mapValues(
                num -> num * 10
        );

        rdd_mv.collect().forEach(System.out::println);

        jsc.close();
    }
}

```



##### 1.3.7.2 mapToPair 单值类型和KV之间的转换

mapToPair 单值转换为KV类型

```
package com.atguigu.bigdata.rdd.suanzi;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import scala.Tuple2;

import java.util.Arrays;
import java.util.List;

/**
 * @author:auserwn
 * @create:--
 */
public class Transform_singleVToKV {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        List<Integer> tuple2s = Arrays.asList(1, 2, 3);

        JavaRDD<Integer> singleV_rdd = jsc.parallelize(tuple2s);

        JavaPairRDD<Integer, Integer> rdd_KV = singleV_rdd.mapToPair(
                num -> new Tuple2<Integer, Integer>(num, num*10)
        );

        rdd_KV.collect().forEach(System.out::println);

        jsc.close();
    }
}

```



##### 1.3.7.3 经典案例WordCount

示例代码如下：

```
package com.atguigu.bigdata.rdd.suanzi;

import com.atguigu.wc.WordCountBatchDemo;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import scala.Tuple2;

import java.util.Arrays;
import java.util.List;

/**
 * @author:auserwn
 * @create:--
 */
public class Transform_workCount {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        // 读取文件
        JavaRDD<String> rdd_wordline = jsc.textFile("data/word.txt");

        // 按照空格拆分
        JavaRDD<String> rdd_word = rdd_wordline.flatMap(line -> Arrays.asList(line.split(" ")).iterator());

        // 分组
        JavaPairRDD<String, Iterable<String>> rdd_group = rdd_word.groupBy(word -> word);

        // 分组内统计数量
        JavaPairRDD<String, Integer> rdd_count = rdd_group.mapValues(
                iter -> {
                    int i = 0;
                    for (String s : iter) {
                        i++;
                    }
                    return i;
                }
        );
        rdd_count.collect().forEach(System.out::println);
        jsc.close();
    }
}

```



##### 1.3.7.4 groupByKey

将kv类型的数据，按照key进行分组。

reduceByKey方法的作用：将kv类型的数据，按照相同的K对V进行操作：聚合成一个值。

基本思想，两两计算 1+2+3+4=3+3+4=6+4=10

示例代码如下

```
package com.atguigu.bigdata.rdd.suanzi;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import scala.Tuple2;

import java.util.Arrays;

/**
 * @author:auserwn
 * @create:--
 */
public class Transform_groupByKey {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        JavaPairRDD<String, Integer> rdd = jsc.parallelizePairs(
                Arrays.asList(
                        new Tuple2<>("a", 1),
                        new Tuple2<>("a", 3),
                        new Tuple2<>("b", 2),
                        new Tuple2<>("b", 4)
                )
        );

        JavaPairRDD<String, Iterable<Integer>> rdd_gbk = rdd.groupByKey();
        rdd_gbk.collect().forEach(System.out::println);
        jsc.close();
    }
}

```



##### 1.3.7.5 reduceByKey

reduceByKey 合二为一：分组+聚合

示例代码如下：

```
package com.atguigu.bigdata.rdd.suanzi;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.Function2;
import scala.Tuple2;

import java.util.Arrays;

/**
 * @author:auserwn
 * @create:--
 */
public class Transform_workCount_reduceByKey {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        JavaPairRDD<String, Integer> rdd = jsc.parallelizePairs(
                Arrays.asList(
                        new Tuple2<>("a", 1),
                        new Tuple2<>("a", 9),
                        new Tuple2<>("b", 5),
                        new Tuple2<>("b", 5)
                )
        );
//        JavaPairRDD<String, Integer> rdd_rbk = rdd.reduceByKey(
//                new Function2<Integer, Integer, Integer>() {
//                    @Override
//                    public Integer call(Integer i1, Integer i2) throws Exception {
//                        return i1*i2;
//                    }
//                }
//        );
        JavaPairRDD<String, Integer> rdd_rbk = rdd.reduceByKey(
                (i1,i2)->i1 * i2
        );
        rdd_rbk
                .collect().forEach(System.out::println);
        jsc.close();
    }
}

```



##### 1.3.7.6 sortByKey根据K排序

按照key进行排序，默认升序，传参数-true升序 false降序

sortByKey要求数据可以进行比较，实现接口implements Comparable

示例代码如下：

```
package com.atguigu.bigdata.rdd.suanzi;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.jetbrains.annotations.NotNull;
import scala.Serializable;
import scala.Tuple2;

import java.util.ArrayList;
import java.util.Arrays;

/**
 * @author:auserwn
 * @create:--
 */
public class Transform_sortByKey {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setMaster("local");
        conf.setAppName("spark");
        JavaSparkContext jsc = new JavaSparkContext(conf);

        User user1 = new User();
        user1.age=10;
        user1.amount=100;

        User user2 = new User();
        user2.age=20;
        user2.amount=200;

        User user3 = new User();
        user3.age=30;
        user3.amount=300;

        ArrayList<Tuple2<User,Integer>> users = new ArrayList<>();
        users.add(new Tuple2<User,Integer>(user1,1));
        users.add(new Tuple2<User,Integer>(user2,2));
        users.add(new Tuple2<User,Integer>(user3,3));

        JavaRDD<Tuple2<User, Integer>> rdd = jsc.parallelize(users);
        JavaPairRDD<User, Integer> rdd_pair = rdd.mapToPair(t -> t);
        JavaPairRDD<User, Integer> rdd_pair_sorybykey = rdd_pair.sortByKey(false);

        rdd_pair_sorybykey.collect().forEach(System.out::println);

        jsc.close();
    }
}

class User implements Serializable,Comparable<User> {
    public int age=0;
    public int amount = 0;

    @Override
    public String toString() {
        return "User{" +
                "age=" + age +
                ", amount=" + amount +
                '}';
    }

    @Override
    public int compareTo(@NotNull User other) {
        if(this.amount>other.amount){
            return 1;
        }else if (this.amount<other.amount){
            return -1;
        }else{
            return 0;
        }
    }
}

```



##### 1.3.7.6 sortByKey根据V排序

kv通过map互换

```
JavaRDD<Tuple2<User, Integer>> rdd = jsc.parallelize(users);
JavaPairRDD<User, Integer> rdd_pair = rdd.mapToPair(t -> t);
JavaPairRDD javaPairRDD = rdd_pair.mapToPair(
kv -> new Tuple2(kv._2, kv)
);
JavaPairRDD rdd_new_vsort = javaPairRDD.sortByKey();
```



##### 1.3.7.7 reduceByKey和groupByKey的区别

![](image\spark_image\rdd-5.png)

真正影响性能的其实是这里的shuffle 所以上下性能差距没那么大。如何优化shuffle呢？

shuffle慢是因为读写磁盘太慢了

- 硬件--提升硬件性能
- 资源--增加磁盘读写的缓冲区
- combine：在不影响结果的情况下，落盘数量越少，性能会快：在shuffle之前聚合-预聚合(分区内聚合)，降低落盘量。【实际开发中，如果遇到分组聚合的，考虑**预聚合**功能】



![](image\spark_image\rdd6.png)



#### 1.3.8 coalesce 改变分区

改变分区数量

```
// 将分区数量修改为3个
JavaRDD<Integer> coalesceRDD = fliterrdd.coalesce(3)
```

此方法默认没有shuffle功能，所以数据不会被打乱重新组合，所以如果要扩大分区是无法实现的，如果缩减可以。

如果从4->2是可以的，如果从2->4是不行的，但是不会报错。

```
// 将分区数量修改为3个
JavaRDD<Integer> coalesceRDD = fliterrdd.coalesce(3，true)
```

如果设置shuffle功能为true，则可以扩大分区。



repartition其实就是设定shuflle为true的coalesce方法。

```
fliterrdd.repartition(3)
```



通常，缩减分区使用coalesce，扩大分区使用repartition方法



### 1.4 RDD的行动算子

1.3中的转换算子，其实就是将旧的rdd通过方法转换成为一个新的RDD。使用到了**装饰者模式**。

行动算-触发作业Job的执行。转换算子是懒加载的，并不会立即执行。

行动算子和转换算子如何区分？

- 转换算子是为了组合功能转化为新的RDD。IN=RDD OUT=RDD则是转换算子。



#### 1.4.1 collect

collect是以数组的形式返回数据集。

```
rdd_map.collect().forEach(System.out::println);
rdd_map.collect().forEach(System.out::println);
如果写两次，就是触发两个作业。
```

collect方法就是将Executor端执行的结果按照分区的顺序拉取到Driver端。

![](image\spark_image\rdd-collect.png)

解释：

spark在写代码时，调用转换算子，并不会真正执行，只是在Driver端组合功能。

```
JavaRDD<Integer> rdd_map = rdd.map(
                num->{
                    System.out.println("****");
                    return num * 2;
                }
        );
这段代码中println操作是在Executor端执行的。
```

如果Driver端需要从hdfs读取文件进行处理，则Driver端向Executor端发送文件的切片信息。

Executor端可能会导致大量数据拉取到Driver端，所以生产环境**慎用**！

collect用于采集数据。

下边的count用于获取结果的数量。

#### 1.4.2 count

返回结果的数量。

```
long count = rdd_map.count();
```



#### 1.4.3 first

返回结果的第一个。

first其实底层就是take(1)。



#### 1.4.4 take

从结果中取(前)k个。



#### 1.4.5 countByKey

针对pair，结果按照key计算数量

示例代码如下：

```
package com.atguigu.bigdata.rdd.suanzi_action;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import scala.Tuple2;

import java.util.Arrays;
import java.util.List;
import java.util.Map;

/**
 * @author:auserwn
 * @create:--
 */
public class Action_countByKey {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setAppName("action_jsc");
        conf.setMaster("local[*]");

        JavaSparkContext jsc = new JavaSparkContext(conf);

        List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
        JavaRDD<Integer> rdd = jsc.parallelize(nums);

        JavaPairRDD<Integer, Integer> rdd_pair = rdd.mapToPair(
                num -> new Tuple2<>(num, num)
        );

        Map<Integer, Long> integerLongMap = rdd_pair.countByKey();

        System.out.println(integerLongMap);

        jsc.close();

    }
}

```



#### 1.4.6 save

save方法有好几个：

- saveAsTextFile：每个分区数据保存到一个文件
- saveAsObjectFile：序列化成为对象保存到文件



以下区分：

```
rdd_map.collect().forEach(System.out::println);
--对应区分以下
rdd_map.forEach(
	num->System.out.println(num)
);
--以上两种方式的区别：
collect将结果按照分区拉取到Driver形成集合，上者是单点循环-Driver 下者是分布式循环
下边的打印是在Executor，分区间是无续的，所以打印可能是乱序的。
下边是一个一个打印，上边是拉取成为集合后再打印，但是上边效率会更高，形成了集合一起打印。
```



#### 1.4.7 foreachPartition 

关联上边的区分，注意foreach和foreachPartition 的区别

foreachPartition 是指将分区数据全部给你，做操作

forEach是一条一条数据做操作，所以注意区分

forEach执行效率低，但是占用内存小，如果数据量过大foreachPartition 可能超出缓冲区，注意使用场景。



#### 1.4.8 foreach

反思以下代码：

```
package com.atguigu.bigdata.rdd.suanzi_action;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;

import java.util.Arrays;
import java.util.List;

/**
 * @author:auserwn
 * @create:--
 */
public class Action_forearch_fansi {

    public static void main(String[] args) {

        SparkConf conf = new SparkConf();
        conf.setAppName("action_jsc");
        conf.setMaster("local[*]");

        JavaSparkContext jsc = new JavaSparkContext(conf);

        List<Integer> nums = Arrays.asList(1, 2, 3, 4);
        JavaRDD<Integer> rdd = jsc.parallelize(nums,2);

        Student stu = new Student();

        rdd.foreach(
                num->{
                    System.out.println(stu.num+num);
                }
        );


        jsc.close();

    }
}

class Student{
    public int num = 10;
}
```

上述代码中foreach是在每个Executor端执行的，那么对象stu怎么办？

上述代码Student stu = new Student(); 中stu对象是在Driver端创建的，Executor端怎么使用呢？需要把Driver端对象通过网络传递到Executor端，否则无法使用。

这里传输的对象必须实现implements Serializable序列化接口。否则无法传递。



怎么区分哪些代码在Driver端执行还是在Executor端执行呢？：RDD算子的逻辑代码是在Executor端执行，其他代码都是在Driver端执行。

如果用到Driver端的东西，需要做序列化。



### 1.5 RDD的依赖

两个相邻的RDD之间为**依赖关系**。

连续的依赖关系称之为**血缘关系**。

spark中每个RDD都保存了依赖关系和血缘关系。



查看rdd的方法：rdd.toDebugString()  -前边(16)代表分区数 rdd记录了血缘关系

![](image\spark_image\rdd_dep_1.png)



宽依赖窄依赖：rdd.rdd()

- org.apache.spark.OneToOneDependency ：窄依赖
- org.apache.spark.ShuffleDependency ：宽依赖

在Driver端准备计算逻辑，但是执行计算是在Executor端。

RDD的依赖关系本质上不是RDD对象的关系，而是RDD对象中分区数据的关系。

- 窄依赖：如果计算上游的RDD的一个分区数据被下游RDD的一个分区所独享，成为窄依赖。
- 宽依赖：  ...多个...

因为**宽依赖**会将分区数据打乱重新组合，所以底层实现会存在**shuffle操作**



#### 1.5.1依赖关系 任务数量 阶段数量

作业Job：行动算子执行时，会触发作业的执行。ActiveJob。

阶段Stage：一个Job中RDD的计算流程，默认就是一个完整的流程。但是如果计算存在shuffle，那么流程一分为二。分开的每一段称为stage阶段。前一个阶段不执行完，后一个阶段不允许执行。阶段的数量和shuffle依赖有关系：阶段数=1+shuffle依赖的数量。

任务Task：每个Executor执行的计算。任务的数量其实就是每个阶段最后一个RDD分区的数量之和。

下图为例，此job拥有前后三个rdd，但是task数量为2+2，其中rdd2对应2个+rdd3对应2个。

![](image\spark_image\rdd7.png)





### 1.6 RDD的持久化

应用背景：有重用的RDD 怎么做

![](image\spark_image\rdd8.png)

但是上述操作注意RDD不保存数据，rdd只是保存了血缘和依赖，还是会出现计算重复，比如走了reduceByKey数据，数据走一遍，如果计算下边的groupByKey还得从头计算一遍，导致计算重复。所以解决方案是，对mapToPair的RDD结果进行保存。避免再从头计算一遍。所以只需要mapToPair.cache()。

![](image\spark_image\rdd9.png)

持久化方式：

- cache：
- presist：



## 第二章 sparkCore

sparkCore中RDD的方法称之为算子。

// JDK1.8之后存在元组，特殊的类：new Tuple1<>() tuple1中1代表元组中元素的个数

从33开始

## 第三章 sparkSQL



spark on Hive: 使用spark操作Hive中的表格，只作为存储元数据，spark负责sql的解析优化，语法是sparksql，spark底层采用优化后的df或者ds执行。

Hive on spark：使用Hive，只是将原来的MR转化为spark。Hive既作为存储元数据又负责SQL的解析优化，语法是hql语法，执行引擎变成了spark，spark负责RDD的执行。



发展历史：RDD(spark1.0) -> Dataframe(spark1.3) -> DataSet(spark1.6)

现在是DataSet(Row)



兼容Hive 可以通过JDBC或者ODBC来连接



### 3.1 SparkSession的新的起始点

老版本中提供两种sql查询的起始点

- 一个是SQLContext 用于spark自己提供的sql查询
- 一个是HiveContext，用于连接hive的查询

SparkSession是spark最新的sql查询起始点，实质上是SQLContext和HiveContext的组合。

spark中的SparkSession类似于JDBC中的Connection 连接对象。

创建方式如下两种：

```
1.
SparkSession sparkSql = SparkSession
                .builder()
                .master("local[*]")
                .appName("sparkSql")
                .getOrCreate();

2.
SparkConf conf = new SparkConf().setAppName("sparkSql").setMaster("local[*]");
SparkSession sparkSession = SparkSession.builder().config(conf).getOrCreate();
```

结构化数据处理模块

```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;
import org.apache.spark.sql.SparkSession;

/**
 * @author:auserwn
 * @create:--
 */
public class SparkSQL03_SQL {

    public static void main(String[] args) {

        //构建环境对象，通过这种构建器模式
        SparkSession sparkSql = SparkSession
                .builder()
                .master("local[*]")
                .appName("sparkSql")
                .getOrCreate();

        // sparksql中对数据模型也进行了封装，封装成了：RDD->dataset
        // 对接文件数据源时，将文件中的一行数据封装成ROW对象
        Dataset<Row> ds = sparkSql.read().json("data/user.json");

        // 将数据模型转换成为表
        ds.createOrReplaceTempView("user");

//        String sql = "select * from user";
        String sql = "select age as aaa from user";
        Dataset<Row> sqlDS = sparkSql.sql(sql);

        sqlDS.show();

        // 释放资源
        sparkSql.close();
    }
}

```



环境之间的转换：Core SparkContext -> SQL:SparkSession 

```
new SparkSession(new SparkContext(conf));
```

### 3.2 常用方式

#### 3.2.2 SQL使用方式访问

模型对象的访问： 将数据模型转换为二维的结构(行列)，可以通过sql进行访问

```
public class SparkSQL03_SQL {
    public static void main(String[] args) {
        //构建环境对象，通过这种构建器模式
        SparkSession sparkSql = SparkSession
                .builder()
                .master("local[*]")
                .appName("sparkSql")
                .getOrCreate();
        // sparksql中对数据模型也进行了封装，封装成了：RDD->dataset
        // 对接文件数据源时，将文件中的一行数据封装成ROW对象
        Dataset<Row> ds = sparkSql.read().json("data/user.json");

        // 将数据模型转换成为表
        ds.createOrReplaceTempView("user");

        String sql = "select age as aaa from user";
        Dataset<Row> sqlDS = sparkSql.sql(sql);

        sqlDS.show();
        // 释放资源
        sparkSql.close();
    }
}
```

#### 3.2.3 DSL特殊语法 扩展访问

```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;
import org.apache.spark.sql.SparkSession;

/**
 * @author:auserwn
 * @create:--
 */
public class SparkSQL02_Model_3 {

    public static void main(String[] args) {

        //构建环境对象，通过这种构建器模式
        SparkSession sparkSql = SparkSession
                .builder()
                .master("local[*]")
                .appName("sparkSql")
                .getOrCreate();

        Dataset<Row> ds = sparkSql.read().json("data/user.json");

        // 采用DSL语法进行访问
        ds.select("*").show();
        // 释放资源
        sparkSql.close();
    }
}

```



### 3.3 udf函数

sparksql 提供了一种特殊的方式，可以在sql中增加自定义方法来实现复杂的逻辑

```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;
import org.apache.spark.sql.SparkSession;
import org.apache.spark.sql.api.java.UDF1;
import org.apache.spark.sql.types.StringType$;

/**
 * @author:auserwn
 * @create:--
 */
public class SparkSQL03_SQL_1 {

    public static void main(String[] args) {

        //构建环境对象，通过这种构建器模式
        SparkSession sparkSql = SparkSession
                .builder()
                .master("local[*]")
                .appName("sparkSql")
                .getOrCreate();

        // sparksql中对数据模型也进行了封装，封装成了：RDD->dataset
        // 对接文件数据源时，将文件中的一行数据封装成ROW对象
        Dataset<Row> ds = sparkSql.read().json("data/user.json");

        // 将数据模型转换成为表
        ds.createOrReplaceTempView("user");

//        String sql = "select concat('Name:',name) from user";

        // 自定义方法在sql中使用，需要在spark中进行注册和注册
        // register方法需要传递三个参数：第一个是 sql使用的方法名 第二个是表示逻辑IN=>OUT 第三个是返回的数据类型
        sparkSql.udf().register("prefixName", new UDF1<String, String>() {
            @Override
            public String call(String name) throws Exception {
                return "Name is :" + name;
            }
        }, StringType$.MODULE$);

        String sql = "select prefixName(name) from user";
        Dataset<Row> sqlDS = sparkSql.sql(sql);

        sqlDS.show();

        // 释放资源
        sparkSql.close();
    }
}

```

### 3.4 udf函数的底层实现原理

udf函数是每一行数据都会用一次，一行一行处理

类似map 一行入一行出

![](image\spark_image\sparksql-1.png)



### 3.5 udaf函数的底层实现原理

udaf函数是所有的数据产生一个结果数据

udaf函数底层中需要存在一个**缓冲区**，用于存放临时数据，比如求年龄的平均值

类似reduce 输入多行，输出一行

spark3.x推荐使用extends Aggregator自定义UDAF，属于强类型的DataSet方式。

![](image\spark_image\sparksql-2.png)



```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Encoder;
import org.apache.spark.sql.Encoders;
import org.apache.spark.sql.expressions.Aggregator;

import java.io.Serializable;

/**
 * @author:auserwn
 * @create:--
 */
public class MyAvgAgeUDAF extends Aggregator<Long,AvgAgeBuffer,Long> {

    @Override
//    缓冲区的初始化操作
    public AvgAgeBuffer zero() {
        return new AvgAgeBuffer(0L,0L);
    }

    @Override
//    聚合：输入的年龄和缓冲区的做聚合
    public AvgAgeBuffer reduce(AvgAgeBuffer buffer, Long a) {
        buffer.setTotal(buffer.getTotal() + a);
        buffer.setCnt(buffer.getCnt() + 1);
        return buffer;
    }

    @Override
//    合并缓冲区的数据 分布式计算框架，需要合并多个缓冲区
    public AvgAgeBuffer merge(AvgAgeBuffer b1, AvgAgeBuffer b2) {
        return null;
    }

    @Override
//    计算最终结果
    public Long finish(AvgAgeBuffer buffer) {
        return buffer.getTotal()/buffer.getCnt();
    }

    @Override
    public Encoder<AvgAgeBuffer> bufferEncoder() {
        return Encoders.bean(AvgAgeBuffer.class);
    }

    @Override
    public Encoder<Long> outputEncoder() {
        return Encoders.LONG();
    }
}
```



### 3.6 读取数据源-加载和保存数据

sparkSQL读取保存的文件一般分为三种：JSON CSV 列式存储的文件。同时可以添加参数，来识别不同的存储和压缩格式。

#### 3.6.1 CSV文件

```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;
import org.apache.spark.sql.SparkSession;

/**
 * @author:auserwn
 * @create:--
 */
public class SparkSQL04_Source_csv_write {

    public static void main(String[] args) {

        SparkSession sparkSession = SparkSession.builder()
                .master("local[*]")
                .appName("sparkSQL")
                .getOrCreate();

        //读取csv文件
        Dataset<Row> csv = sparkSession.read()
                .option("header","true")
                //.option("sep","_") 代表用什么分割  \t通常为txv文件
                .csv("data/user.csv");
        csv.show();
        //写入csv文件
        csv.write()
                // 追加模式
                .option("header","true")
                .mode("append")
                .csv("output");
        sparkSession.close();
    }
}

```



#### 3.6.2 json文件

```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;
import org.apache.spark.sql.SparkSession;

/**
 * @author:auserwn
 * @create:--
 */
public class SparkSQL03_SQL {

    public static void main(String[] args) {

        SparkSession sparkSql = SparkSession
                .builder()
                .master("local[*]")
                .appName("sparkSql")
                .getOrCreate();
        Dataset<Row> ds = sparkSql.read().json("data/user.json");

        sqlDS.show();

        // 释放资源
        sparkSql.close();
    }
}

```



行式存储统计的效率不高，查询效率很高。行式存储依赖主键

列式存储如下：查询效率不高，但是做聚合统计效率高。

![](image\spark_image\sparksql-3.png)



#### 3.6.3 parquet文件 -spark的自己的列式存储

列式存储的压缩格式，snappy



#### 3.6.4 jdbc连接

```
public class SparkSQL04_Source_mysql {

    public static void main(String[] args) {

        SparkSession sparkSession = SparkSession.builder()
                .master("local[*]")
                .appName("sparkSQL")
                .getOrCreate();
        // 连接配置
        Properties properties = new Properties();
        properties.setProperty("user","root");
        properties.setProperty("password","12345678");

        Dataset<Row> jdbc = sparkSession.read().jdbc("jdbc:","tablename",properties);
        jdbc.show();
        sparkSession.close();
    }
}

```



#### 3.6.5 与hive交互

通常采用外部的hive

需要增加hive依赖，增加hive-site.xml文件到resource

```
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-hive_2.12</artifactId>
            <version>3.3.1</version>
        </dependency>
```

执行如下：

```
package com.atguigu.bigdata.sparksql;

import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;
import org.apache.spark.sql.SparkSession;

public class SparkSQL04_Source_hive {

    public static void main(String[] args) {

        System.setProperty("HADOOP_USER_NAME","atguigu");

        SparkSession sparkSession = SparkSession.builder()
                .enableHiveSupport()// 添加hive的支持
                .master("local[*]")
                .appName("sparkSQL")
                .getOrCreate();
        
        sparkSession.sql("show tables").show();

        sparkSession.close();
    }
}

```



## 第四章 sparkStreaming

特定场合下，对sprakCore(RDD)的封装

有界数据流 - 无界数据流

sparkStreaming实际上就是将无界数据流切分为有界数据流 方便计算

![](image\spark_image\sparkStreaming-1.png)

sparkStreaming底层实际上还是sparkCore

从数据处理方式处理：

- 流式数据处理：一个一个处理
- 微批量数据处理：一小批一小批(一小段时间为分割)
- 批量数据处理：一批一批处理

从数据处理延迟角度：

- 实时数据处理：数据处理以ms为单位
- 准实时数据处理：以s/min为单位
- 离线数据处理：数据处理以h/天为单位

spark是批量 离线数据处理框架

sparkStreaming是微批量 准实时数据处理框架

![](image\spark_image\sparkStreaming-2.png)

上图中解析：

- 第一个是sparkStreaming：无界数据流微批量经过转换为DStream 然后转换为RDD操作
- 第二个为普通文件处理
- 第三个为sparlsql，将指定格式转化为DataSet然后转换为rdd操作

本质核心还是转化为RDD进行数据处理。



![](image\spark_image\sparkStreaming-3.png)

### 4.1 实现

增加环境依赖

```
<dependency>
	<groupId>org.apache.spark</groupId>
	<artifactId>spark-streaming_2.12</artifactId>
	<version>3.3.1</version>
</dependency>
```



代码示例：

```
package com.atguigu.bigdata.sparkstreaming;
import org.apache.spark.SparkConf;
import org.apache.spark.streaming.Duration;
import org.apache.spark.streaming.api.java.JavaReceiverInputDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;


public class SparkStreaming02_socket {

    public static void main(String[] args) throws InterruptedException {

        SparkConf conf = new SparkConf();
        conf.setMaster("local[*]");
        conf.setAppName("sparkStreaming");
        //构建环境对象，通过这种构建器模式
        JavaStreamingContext jsc = new JavaStreamingContext(conf, new Duration(3000));
        JavaReceiverInputDStream<String> socketDS = jsc.socketTextStream("localhost", 9999);

        socketDS.print();
        jsc.start();
        jsc.awaitTermination();

    }
}

```



### 4.2 与Kafka对接

![](image\spark_image\sparkStreaming-4.png)

DStream确实是对RDD的封装，但是不是所有方法都进行了封装。有些方法不能使用：sortBy sortByKey

特定场合下如果想使用需要 将DStream转换成为RDD使用。

### 4.3 窗口操作

窗口的默认移动幅度是采集周期

窗口时长：计算内容的时间范围

滑动步长：隔多久触发一次计算

数据窗口范围>窗口移动幅度 数据重复

数据窗口范围<窗口移动幅度 数据丢失



### 4.4 DStream

![](image\spark_image\sparkStreaming-5.png)



### 4.5 sparkStream关闭

```
jsc.stop() //强制关闭
jsc.stop(true, true) //优雅的关闭，并不会直接关闭
```



## 第五章 spark内核

### 5.1 spark的Task的执行流程

job：作业  [RDD的行动算子触发job new ActiveJob]

stage：阶段 [数量=1+shuffle依赖的数量]

task：任务[数量=每个阶段最后一个RDD分区数量之和]

