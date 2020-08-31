# 从Pandas到PySpark


## PySpark的特性

Immutable

* Changes create new object references
* Old versions are unchanged

Lazy

* Compute does not happen until output is requested

## Pandas VS PySpark

### Load CSV

```python
# Pandas
df = pd.read_csv("datafile.csv")

# PySpark
df = spark.read.options(header=True, inferSchema=True).csv("datafile.csv")
```

### View Dataframe

```python
# Pandas
df

# PySpark
df.show()
```

### Columns & Data Types

```python
# Pandas
df.columns
df.dtypes

# PySpark
df.columns
df.dtypes
```

### Rename Columns

```python
# Pandas
df.columns = ['a', 'b', 'c']
df.rename(columns = {'old': 'new'})

# PySpark
df.toDF('a', 'b', 'c')
df.withColumnRenamed('old', 'new')
```

### Drop Columns

```python
# Pandas
df.drop('colx', axis=1)

# PySpark
df.drop('colx')
```

### Filtering

```python
# Pandas
df[df.col1 < 20]
df[(df.col1 < 20) & (df.col2 == 6)] # 注意括号

# PySpark
df[df.col1 < 20]
df[(df.col1 < 20) & (df.col2 == 6)] # 注意括号
```

### Add Column

```python
# Pandas
df['col3'] = 1 / df.col1 # 除以0得infty

# PySpark
df.withColumn('col3', 1 / df.col1) # 除以0得Null
```

### Fill Nulls

```python
# Pandas
df.fillna(0) # more options

# PySpark
df.fillna(0)
```

### Aggregation

```python
# Pandas
df.groupby(['col1', 'col2']).agg({'col3': 'mean', 'col4': 'min'})

# PySpark
df.groupby(['col1', 'col2']).agg({'col3': 'mean', 'col4': 'min'})
```

## 标准的转化

```python
# Pandas
import numpy as np
df['logCol'] = np.log(df.col)

# PySpark
import pyspark.sql.functions as F
df.withColumn('logCol', F.log(df.col))
```

使用`pyspark.sql.functions`里的函数，可以直接调用JVM，即Java引擎，而非使用较慢的python。


例子Row Conditional Statements

```python
# Pandas
df['status'] = df.apply(lambda r: 1 if r.col1 > 20 else 2 if r.col2 == 6 else 3, axis=1)

# PySpark
import pyspark.sql.functions as F
df.withColumn('status', \
	F.when(df.col1 > 20, 1) \
	 .when(df.col2 == 6, 2) \
	 .otherwise(3))
```

## 更复杂的转化，python必要时

```python
# Pandas
df['col5'] = df.col1.apply(lambda x : x + 1)

# PySpark
import pyspark.sql.functions as F
from pyspark.sql.types import DoubleType
# udf必须deterministic，pyspark优化默认相同运算结果相同
fn = F.udf(lambda x : x + 1, DoubleType())
df.withColumn('col5', fn(df.col1))
```

## Join

```python
# Pandas
leftDF.merge(rightDF, on='key')
leftDF.merge(rightDF, left_on='a', right_on='b')

# PySpark
leftDF.join(rightDF, on='key')
leftDF.join(rightDF, leftDF.a == rightDF.b)
```

默认为inner join

## 统计

```python
# Pandas
df.describe()

# PySpark
df.describe().show()
df.selectExpr("percentile_approx(col1, array(.25, .5, .75)) as col1").show()
```

## 统计图

```python
# Pandas
df.hist()

# PySpark
df.sample(False, 0.1).toPandas().hist()
```

## SQL

```python
# Pandas
NA

# PySpark
df.createOrReplaceTempView('foo')
df2 = spark.sql('select * from foo')
```

## 使用习惯

* 使用`pyspark.sql.functions`内置函数。
* 在cluster中保持和driver相同的版本和库。
* 观测状态`http://localhost:4040/`
* Learn about SSH port forwarding

不要：

* Try to iterate through rows
* Hard code a master in your dirver
	* Use `spark-submit` for that
* `df.toPandas().head()`
	* instead do: `df.limit(5).toPandas()`


参考

[Spark Submit 2017](https://youtu.be/XrpSRCwISdk)













