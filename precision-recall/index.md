# Precision和Recall


## 什么是精确率（precision）与召回率（recall）？

Precision和Recall是衡量模型预测能力的指标。在简单的分类问题（种类数=2）中，根据实际答案与模型预测，可分为如下4种情况：

* True Positive (TP) - 正确预测了阳类
* True Negative (TN) - 正确预测了阴类
* False Positive (FP) - 错误预测了阳类（实际为阴类）
* False Negative (FN) - 错误预测了阴类（实际为阳类）

注：True和False表示预测是否正确，Positive和Negative表示预测结果

精准率和召回率定义：

Precision = TP / (TP + FP)

Recall = TP / (TP + FN)

他们区别在分母不同，所取值范围均为0～1

## 如何理解Precision和Recall？
信息检索中，Precision又叫查准率（找得对），Recall又叫查全率（找得全）。

举个例子，池塘有大龙虾30只，小龙虾70只。我们发明了一只捕大龙虾神器专捕大龙虾。神器一网下去，捕到大龙虾20只，小龙虾5只。那么：

精准率 = 20 /（20 + 5） = 0.80

召回率 = 20 / 30 = 0.67

我们来看3个极端神器的Precision和Recall。

* 神器谨慎版：只捕了一只，且为大龙虾。Precision = 1/1 = 1.0, Recall = 1/30 = 0.033
* 神器电鱼版：捕了全池塘的100只。Precision = 30/100 = 0.3, Recall = 30/30 = 1.0
* 神器专业版：补了30只，且全为大龙虾。Precision = 30/30 = 1.0, Recall = 30/30 = 1.0

实际应用中，Precision和Recall往往不能兼得。有时我们更在意Precision，而有时我们更在意Recall。比如判断视频是否适合儿童观看（适合-正，不适合-负），我们更青睐high precision - low recall的分类器（声称安全的视频真是安全的，但可能也拦截了其他安全的视频）。再比如报警器判断是否有小偷（有小偷-正，无小偷-负），我们更青睐low precision - high recall的警报器（声称有小偷的时候可能是误报，但是真有小偷的时候它通常会响）。

## PR曲线

机器学习模型往往先预测概率（scoring），再根据概率决策分类（label）。比如score高于threshold输出positive，低于输出negative。这样Precision与Recall将是Threshold的函数，即P(t)和R(t)。如果把P(t)和R(t)作为坐标画在坐标中,就形成了PR曲线，其中(1,1)为理想点。我们可以用PR曲线下的面积（AUPR）来衡量模型表现。


{{< figure src="https://3qeqpr26caki16dnhd19sv6by6v-wpengine.netdna-ssl.com/wp-content/uploads/2018/08/Precision-Recall-Plot-for-a-No-Skill-Classifier-and-a-Logistic-Regression-Model4.png" class="center" width="500" >}}

相比于ROC曲线，PR曲线对不平衡数据集的性能描述更为准确。


## 参考

[如何解释召回率与精确率？ - 知乎](https://www.zhihu.com/question/19645541)

[推荐系统评测指标—准确率(Precision)、召回率(Recall)、F值(F-Measure)](http://bookshadow.com/weblog/2014/06/10/precision-recall-f-measure/)











