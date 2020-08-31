# Spark: Optimize Data Skew in N Ways


## What is Data Skew?

For distributed data computing engines like Spark/Hadoop, skewed data brings more pain than the big data. Ideally, for a cluster consisting of N machines, the wall time to finish an [embarrassingly parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel) job is reduced to 1/N. Unfortunately, perfect parallelism is not always achieved. In other words, the workload might not be evenly distributed across the cluster. For example, a machine might end up with computing 80% of the workload, while the other two machines are taking care of the remaining 20% of the workload (10% each).

<span style=color:red>Update</span>

As similar to the [Liebig's law](https://en.wikipedia.org/wiki/Liebig%27s_law_of_the_minimum), the total spent time to finish a stage (a collection of tasks) depends on the finishing time of the slowest task.

Assuming all machines of a cluster have the same computation capability, the workload difference is mainly due to the data amount difference among tasks. The uneven data distribution across the cluster is so-called **data skew**.

Spark利用对多个partition任务的并行，减少了运算所需的时间。对于均衡的任务集，若分布式集群机器数量为N，并行后所需时间理论上将变为原来的1/N。然而有些时候，任务集里的每个任务的工作量相差很大，导致有的机器需要长时间才能完成所分配的任务（并行不充分）。“不患多而患不均”造成了短板效应：一个Stage所耗费的时间，是由最慢的Task决定。这种工作量的不平衡主要源于所要处理的数据量不平衡，即数据倾斜（Data Skew）。

## 数据倾斜的表现

{{< figure src="/spark-data-skew/task-bar.png" class="center" width="1000" >}}

典型表现：

* 多数任务迅速完成，个别任务执行极慢（如上图）
* 由于大任务数据量过大，造成内存不足的报错（OOM = Out-Of-Memory）

通过Spark Web UI，可以查看各Task分配的数据量（`Shuffle Read Size/Records`），来确定是否出现了数据倾斜。通过`countByKey`，可以查看key的分布确定发生数据倾斜的目标算子。这类算子通常带有shuffle过程，常见的shuffle算子：`distinct`, `groupByKey`, `reduceByKey`, `aggregateByKey`, `join`, `cogroup`, `repartition`，等。

## 怎么缓解/消除

### 思路1 - 过滤异常数据
### 思路2 - 提高shuffle并行度
### 思路3 - 自定义Partitioner
### 思路4 - Broadcast机制
### 思路5 - 拆分join再union
### 思路6 - 大表key加盐，小表扩大N倍join
### 思路7 - map端先局部聚合
### 思路8 - 加盐局部聚合 + 去盐全局聚合


## 参考

[Arganzheng's Blog, Spark数据倾斜及其解决方案](http://arganzheng.life/spark-data-skew.html)

