# Kaggle Regression Eda


## 数据的读入

## 特征的理解

### 特征的数据类型

```python
df.info(verbose=True) # 查看每列缺省情况和数据类型
```

| Pandas dtype | Python type | NumPy type | Usage |
| ------------ | ----------- | ---------- | ----- |
| object       | str or mixed| string_, unicode_, mixedtypes | 字符串文本 |
| int64        | int         | int_, int8, int16, int32, int64, uint8, uint16, uint32, uint64 | 整数 |
| float64      | float       | float_, float16, float32, float64 | 浮点数 |
| bool         | bool        | bool_ | 真假 |
| datetime64   | NA          | datetime64[ns]| 日期与时间 | 
| timedelta[ns]| NA          | NA | 日期时间差 |
| category     | NA          | NA | Finite list of text values |

### 特征类型
这个步骤是用来理解数据中每一列的具体含义，通过查看feature与label的关系，可以检查是否符合自己的直观感受。比如，通常情况下，房子越大，房子越新，房价越高，等等。可以通过数据类型将每个feature划归到对应的特征类型，便于后续的分析与建模。

#### 类型1: ID
主要查看数据集是否为event level，看是否需要聚合操作将每个ID的行为总结。数据集可以是交易数据，每行数据为一笔交易，也可以是看房数据，同一套房子可能触发多个看房事件。例如，聚合每套房子的看房者数量，可以一定程度上帮助预测房子的火热程度。

#### 类型2: Numerical
区别于类别型，数值型的特征值是可比较的，包含连续型和离散型。连续型的例子可以是价格，面积，温度, 时间。离散型的例子可以是用户打分（例如五星好评）。对于时间特征，可以做差生成时间段特征。

#### 类型3: Categorical

```python
def specify_feature_type(df):
    numerical_cols = df.select_dtypes(include='number').columns.tolist()
    object_cols = df.select_dtypes(include='object').columns.tolist()
    
    id_features = ['Id']
    label = ['SalePrice']
    numerical_features = list(set(numerical_cols) - set(id_features) - set(label))
    categorical_features = object_cols
    
    return id_features, numerical_features, categorical_features, label

id_features, numerical_features, categorical_features, label = specify_feature_type(df)
    
assert len(id_features) + len(numerical_features) + len(categorical_features) + len(label) == df.shape[1]
```

### 特征的分布与预测能力

对于numerical feaure，可以计算feature与label的线性相关性Pearson Correlation Coefficient (PCC)，并将强相关性关系画出来。

```python
def rank_feature_by_corr(df, method='pearson'):
    """calculate Pearson correlation coefficient between feature and label, and rank the results"""
    feature_corr = dict()
    for feature in numerical_features:
        feature_corr[feature] = df[feature].corr(df[label[0]], method)
    corr_df = pd.DataFrame(feature_corr.items(), columns=['feature', 'corr'])
    corr_df['abs'] = np.abs(corr_df['corr'])
    return corr_df.sort_values('abs', ascending=False).reset_index(drop=True)

def plot_feature_correlation(df, feature):
    fig = px.scatter(df, x=feature, y=label[0], \
                     marginal_x="histogram", marginal_y="histogram",\
                     width=600, height=450)
    fig.show()

pcc_rank = rank_feature_by_corr(df)
for i in range(2):
    plot_feature_correlation(df, pcc_rank.loc[i,'feature'])
```

{{< figure src="/kaggle-regression-eda/plot_feature_correlation.png" class="center" width="500" >}}

而对于categorical feature, 可以画出feature列中每种类别的label分布。

```python
import plotly.figure_factory as ff

def plot_label_distribution_per_class(df, feature):
    hist_data = []
    group_labels = []
    for name, group in df.groupby(feature):
        hist_data.append(group[label[0]])
        group_labels.append(name)

    # Create distplot with curve_type set to 'normal'
    fig = ff.create_distplot(hist_data, group_labels, show_hist=False)

    # Add title
    fig.update_layout(title_text='Curve and Rug Plot')
    fig.show()
    
plot_label_distribution_per_class(df, categorical_features[0])
```
{{< figure src="/kaggle-regression-eda/plot_label_distribution_per_class.png" class="center" width="1000" >}}

也可以用heatmap将两两之间的相关性画出来。

```python
fig = px.imshow(df.corr())
fig.show()
```

{{< figure src="/kaggle-regression-eda/heatmap.png" class="center" width="1000" >}}


## 参考
* [pandas数据类型](https://pbpython.com/pandas_dtypes.html)
* [Kaggle房价预测](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/)
* [correlation between categorical and numerical variable](https://datascience.stackexchange.com/a/898)

