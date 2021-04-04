# 《机器学习实战（第二版）》-读书笔记1


## Chapter 1: The Machine Learning Landscape

##### 下载github库中csv数据集:

```python
import urllib.request
DOWNLOAD_ROOT = "https://raw.githubusercontent.com/ageron/handson-ml2/master/"
os.makedirs(datapath, exist_ok=True)
for filename in ("oecd_bli_2015.csv", "gdp_per_capita.csv"):
    print("Downloading", filename)
    url = DOWNLOAD_ROOT + "datasets/lifesat/" + filename
    urllib.request.urlretrieve(url, datapath + filename)
```

##### 预处理pivot:

```python
def prepare_country_stats(oecd_bli, gdp_per_capita):
	# 筛选行
    oecd_bli = oecd_bli[oecd_bli["INEQUALITY"]=="TOT"]
    # 抽取多行数据为多列数据
    oecd_bli = oecd_bli.pivot(index="Country", columns="Indicator", values="Value")
    # 列重命名
    gdp_per_capita.rename(columns={"2015": "GDP per capita"}, inplace=True)
    gdp_per_capita.set_index("Country", inplace=True)
    # join
    full_country_stats = pd.merge(left=oecd_bli, right=gdp_per_capita,
                                  left_index=True, right_index=True)
    # 排序
    full_country_stats.sort_values(by="GDP per capita", inplace=True)
    # 剔除不需要的行
    remove_indices = [0, 1, 6, 8, 33, 34, 35]
    keep_indices = list(set(range(36)) - set(remove_indices))
    # 按整数索引行
    return full_country_stats[["GDP per capita", 'Life satisfaction']].iloc[keep_indices]

# df.pivot()作用：
# Country  | INDICATOR | Value          Country  | Dwe....... | Housing .. | ...
# ----------------------------    ===》 ----------------------------------------
# Australia| Dwe...... | 1.10           Australia| 1.10       | 20.00      | ...
# Australia| Housing ..| 20.00          Austria  | ... 
# ...                                   ...
```

##### 保存图片:

```python
# Where to save the figures
PROJECT_ROOT_DIR = "."
CHAPTER_ID = "fundamentals"
IMAGES_PATH = os.path.join(PROJECT_ROOT_DIR, "images", CHAPTER_ID)
os.makedirs(IMAGES_PATH, exist_ok=True)

def save_fig(fig_id, tight_layout=True, fig_extension="png", resolution=300):
    path = os.path.join(IMAGES_PATH, fig_id + "." + fig_extension)
    print("Saving figure", fig_id)
    if tight_layout:
        plt.tight_layout()
    plt.savefig(path, format=fig_extension, dpi=resolution)
```

##### 在图片上加注释:

```python
sample_data.plot(kind='scatter', x="GDP per capita", y='Life satisfaction', figsize=(5,3))
plt.axis([0, 60000, 0, 10])
position_text = {
    "Hungary": (5000, 1), # 文本框左下角xy
    "Korea": (18000, 1.7),
    "France": (29000, 2.4),
    "Australia": (40000, 3.0),
    "United States": (52000, 3.8),
}
for country, pos_text in position_text.items():
    pos_data_x, pos_data_y = sample_data.loc[country]
    country = "U.S." if country == "United States" else country
    # xy：箭头位置， xytext：文本框位置，shrink：箭头缩短10%
    plt.annotate(country, xy=(pos_data_x, pos_data_y), xytext=pos_text,
            arrowprops=dict(facecolor='black', width=0.5, shrink=0.1, headwidth=5))
    plt.plot(pos_data_x, pos_data_y, "ro")
plt.xlabel("GDP per capita (USD)")
save_fig('money_happy_scatterplot')
plt.show()
```

{{< figure src="/handson-ml2-1/money_happy_scatterplot.png" class="center" width="400" >}}

如果数据集中某些样本缺少一些特征（比如5%的顾客没有声明年龄），可以尝试的方法：1）忽略所有样本的年龄，2）忽略缺少年龄特征的样本，3）补缺（比如年龄的中位数）4）训练两个模型，一个包含所有特征，一个不含缺失特征。

Holdout validation: 在training set上效果好的模型，不一定在新数据上表现好，所以我们需要训练集外的数据来评判模型泛化能力。在test set上表现好的模型也只能表示其在该特定test set上表现好，还需要第三个数据集客观评判。所以，常见的处理是把数据集分为training dataset，validation dataset，和test dataset。用training dataset训练模型，确定模型参数。用validation dataset挑选一系列模型（比如确定hyperparameter）。通过validation dataset上的表现，选出最优模型再在training+validation上训练。最后通过test dataset客观评价模型的泛化能力。（思考kaggle的open和private数据集）

