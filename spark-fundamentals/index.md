# Spark FAQ



## 什么是Job, Stage, Task?

Spark数据处理是像流水线一样，通过一系列操作（算子）完成对RDD的处理, 即`RDD1 -> RDD2 -> ...`。操作分为两类：`Transformation`和`Action`。Spark使用lazy evaluation模式，即Transformation操作下达后，实际并不立刻运行，只有当遇到Action时才把之前所有的Transformation和当前Action运行完毕。每当一个Action触发，就会生成一个Job。因此Job以Action划分。Job之间是串行的，只有当前job结束，才会启动下一个job。

{{< figure src="/spark-fundamentals/narrow_vs_wide_dependencies.png" class="center" width="1000" >}}

一个Job会被划分为一个或多个stage。在一个stage中，任务是可以并行计算的。stage是按照ShuffleDependency来进行划分的。两种依赖方式为窄依赖（narrow dependency）与宽依赖（wide dependency）。区分两种依赖的方法是看：父RDD的partition是否被多个（>1）子RDD的partition使用，若未被多个子RDD的partition使用，则为窄依赖，不需shuffle，不划分stage； 否则为宽依赖，划分stage。上图对比了两种依赖方式的不同。具体划分stage的算法是: 从最后一个RDD开始，从后往前推，找该RDD和父RDD之间的依赖关系，如果是窄依赖，会继续找父RDD的父RDD，如果是宽依赖，就会从该RDD开始到前面所有的RDD划分为一个stage，递归的出口是直到找不到父RDD，最后把所有的RDD划分为一个stage。

一个例子：

{{< figure src="/spark-fundamentals/stage123.png" class="center" width="1000" >}}

一个stage并行的任务称为task，对应一个partition的处理，即task总数为stage的partition总数。


## 参考

[Wide vs Narrow Dependencies github blog](https://github.com/rohgar/scala-spark-4/wiki/Wide-vs-Narrow-Dependencies)

[stackoverflow, Does stages in an application run parallel in spark](https://stackoverflow.com/a/41340858)

[Queirozf Apache Spark Architecture Overview: Jobs, Stages, Tasks, etc](https://queirozf.com/entries/apache-spark-architecture-overview-clusters-jobs-stages-tasks)

[cnblogs, qingyunzong](https://www.cnblogs.com/qingyunzong/category/1202252.html)

[csdn, Z_Data](https://blog.csdn.net/Z_Date/article/details/87894821)

[Spark by Examples, Spark Repartition vs Coalesce](https://sparkbyexamples.com/spark/spark-repartition-vs-coalesce/)

[Arganzheng's Blog, Spark数据倾斜及其解决方案](http://arganzheng.life/spark-data-skew.html)











