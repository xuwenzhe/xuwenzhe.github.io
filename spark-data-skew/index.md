# Spark: N Ways to Optimize Data Skew


## 什么是数据倾斜？

对于Spark/Hadoop等分布式系统，机器间工作量的不均匀比单纯的工作量大更难处理，因为单纯的工作量大基本通过scale-out就可以解决。假设有100个任务并行处理，若任务间工作量相当，20台机器要比10台机器耗时减半（假设机器性能相当）。然而当任务间工作量相差巨大时（例如，任务中10个大任务，90个小任务，工作量差别1M倍），很难通过scale-out的方式充分利用并行优势，此时，完成总耗时取决于最慢的大任务何时完成（木桶原理）。

对于Spark这种计算引擎，工作量主要在于所需处理的数据量。因此当任务划分时，**数据分布不均匀，即发生了数据倾斜**。

## 数据是如何倾斜的？

当Spark产生shuffle操作时，相同key的数据会被放在相同的partition。因此当某一个key的数据量过大（即popular key），会造成partition间工作量巨大差别，造成数据倾斜。这样不仅影响了并行效率，更加容易造成内存不足，产生OOM（Out Of Memory）报错使整个应用崩溃。

常见会触发shuffle的算子：`distinct`, `groupByKey`, `reduceByKey`, `aggregateByKey`, `join`, `cogroup`, `repartition`。

## 如何识别数据倾斜？

{{< figure src="/spark-data-skew/task-bar.png" class="center" width="1000" >}}

* 观察stage进度条，发现大多数task很快执行完，但是剩余task等待时间巨长甚至出现OOM报错。（如上图）
* 查看**Spark Web UI**里的`Shuffle Read Size/Records`，查看任务间数据量分布是否均匀。
* 计算key的分布。（如下）

```scala
df.select("key").sample(false, 0.1)           // 数据采样 
    .(k => (k, 1)).reduceBykey(_ + _)         // 统计 key 出现的次数
    .map(k => (k._2, k._1)).sortByKey(false)  // 根据 key 出现次数进行排序
    .take(10)                                 // 取前 10 个。
```

## 发生了数据倾斜怎么办？

### 思路1 - 过滤异常数据

如果数据包含大量异常key，过滤掉就好了。

异常key：

1. 空值 `Null`
2. 无效数据，大量重复的测试数据或是对结果影响不大的有效数据
3. 有效数据，业务导致的正常数据分布

对于情况1&2，直接过滤即可。对于3，直接过滤不适用，尝试以下其他方法。 

### 思路2 - 提高shuffle并行度

通过增加partition的方法，让每个task执行更少的不同key，把原本被分配到同一task的不同key分配到不同task。

RDD 操作 可在需要 Shuffle 的操作算子上直接设置并行度或者使用 `spark.default.parallelism` 设置。如果是 Spark SQL，还可通过 `SET spark.sql.shuffle.partitions=[num_tasks]` 设置并行度。默认参数由不同的 Cluster Manager 控制。

dataFrame 和 sparkSql 可以设置 `spark.sql.shuffle.partitions=[num_tasks]` 参数控制 shuffle 的并发度，默认为200。

**TIPS** 可以把数据倾斜类比为 hash 冲突。提高并行度就类似于 提高 hash 表的大小。

### 思路3 - 自定义Partitioner
### 思路4 - Broadcast机制
对于Join算子，若一边数据足够小到放入内存，可以通过Broadcast方法广播到Worker中。这样的Broadcast Join完全避免了shuffle，彻底消除了数据倾斜产生的条件。

```python
from pyspark.sql.functions import broadcast
result = broadcast(A).join(B, ["join_col"], "left")
```

{{< figure src="/spark-data-skew/broadcast-join.jpeg" class="center" width="1000" >}}


### 思路5 - 拆分join再union
对于Join算子，若一边数据倾斜，另一边数据均匀，且无法map端join（broadcast），可以把两边大表各拆分为popular key与unpopular key部分，分别join再union。

join例子：  left （数据倾斜）和 right（数据均匀）

1. 采样统计left中Top K popular key，把两表按照key是否popular分拆
	* `left = leftSkew + leftUnskew`
	* `right = rightSkew + rightUnskew`
2. 左加盐，右膨胀
	* 对leftSkew加盐，即`key -> (key, rand(N))`, 这样大key会被分配到不同partition
	* 对rightSkew做flatMap操作把每条数据转化为N条数据，即`key -> (key, 1), (key, 2), ... (key, N)`
3. 分别join再union，即`Union ( Join(leftSkew, rightSkew), Join(leftUnskew, rightUnskew) )`

### 思路6 - 大表key加盐，小表扩大N倍join
对于数据倾斜表中key都很大的情况，分拆popular key与unpopular key意义不大。可直接对大表整体加盐，小表扩大N倍后再join，即此思路为思路5的局部优化`Join(leftSkew, rightSkew)`。

#### 例子

**创建两个表来模拟，其中大表数据倾斜，小表相对均匀**

```python
from math import exp
from random import randint
from datetime import datetime

def count_elements(splitIndex, iterator):
    n = sum(1 for _ in iterator)
    yield (splitIndex, n)

def get_part_index(splitIndex, iterator):
    for it in iterator:
        yield (splitIndex, it)

num_parts = 18
# create the large skewed rdd
skew_rdd = sc.parallelize(range(0,num_parts), num_parts).flatMap(lambda x: range(0, int(exp(x))))
skew_rdd = skew_rdd.mapPartitionsWithIndex(lambda idx, x: get_part_index(idx, x))
skew_df = spark.createDataFrame(skewed_large_rdd,['x','y'])

small_rdd = sc.parallelize(range(0,num_parts), num_parts).map(lambda x: (x, x))
small_df = spark.createDataFrame(small_rdd,['a','b'])

# skew_df:         small_df:
# x, y             a, b
# ====             ====
# 0, 0             0, 0
# 1, 0             1, 1
# 1, 1             2, 2
# 2, 0             3, 3
# 2, 1             4, 4
# 2, 2             5, 5
# 2, 3             6, 6
# 2, 4             7, 7
# 2, 5             8, 8
# 2, 6             9, 9
# ...              ...
```

**大表key加盐，小表扩大N（=100）倍**

```python
salt_bins = 100
from pyspark.sql import functions as F

skew_df_salt = skew_df.withColumn('salt', (F.rand()*salt_bins).cast('int')).cache()

small_df_rep = small_df.withColumn('replicate', F.array([F.lit(i) for i in range(salt_bins)]))
small_df_rep = small_df_rep.select('*', F.explode('replicate').alias('salt')).drop('replicate').cache()

# skew_df:             small_df:
# x, y, salt           a, b, salt
# ==========           ==========
# 0, 0, 24             0, 0, 0
# 1, 0, 53             0, 0, 1
# 1, 1, 24             0, 0, 2
# 2, 0, 37             0, 0, 3
# 2, 1, 99             0, 0, 4
# 2, 2, 87             0, 0, 5
# 2, 3, 14             0, 0, 6
# 2, 4, 94             0, 0, 7
# 2, 5, 12             0, 0, 8
# 2, 6,  5             0, 0, 9
# ...                  ...
```

**最后join**

```python
%%time # measure excution time in jupyter notebook
res = skew_df_salt.join(small_df_rep, (skew_df_salt['x'] == small_df_rep['a'])\
	& (skew_df_salt['salt'] == small_df_rep['salt']))
res.count()

# res:
# x, y, b
# =========
# 0, 0, 0
# 1, 0, 1
# 1, 1, 1
# 2, 0, 2
# 2, 1, 3
# 2, 2, 2
# 2, 3, 2
# 2, 4, 2
# 2, 5, 2
# 2, 6, 2
# ...  
```

### 思路7 - map端先局部聚合
### 思路8 - 加盐局部聚合 + 去盐全局聚合


## 参考

[Arganzheng's Blog, Spark数据倾斜及其解决方案](http://arganzheng.life/spark-data-skew.html)

