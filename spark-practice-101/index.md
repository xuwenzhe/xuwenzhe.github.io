# Spark 101: DataFrame基本使用


## Example 1. DataFrame的5种基本操作

1.筛选行，2.选取列，3.增加行列，4.分组总结，5.排序

一个简单的工资数据集：

```python
df = spark.createDataFrame([
    (1, 'sales', 4200),
    (2, 'admin', 3100),
    (3, 'sales', 4000),
    (4, 'sales', 4000),
    (5, 'admin', 2700),
    (6, 'dev', 3400),
    (7, 'dev', 5200),
    (8, 'dev', 3700),
    (9, 'dev', 4400),
    (10, 'dev', 4400)
], schema=['id', 'dept', 'salary'])

df.show()

# +---+-----+------+
# | id| dept|salary|
# +---+-----+------+
# |  1|sales|  4200|
# |  2|admin|  3100|
# |  3|sales|  4000|
# |  4|sales|  4000|
# |  5|admin|  2700|
# |  6|  dev|  3400|
# |  7|  dev|  5200|
# |  8|  dev|  3700|
# |  9|  dev|  4400|
# | 10|  dev|  4400|
# +---+-----+------+
```

###### Q1: 列出dev部门工资大于4000的所有id。(`filter`, `select`, `show`)

```python
df.filter((F.col('dept') == 'dev') & (F.col('salary') > 4000)).select(['id', 'salary']).show()

# +---+------+
# | id|salary|
# +---+------+
# |  6|  3400|
# |  7|  5200|
# |  8|  3700|
# |  9|  4400|
# | 10|  4400|
# +---+------+
```

###### Q2: 求基础工资的10%奖金列，并补全列名dept->department。(`withColumn`, `withColumnRenamed`)

```python
df.withColumn('bonus', F.col('salary')*0.1).withColumnRenamed('dept', 'department').show()

# +---+----------+------+-----+
# | id|department|salary|bonus|
# +---+----------+------+-----+
# |  1|     sales|  4200|420.0|
# |  2|     admin|  3100|310.0|
# |  3|     sales|  4000|400.0|
# |  4|     sales|  4000|400.0|
# |  5|     admin|  2700|270.0|
# |  6|       dev|  3400|340.0|
# |  7|       dev|  5200|520.0|
# |  8|       dev|  3700|370.0|
# |  9|       dev|  4400|440.0|
# | 10|       dev|  4400|440.0|
# +---+----------+------+-----+
```

###### Q3: 求每个部门的最低工资，平均工资，最高工资。(`groupby`, `agg`, `alias`, `sort`), 并按平均工资降序排序。

```python
df.groupby('dept').agg(F.min(F.col('salary')).alias('minimum'), \
                       F.avg(F.col('salary')).alias('mean'), \
                       F.max(F.col('salary')).alias('maximum'))\
                .sort('mean', ascending=False).show()

# +-----+-------+------------------+-------+
# | dept|minimum|              mean|maximum|
# +-----+-------+------------------+-------+
# |  dev|   3400|            4220.0|   5200|
# |sales|   4000|4066.6666666666665|   4200|
# |admin|   2700|            2900.0|   3100|
# +-----+-------+------------------+-------+
```

## Example 2. groupby 与 window

参考：[Youtube, How do spark window functions work?](https://youtu.be/wH6RaEWuiDQ)

###### Q1: 求部门工资数组，和部门总工资。(`groupby`, `agg`, `collect_list`)

```python
df = df.groupby('dept').agg(
    F.expr('collect_list(salary)').alias('list_salary'),
    F.expr('avg(salary)').alias('average_salary'),
    F.expr('sum(salary)').alias('total_salary')
).show()

# +-----+--------------------+------------------+------------+
# | dept|         list_salary|    average_salary|total_salary|
# +-----+--------------------+------------------+------------+
# |  dev|[3400, 5200, 3700...|            4220.0|       21100|
# |sales|  [4200, 4000, 4000]|4066.6666666666665|       12200|
# |admin|        [3100, 2700]|            2900.0|        5800|
# +-----+--------------------+------------------+------------+
```

###### Q2: 在每条数据旁列出所在部门的工资情况（上问的答案）。(`Window`, `partitionBy`, `over`)

```python
windowSpec = Window.partitionBy('dept')

df = df.withColumn('list_salary', F.collect_list(F.col('salary')).over(windowSpec))\
        .withColumn('average_salary', F.avg(F.col('salary')).over(windowSpec))\
        .withColumn('total_salary', F.sum(F.col('salary')).over(windowSpec)).show()

# +---+-----+------+--------------------+------------------+------------+
# | id| dept|salary|         list_salary|    average_salary|total_salary|
# +---+-----+------+--------------------+------------------+------------+
# |  6|  dev|  3400|[3400, 5200, 3700...|            4220.0|       21100|
# |  7|  dev|  5200|[3400, 5200, 3700...|            4220.0|       21100|
# |  8|  dev|  3700|[3400, 5200, 3700...|            4220.0|       21100|
# |  9|  dev|  4400|[3400, 5200, 3700...|            4220.0|       21100|
# | 10|  dev|  4400|[3400, 5200, 3700...|            4220.0|       21100|
# |  1|sales|  4200|  [4200, 4000, 4000]|4066.6666666666665|       12200|
# |  3|sales|  4000|  [4200, 4000, 4000]|4066.6666666666665|       12200|
# |  4|sales|  4000|  [4200, 4000, 4000]|4066.6666666666665|       12200|
# |  2|admin|  3100|        [3100, 2700]|            2900.0|        5800|
# |  5|admin|  2700|        [3100, 2700]|            2900.0|        5800|
# +---+-----+------+--------------------+------------------+------------+
```

###### Q3: 将工资在其部门顺序，在部门内扫描得到前缀和（相等的工资同时扫描）。(`orderBy`)

```python
windowSpec = Window.partitionBy('dept').orderBy(F.asc('salary'))

df = df.withColumn('list_salary', F.collect_list(F.col('salary')).over(windowSpec))\
        .withColumn('average_salary', F.avg(F.col('salary')).over(windowSpec))\
        .withColumn('total_salary', F.sum(F.col('salary')).over(windowSpec)).show()

# +---+-----+------+--------------------+------------------+------------+
# | id| dept|salary|         list_salary|    average_salary|total_salary|
# +---+-----+------+--------------------+------------------+------------+
# |  6|  dev|  3400|              [3400]|            3400.0|        3400|
# |  8|  dev|  3700|        [3400, 3700]|            3550.0|        7100|
# |  9|  dev|  4400|[3400, 3700, 4400...|            3975.0|       15900|
# | 10|  dev|  4400|[3400, 3700, 4400...|            3975.0|       15900|
# |  7|  dev|  5200|[3400, 3700, 4400...|            4220.0|       21100|
# |  3|sales|  4000|        [4000, 4000]|            4000.0|        8000|
# |  4|sales|  4000|        [4000, 4000]|            4000.0|        8000|
# |  1|sales|  4200|  [4000, 4000, 4200]|4066.6666666666665|       12200|
# |  5|admin|  2700|              [2700]|            2700.0|        2700|
# |  2|admin|  3100|        [2700, 3100]|            2900.0|        5800|
# +---+-----+------+--------------------+------------------+------------+
```

注：注意观察id9&10的重复工资，相等的工资条将导致工资前缀和的同时扫描。请看下问一个更贴切实际的前缀和实例。

###### Q4: Q3问题不变（相等的工资先后扫描）。(`rowsBetween`)

```python
windowSpec = Window.partitionBy('dept').orderBy(F.asc('salary'))\
    .rowsBetween(Window.unboundedPreceding, Window.currentRow)

df = df.withColumn('list_salary', F.collect_list(F.col('salary')).over(windowSpec))\
        .withColumn('average_salary', F.avg(F.col('salary')).over(windowSpec))\
        .withColumn('total_salary', F.sum(F.col('salary')).over(windowSpec)).show()

# +---+-----+------+--------------------+------------------+------------+
# | id| dept|salary|         list_salary|    average_salary|total_salary|
# +---+-----+------+--------------------+------------------+------------+
# |  6|  dev|  3400|              [3400]|            3400.0|        3400|
# |  8|  dev|  3700|        [3400, 3700]|            3550.0|        7100|
# |  9|  dev|  4400|  [3400, 3700, 4400]|3833.3333333333335|       11500|
# | 10|  dev|  4400|[3400, 3700, 4400...|            3975.0|       15900|
# |  7|  dev|  5200|[3400, 3700, 4400...|            4220.0|       21100|
# |  3|sales|  4000|              [4000]|            4000.0|        4000|
# |  4|sales|  4000|        [4000, 4000]|            4000.0|        8000|
# |  1|sales|  4200|  [4000, 4000, 4200]|4066.6666666666665|       12200|
# |  5|admin|  2700|              [2700]|            2700.0|        2700|
# |  2|admin|  3100|        [2700, 3100]|            2900.0|        5800|
# +---+-----+------+--------------------+------------------+------------+
```

###### Q5: 将Q3&4问中的前缀和替换为，滑动窗口总和（当前数据的上一条，当前数据）(`rowsBetween`)

```python
windowSpec = Window.partitionBy('dept').orderBy(F.asc('salary'))\
    .rowsBetween(-1, Window.currentRow) # 区间表示

df = df.withColumn('list_salary', F.collect_list(F.col('salary')).over(windowSpec))\
        .withColumn('average_salary', F.avg(F.col('salary')).over(windowSpec))\
        .withColumn('total_salary', F.sum(F.col('salary')).over(windowSpec)).show()

# +---+-----+------+------------+--------------+------------+
# | id| dept|salary| list_salary|average_salary|total_salary|
# +---+-----+------+------------+--------------+------------+
# |  6|  dev|  3400|      [3400]|        3400.0|        3400|
# |  8|  dev|  3700|[3400, 3700]|        3550.0|        7100|
# |  9|  dev|  4400|[3700, 4400]|        4050.0|        8100|
# | 10|  dev|  4400|[4400, 4400]|        4400.0|        8800|
# |  7|  dev|  5200|[4400, 5200]|        4800.0|        9600|
# |  3|sales|  4000|      [4000]|        4000.0|        4000|
# |  4|sales|  4000|[4000, 4000]|        4000.0|        8000|
# |  1|sales|  4200|[4000, 4200]|        4100.0|        8200|
# |  5|admin|  2700|      [2700]|        2700.0|        2700|
# |  2|admin|  3100|[2700, 3100]|        2900.0|        5800|
# +---+-----+------+------------+--------------+------------+
```

###### Q6: 求每条工资的部门内顺序，并比较官方的三种名次规则。(`rank`, `dense_rank`, `perc_rank`)

```python
windowSpec = Window.partitionBy('dept').orderBy(F.asc('salary'))

df = df.withColumn('list_salary', F.collect_list(F.col('salary')).over(windowSpec))\
        .withColumn('rank', F.rank().over(windowSpec))\
        .withColumn('dense_rank', F.dense_rank().over(windowSpec))\
        .withColumn('perc_rank', F.percent_rank().over(windowSpec)).show()

# +---+-----+------+--------------------+----+----------+---------+
# | id| dept|salary|         list_salary|rank|dense_rank|perc_rank|
# +---+-----+------+--------------------+----+----------+---------+
# |  6|  dev|  3400|              [3400]|   1|         1|      0.0|
# |  8|  dev|  3700|        [3400, 3700]|   2|         2|     0.25|
# |  9|  dev|  4400|[3400, 3700, 4400...|   3|         3|      0.5|
# | 10|  dev|  4400|[3400, 3700, 4400...|   3|         3|      0.5|
# |  7|  dev|  5200|[3400, 3700, 4400...|   5|         4|      1.0|
# |  3|sales|  4000|        [4000, 4000]|   1|         1|      0.0|
# |  4|sales|  4000|        [4000, 4000]|   1|         1|      0.0|
# |  1|sales|  4200|  [4000, 4000, 4200]|   3|         2|      1.0|
# |  5|admin|  2700|              [2700]|   1|         1|      0.0|
# |  2|admin|  3100|        [2700, 3100]|   2|         2|      1.0|
# +---+-----+------+--------------------+----+----------+---------+
```

## Example 3. Join与Skew Join

join可以把两张表内关键字匹配的数据做笛卡尔乘积，放入结果表。

参考： [Medium, 6 spark exercises to rule them all](https://towardsdatascience.com/six-spark-exercises-to-rule-them-all-242445b24565)

数据集：三张表sales, products, sellers, 大约6GB，使用local模式。

{{< figure src="/spark-practice-101/sales_dataset.png" class="center" width="1000" >}}

###### Q1: 三张表各有多少记录？(`parquet`, `count`)

```python
DATASET_PATH = './DatasetToCompleteTheSixSparkExercises'
products = spark.read.parquet(f'{DATASET_PATH}/products_parquet')
sales = spark.read.parquet(f'{DATASET_PATH}/sales_parquet')
sellers = spark.read.parquet(f'{DATASET_PATH}/sellers_parquet')

sales.count(), products.count(), sellers.count()

# (20000040, 75000000, 10)
```

###### Q2: 有多少种商品至少被卖出过一次？(`distinct`)

```python
sales.select(['product_id']).distinct().count()

# 993429
```

###### Q3: 订单中最常见的商品是哪个？(`sort`, `limit`)

```python
sales.groupby(['product_id']).count().sort('count', ascending=False).limit(1).show()

# +----------+--------+
# |product_id|   count|
# +----------+--------+
# |         0|19000000|
# +----------+--------+
```

###### Q4: 每天各卖多少种商品？(`countDistinct`)

```python
sales.groupby(['date']).agg(F.countDistinct("product_id").alias("cnt")).sort("cnt", ascending=False).show()

# +----------+------+
# |      date|   cnt|
# +----------+------+
# |2020-07-06|100765|
# |2020-07-09|100501|
# |2020-07-01|100337|
# |2020-07-03|100017|
# |2020-07-02| 99807|
# |2020-07-05| 99796|
# |2020-07-04| 99791|
# |2020-07-07| 99756|
# |2020-07-08| 99662|
# |2020-07-10| 98973|
# +----------+------+
```

###### Q5: 求订单盈利（数量x价格）的平均值。(`join`, skewed data)

由于订单中的商品集中在少数商品ID，使用product_id进行join会造成skewed data,降低并行效率。

```python
df = sales.join(products, on='product_id')
df = df.withColumn('revenue', sales['num_pieces_sold'] * products['price'])
df.select(F.avg('revenue')).show()

# +------------------+
# |      avg(revenue)|
# +------------------+
# |1246.1338560822878|
# +------------------+
```

{{< figure src="/spark-practice-101/exercise1-skew.png" class="center" width="1000" >}}

可以对product常见key加盐，可以先join两表加盐的key部分，再join两表未加盐的普通key部分，最后union。这样可以把集中分布的key加盐溶解，提高集群的并行效率。

```python
def skew_join(skew_df, other_df, on, topK, how='inner', SALT_RANGE=100):
    # STEP1: find topK popular keys in skew_df
    topKkey = skew_df.groupby(on).agg({'*': 'count'}).withColumnRenamed('count(1)', '_skew_key_cnt')\
                    .sort('_skew_key_cnt', ascending=False).limit(topK)
    
    # STEP2: split skew_df, other_df into pop and unpop halves
    skew_df_pop = skew_df.join(F.broadcast(topKkey), on=on).drop('_skew_key_cnt')
    skew_df_unpop = skew_df.join(F.broadcast(topKkey), on=on, how='left_anti').drop('_skew_key_cnt')
    
    other_df_pop = other_df.join(F.broadcast(topKkey), on=on).drop('_skew_key_cnt')
    other_df_unpop = other_df.join(F.broadcast(topKkey), on=on, how='left_anti').drop('_skew_key_cnt')
    
    # STEP3: salt skew_df_pop, replicate other_df_pop
    skew_df_pop = skew_df_pop.withColumn('_skew_salt', (F.rand() * SALT_RANGE).cast(types.IntegerType()))
    other_df_pop = other_df_pop.withColumn('_skew_salts', F.array([F.lit(i) for i in range(SALT_RANGE+1)]))
    other_df_pop = other_df_pop.select('*', F.explode('_skew_salts').alias('_skew_salt')).drop('_skew_salts')
    
    # STEP4: join seperately and union
    res_pop = skew_df_pop.join(other_df_pop, on=on+['_skew_salt'], how=how).drop('_skew_salt')
    res_unpop = skew_df_unpop.join(other_df_unpop, on=on, how=how)
    
    return res_pop.union(res_unpop)
    
df = skew_join(sales, products, on=['product_id'], topK=10)
df = df.withColumn('revenue', sales['num_pieces_sold'] * products['price'])
df.select(F.avg('revenue')).show()

# +------------------+
# |      avg(revenue)|
# +------------------+
# |1246.1338560822878|
# +------------------+
```

{{< figure src="/spark-practice-101/exercise1-salt.png" class="center" width="1000" >}}

###### Q6: 对每个销售员，求一个订单对其目标的平均贡献。(`broadcast`)

注：比如销售员0目标250件总共拥有三笔订单，订单1卖10件，订单2卖8件，订单3卖7件。那么每张订单贡献为（10/250， 8/250， 7/250），平均贡献为avg(10/250, 8/250, 7/250)=25/250/3 = 0.033

由于seller表较小，可以broadcast给所有worker进行worker内join，无需shuffle，提高效率。

```python
df = sales.join(F.broadcast(sellers), on='seller_id')\
    .withColumn('contribution', F.col('num_pieces_sold')/F.col('daily_target'))
df.groupby('seller_id').agg({'contribution':'avg'}).show()

# +---------+--------------------+
# |seller_id|   avg(contribution)|
# +---------+--------------------+
# |        7|2.595228787788170...|
# |        3| 1.62888537056594E-4|
# |        8|9.213030375408861E-5|
# |        0|2.019885898946922...|
# |        5|4.211073965904022E-5|
# |        6|4.782147194369122E-5|
# |        9|3.837913136180238E-5|
# |        1|1.964233366461014...|
# |        4|3.296428039825817E-5|
# |        2|6.690408001060484E-5|
# +---------+--------------------+
```

## Example 4. UDF

Q1: 在sales数据中创建列，根据order_id和bill_raw_text计算每条销售数据bill的hash值，并查看是否有hash冲突。(`udf`)

hash规则：
当order_id为奇数，计算sha256；当order_id为偶数时，迭代计算md5，迭代次数为大写A出现的次数。

```python
import hashlib
from pyspark.sql.types import StringType

def fancy_hash(order_id, bill_raw_text):
    res = bill_raw_text.encode('utf-8')
    # 1) even case
    if int(order_id) % 2 == 0:
        cnt_A = bill_raw_text.count('A')
        for _ in range(cnt_A):
            res = hashlib.md5(res).hexdigest().encode('utf-8')
        res = res.decode('utf-8')
    else:
        res = hashlib.sha256(res).hexdigest()
    return res


# Convert the fancy_hash python function into its UDF
fancy_hash_udf = F.udf(lambda arg1, arg2: fancy_hash(arg1, arg2), StringType())

sales.withColumn('hashed_bill', fancy_hash_udf(sales['order_id'], sales['bill_raw_text']))\
        .groupby('hashed_bill').agg(F.count('*').alias('cnt')).filter(F.col('cnt') > 1).show()

# 返回为空，即无哈希冲突
```
















