---
published: true
title: 数据挖掘期末复习汇总（四）——树
category: Data Mining
tags: 
  - DataMining
  - Tree
layout: post
excerpt_separator: <!--more-->

---

# 树

## Intuition  
![tree](/assets/images/201906DataMining/tree_pic01.png)

## 单棵树

### Information Gain  
>def: 拆分前和拆分后的熵的差值  
expected entropy drop after split  

$I=-(p_1 \log_2{p_1}+ ... +p_k \log_2{p_k})=-\sum_{i=1}^k p_i \log_2{p_i}$  
$Gain(X)=I(n,n_1)-E(X)$  
其中$I(n,n_1)=-(p \log_2{p}+(1-p) \log_2{(1-p)})$，$p=\frac{n_1}{n}$  
$E(X)=\frac{m_1}{n}\ast I(m_1,m_{11})+\frac{m_2}{n}\ast I(m_2,m_{21})+ ... + \frac{m_k}{n}\ast I(m_k,m_{k1})$  

<!--more-->
  
<br />  
* An Example  
![infogain](/assets/images/201906DataMining/information_gain.jpg)  
**拆分前**的熵值（不按年龄区分时，16个样本，4有12无）--$I(16,4)$  
$I(16,4)=-(4/16 * log_2{4/16}+12/16 * log_2{12/16})=0.8113$  
**拆分后**的熵值（按年龄区分，6/16在第一组，10/16在第二组，第一组1有5无，第二组3有10无）  
$E(年龄)=(6/16)*I(6,1)+(10/16)*I(10,3)=0.7946$  
$Gain(年龄)=I(16,4)-E(年龄)=0.0167$  
<font color='#9a4238'>信息增益越大越好 **等价于** 拆分后的熵值越小越好（拆分前的熵值对于所有下级节点而言都一样）</font>  


```R
I=function(n,a){
    out=-((a/n)*log(a/n)/log(2)+
         (1-a/n)*log(1-a/n)/log(2))
}
# I(16,4)
a=I(16,4);a
# E(年龄)
b=(6/16)*I(6,1)+(10/16)*I(10,3);b
# Gain(年龄)
gain1=a-b;gain1
```


0.811278124459133



0.794565220137316



0.0167129043218172



```R
gain2=I(16,4)-9/16*I(9,1)-7/16*I(7,3);gain2
gain3=I(16,4)-6/16*I(6,1)-7/16*I(7,2)-3/16*I(3,1);gain3
```


0.0971580016328469



0.0177239987078822


### Gini Index  


* the same example using Gini Index  
![info_gain](/assets/images/201906DataMining/information_gain.jpg)  

$Gini(split_j)=weighted\, sum\,  of\,  Gini(node)=\frac{n_1}{n} Gini(n_1,n_{11})+ ... +\frac{n_k}{n} Gini(n_k,n_{k1})$  
其中$Gini(n,a)=1-(\frac{a}{n})^2-(1-\frac{a}{n})^2$  
>worst case: $Gini(n,\frac{n}{2})=1-1/4-1/4=1/2$  
best case: $Gini(n,n)=Gini(n,0)=1-1-0=0$  
  
<font color='#9a4238'>$Gini$系数越小越好</font>  


```R
gini=function(n,a){
    out=1-(a/n)^2-(1-a/n)^2;
}
gini1=6/16*gini(6,1)+10/16*gini(10,3);gini1
gini2=9/16*gini(9,1)+7/16*gini(7,3);gini2
gini3=6/16*gini(6,1)+7/16*gini(7,2)+3/16*gini(3,1);gini3
```


0.366666666666667



0.325396825396825



0.366071428571429



```R
rm(list=ls())
library(rpart)
library(rpart.plot)

data(iris)
n=nrow(iris)
loc=sample(n,.7*n)
train=iris[loc,]
test=iris[-loc,]
head(train)
```


<table>
<thead><tr><th></th><th scope="col">Sepal.Length</th><th scope="col">Sepal.Width</th><th scope="col">Petal.Length</th><th scope="col">Petal.Width</th><th scope="col">Species</th></tr></thead>
<tbody>
	<tr><th scope="row">9</th><td>4.4       </td><td>2.9       </td><td>1.4       </td><td>0.2       </td><td>setosa    </td></tr>
	<tr><th scope="row">43</th><td>4.4       </td><td>3.2       </td><td>1.3       </td><td>0.2       </td><td>setosa    </td></tr>
	<tr><th scope="row">77</th><td>6.8       </td><td>2.8       </td><td>4.8       </td><td>1.4       </td><td>versicolor</td></tr>
	<tr><th scope="row">44</th><td>5.0       </td><td>3.5       </td><td>1.6       </td><td>0.6       </td><td>setosa    </td></tr>
	<tr><th scope="row">105</th><td>6.5       </td><td>3.0       </td><td>5.8       </td><td>2.2       </td><td>virginica </td></tr>
	<tr><th scope="row">87</th><td>6.7       </td><td>3.1       </td><td>4.7       </td><td>1.5       </td><td>versicolor</td></tr>
</tbody>
</table>



### 生成


```R
model1=rpart(Species~., data=train, control=rpart.control(cp=.05))
summary(model1)
```

    Call:
    rpart(formula = Species ~ ., data = train, control = rpart.control(cp = 0.05))
      n= 105 
    
             CP nsplit  rel error    xerror       xstd
    1 0.5294118      0 1.00000000 1.0882353 0.06873741
    2 0.4117647      1 0.47058824 0.5588235 0.07241452
    3 0.0500000      2 0.05882353 0.1323529 0.04218454
    
    Variable importance
     Petal.Width Petal.Length Sepal.Length  Sepal.Width 
              35           30           19           16 
    
    Node number 1: 105 observations,    complexity param=0.5294118
      predicted class=setosa      expected loss=0.647619  P(node) =1
        class counts:    37    36    32
       probabilities: 0.352 0.343 0.305 
      left son=2 (37 obs) right son=3 (68 obs)
      Primary splits:
          Petal.Length < 2.6  to the left,  improve=35.98431, (0 missing)
          Petal.Width  < 0.8  to the left,  improve=35.98431, (0 missing)
          Sepal.Length < 5.45 to the left,  improve=22.82005, (0 missing)
          Sepal.Width  < 3.15 to the right, improve=17.26667, (0 missing)
      Surrogate splits:
          Petal.Width  < 0.8  to the left,  agree=1.000, adj=1.000, (0 split)
          Sepal.Length < 5.45 to the left,  agree=0.905, adj=0.730, (0 split)
          Sepal.Width  < 3.15 to the right, agree=0.848, adj=0.568, (0 split)
    
    Node number 2: 37 observations
      predicted class=setosa      expected loss=0  P(node) =0.352381
        class counts:    37     0     0
       probabilities: 1.000 0.000 0.000 
    
    Node number 3: 68 observations,    complexity param=0.4117647
      predicted class=versicolor  expected loss=0.4705882  P(node) =0.647619
        class counts:     0    36    32
       probabilities: 0.000 0.529 0.471 
      left son=6 (40 obs) right son=7 (28 obs)
      Primary splits:
          Petal.Width  < 1.65 to the left,  improve=26.682350, (0 missing)
          Petal.Length < 4.75 to the left,  improve=24.857240, (0 missing)
          Sepal.Length < 5.75 to the left,  improve= 7.686275, (0 missing)
          Sepal.Width  < 3.15 to the left,  improve= 4.374156, (0 missing)
      Surrogate splits:
          Petal.Length < 5.05 to the left,  agree=0.882, adj=0.714, (0 split)
          Sepal.Length < 6.15 to the left,  agree=0.721, adj=0.321, (0 split)
          Sepal.Width  < 2.95 to the left,  agree=0.706, adj=0.286, (0 split)
    
    Node number 6: 40 observations
      predicted class=versicolor  expected loss=0.1  P(node) =0.3809524
        class counts:     0    36     4
       probabilities: 0.000 0.900 0.100 
    
    Node number 7: 28 observations
      predicted class=virginica   expected loss=0  P(node) =0.2666667
        class counts:     0     0    28
       probabilities: 0.000 0.000 1.000 
    
    


```R
# 输出节点（文字）
path.rpart(model1,node=c(1,2,3))

# 绘图法1
opt(6,4)
rpart.plot(model1,cex=.8)

# 绘图法2
if (FALSE){
    plot(model1,asp=4,compress=T)
    text(model1,cex=1,use.n=T)
}
```

    
     node number: 1 
       root
    
     node number: 2 
       root
       Petal.Length< 2.6
    
     node number: 3 
       root
       Petal.Length>=2.6
    


![png](/assets/images/201906DataMining/output_126_1.png)


### 剪枝

#### 预剪枝 pre-pruning  
通过提前停止树的构建对树剪枝，一旦停止，节点就是树叶，该树叶持有子集元素中最频繁的类。停止决策树生长最简单的方法有：  
>1. 定义一个高度，当决策树达到该高度时就停止决策树的生长
2. 达到某个节点的实例具有相同的特征向量，及时这些实例不属于同一类，也可以停止决策树的生长。这个方法对于处理数据的数据冲突问题比较有效。
3. 定义一个阈值，当达到某个节点的实例个数小于阈值时就可以停止决策树的生长
4. 定义一个阈值，通过计算每次扩张对系统性能的增益(例如information gain > c?)，并比较增益值与该阈值大小来决定是否停止决策树的生长。


#### 后剪枝 post-pruning  
首先**构造完整的决策树，允许树过度拟合训练数据**，然后对那些置信度不够的结点子树用叶子结点来代替，该叶子的类标号用该结点子树中最频繁的类标记。相比于先剪枝，这种方法更常用，正是因为在先剪枝方法中精确地估计何时停止树增长很困难。常见的后剪枝方法：  
>1. Reduced-Error Pruning(REP,错误率降低剪枝）
2. Pesimistic-Error Pruning(PEP,悲观错误剪枝）
3. Cost-Complexity Pruning（CCP，代价复杂度剪枝)
4. EBP(Error-Based Pruning)（基于错误的剪枝）  
<br />  

* Reduced Error Pruning (REP)  
REP方法是一种比较简单的后剪枝的方法，在该方法中，可用的数据被分成两个样例集合：**一个训练集用来形成学习到的决策树，一个分离的验证集用来评估这个决策树在后续数据上的精度**，确切地说是用来评估修剪这个决策树的影响。  
这个方法的动机是：即使学习器可能会被训练集中的随机错误和巧合规律所误导，但**验证集合不大可能表现出同样的随机波动**。所以验证集可以用来对过度拟合训练集中的虚假特征提供防护检验。  
REP是最简单的后剪枝方法之一，不过由于使用独立的测试集，原始决策树相比，修改后的决策树**可能偏向于过度修剪**。
<br />  
  
* Minimal cost-complexity  pruning 代价-复杂度剪枝  
$R_\alpha (T) = R(T)+\alpha |\tilde{T}|$  
其中$T$代表树模型，$R(T)$是误差大小，$|\tilde{T}|$表示树的叶节点个数，即表示树的复杂度。  
$\alpha$是对复杂度的惩罚系数，可以由用户设置：  
 * $\alpha$越大，叶节点的个数对损失函数的影响越大，剪枝之后的决策树更容易选择复杂度较小的树；
 * $\alpha$越小，表示叶节点的个数对损失函数的影响越小。



* CCP算法的步骤  
 1. 对于完全决策树$𝑇$的每个非叶结点计算$\alpha$值，循环剪掉具有最小$\alpha$值的子树，直到剩下根节点。在该步可得到**一系列的剪枝树$｛𝑇_0，𝑇_1，𝑇_2, …, 𝑇_𝑚｝$**,其中$𝑇_0$为原有的完全决策树，$𝑇_𝑚$为根结点，$𝑇_(𝑖+1)$为对$𝑇_𝑖$进行剪枝的结果；
 2. **从子树序列中**，根据真实的误差估计选择最佳决策树。

#### 预剪枝和后剪枝的比较
* 后剪枝决策树通常比预剪枝决策树保留了更多的分支；
* 后剪枝决策树的欠拟合风险很小，泛化性能往往优于预剪枝决策树；
* 后剪枝决策树训练时间开销比未剪枝决策树和预剪枝决策树都要大的多。

#### 一倍标准差规则  
在剪枝理论中，比较著名的规则就是1-SE（1标准差）规则。  
首先要保证预测误差（通过交叉验证获得，在程序中表示为xerror）尽量小，但不一定要取最小值，而是允许它在“最小的误差±一个标准差”的范围内，然后在此范围内选取尽量小的复杂性参量，进而以它为依据进行剪枝。  
这个规则体现了**兼顾树的规模（复杂性）和误差大小的思想**。一般说来，随着拆分的增多，复杂性参量会单调下降（纯度越来越高），但是预测误差则会先降后升，这样，我们就**无法使复杂性和误差同时降到最低**，因此允许误差可以在一个标准差内波动。

#### 剪枝的R实现


```R
if (FALSE){ #法1
    model2=snip.rpart(model1,toss=2)
    opt(6,4)
    rpart.plot(model2)
}
if (TRUE) { #法2
    model2=prune(model1, cp=.01)
    opt(6,4)
    rpart.plot(model2,cex=.8)
}
```


![png](/assets/images/201906DataMining/output_134_0.png)


### 检验


```R
# 混淆矩阵
class=predict(model2, newdata=test, type="class")
table(class, test$Species, dnn=c("Pred","True"))
```


                True
    Pred         setosa versicolor virginica
      setosa         12          0         0
      versicolor      0         14         2
      virginica       0          1        16


多棵树：掌握基本思想、适用情况（强分类器？弱分类器？多弱多强？）

## boosting

Gradient Boosting | R - https://blog.csdn.net/dingming001/article/details/72989547  
XGBoost | R - https://blog.csdn.net/qq_26074991/article/details/51737030  
XGBoost - https://blog.csdn.net/qq_34264472/article/details/53363384  
XGBoost - https://blog.csdn.net/langyichao1/article/details/70183127

## bagging


**步骤**  
设训练样本集合L为${(x_n,y_n),n=1,2,3,...,N}$，$x_n$为$p$维向量，$y_n$为因变量，是取值${1,2,...,J}$的分类变量。  
构建$M$棵决策树$H_m(x,L_m),m=1,2,...,M$，组合$M$棵树得到最终分类器$H_{agg}$。  
  
1. 对样本集合$L$进行Bootstrap抽样得到训练样本集$L_m$  
>对${(x_n,y_n),n=1,2,...,N}$中的$N$个样本点进行概率为$\frac{1}{N}$的等概率有放回抽样，样本量为$N$。  

对$L_m$构建分类器（决策树）$H_m(x,Lm)$  
2. 组合$M$棵决策树得到最终分类器$H_B$，$H_B$对样本x的预测为$argmax_j \sum_{m=1}^M {I(H_m(x,L_m)=j)}$, $I()$为示性函数  

* 如果生成基预测器的算法是不稳定的(unstable)，通过bagging得到的最终预测模型虽然失去了简单的可解释的树型结构，但预测精度往往会大大高于单个基预测器的预测精度。  
* Bagging算法对于基预测器不稳定的情况很有作用，对于稳定的基预测器，bagging并不有效。  
* 从偏差方差分解的角度看，Bagging算法可以提高不稳定基预测器的预测精度，实质上是减小了预测的方差(variance)，但并没有降低偏差(bias)。  
> * 不稳定的算法: 决策树，神经网络，MARS (multivariate splines)，和子集回归(subset regression)等等；  
* 稳定的算法: 岭回归(ridge regression)，最近邻方法(K-nearest neighbor)，和线性判别方法(Linear discriminate)等等。  


* 每次进行bootstrap抽样的时候，我们选择的样本量相等于原始训练集的样本量。因为bootstrap是有放回的重复抽样，所以有些样本点被抽中的次数超过一次，有些样本点没有被抽中。根据bootstrap抽样的理论，当样本量为$N$时，大约有37%的样本点没有被抽中。  
* 研究表明，提高bootstrap抽样的样本量并不能改善bagging算法的预测精度。  

## AdaBoost

自从AdaBoost 方法提出之后，大量模拟和应用结果显示该方法可以大大降低基分类器(比如决策树)的预测误差，Breiman(1996)曾指出AdaBoost 方法是现有的分类方法中最好的一种。Breiman(1996)认为最主要的原因是AdaBoost方法的**自适应的重新抽样的方法**，而不是其具体定义的更新p(n)的公式。

<font color='#9a4238'>二分类Adaboost算法是最小化指数损失$L(y,f(x))=\exp{(-yf(x))}$的分步向前可加模型。</font>


```R
rm(list=ls())
library(adabag)

data(iris)
n=nrow(iris)
loc=sample(n, .7*n)
train=iris[loc,]
test=iris[-loc,]
```

    Warning message:
    "package 'adabag' was built under R version 3.5.3"Loading required package: rpart
    Loading required package: caret
    Warning message:
    "package 'caret' was built under R version 3.5.3"Loading required package: lattice
    Loading required package: ggplot2
    Loading required package: foreach
    Warning message:
    "package 'foreach' was built under R version 3.5.3"Loading required package: doParallel
    Warning message:
    "package 'doParallel' was built under R version 3.5.3"Loading required package: iterators
    Warning message:
    "package 'iterators' was built under R version 3.5.3"Loading required package: parallel
    


```R
if (TRUE){
    err=c()
    for (i in 1:20){ #基分类器个数从1到20
      print(paste0("i=",i))
      iada=boosting(Species~., data=train, mfinal=i)
      ipred=predict.boosting(iada, newdata=test)
      err=c(err, ipred$error)
    }
    derr=data.frame(i=1:20, error=err); derr
}

mycol=RColorBrewer::brewer.pal(5,"Set1")[2]
fig0=ggplot(derr)+
  theme_bw()+
  geom_point(aes(i, error), shape=19, size=2, col=mycol)+
  geom_line(aes(i, error), col=mycol)+
  scale_x_continuous(breaks=seq(1,20,1))+
  xlab("The Number of Basic Classifiers")+ylab("Error")
fig0
```


<table>
<thead><tr><th scope="col">i</th><th scope="col">error</th></tr></thead>
<tbody>
	<tr><td> 1        </td><td>0.06666667</td></tr>
	<tr><td> 2        </td><td>0.02222222</td></tr>
	<tr><td> 3        </td><td>0.06666667</td></tr>
	<tr><td> 4        </td><td>0.06666667</td></tr>
	<tr><td> 5        </td><td>0.02222222</td></tr>
	<tr><td> 6        </td><td>0.04444444</td></tr>
	<tr><td> 7        </td><td>0.02222222</td></tr>
	<tr><td> 8        </td><td>0.06666667</td></tr>
	<tr><td> 9        </td><td>0.02222222</td></tr>
	<tr><td>10        </td><td>0.04444444</td></tr>
	<tr><td>11        </td><td>0.08888889</td></tr>
	<tr><td>12        </td><td>0.06666667</td></tr>
	<tr><td>13        </td><td>0.04444444</td></tr>
	<tr><td>14        </td><td>0.06666667</td></tr>
	<tr><td>15        </td><td>0.06666667</td></tr>
	<tr><td>16        </td><td>0.06666667</td></tr>
	<tr><td>17        </td><td>0.04444444</td></tr>
	<tr><td>18        </td><td>0.04444444</td></tr>
	<tr><td>19        </td><td>0.04444444</td></tr>
	<tr><td>20        </td><td>0.04444444</td></tr>
</tbody>
</table>






![png](/assets/images/201906DataMining/output_148_2.png)



```R
#选取i=9的模型
iada=boosting(Species~., data=train, mfinal=10)
ipred=predict.boosting(iada, newdata=test)

ipred$confusion #混淆矩阵
#相当于table(dft$Species1,dft$Species0, dnn=c("Observed","Predicted"))
```


                   Observed Class
    Predicted Class setosa versicolor virginica
         setosa         15          0         0
         versicolor      0         11         3
         virginica       0          0        16



```R
importanceplot(iada)
```


![png](/assets/images/201906DataMining/output_150_0.png)


## Random Forest

与bagging只在数据（行）的层面上随机不同，<font color='#9a4238'>随机森林在变量（列）的使用和数据（行）的使用上都进行随机化，生成多颗分类树，最后汇总其结果。</font> 这样随机森林算法就可以**降低树与树之间的相关关系**，并且尽可能的控制平均方差。  

<br />  
对于每棵树$𝑏=1,2,…,𝐵$,  
1. 从全部训练样本单元中，采用bootstrap抽样方法抽取n个样本单元构成bootstrap样本集 $𝑍^∗$, 基于$𝑍^∗$构造一棵树$𝐻_𝑏$，  
2. 对树上的**每个节点**，重复以下步骤，直到节点的样本数达到指定的最小限定值𝑛_𝑚𝑖𝑛：  
 * 从全部p个变量中随机选取m个；
 * 从m个随机变量中取最佳的分枝变量；
 * 在这一节点分裂成两个子节点。  
 
<br />
 随机森林在运算量没有显著提高的前提下提高了预测精度。随机森林对多元线性不敏感，结果对缺失数据和非平衡的数据比较稳健，可以很好地预测多达几千个解释变量的作用（适用于变量多情形）。  


```R
rm(list=ls())
library(randomForest)

data(iris)
n=nrow(iris)
loc=sample(n,.7*n)
train=iris[loc,]
test=iris[-loc,]
```


```R
# set.seed(71) # why do we need to set seed here?
iris.rf <- randomForest(Species ~ ., data=train, importance=TRUE, proximity=TRUE)
print(iris.rf)
```

    
    Call:
     randomForest(formula = Species ~ ., data = train, importance = TRUE,      proximity = TRUE) 
                   Type of random forest: classification
                         Number of trees: 500
    No. of variables tried at each split: 2
    
            OOB estimate of  error rate: 3.81%
    Confusion matrix:
               setosa versicolor virginica class.error
    setosa         34          0         0  0.00000000
    versicolor      0         35         2  0.05405405
    virginica       0          2        32  0.05882353
    


```R
#Look at variable importance:
round(importance(iris.rf), 2)
```


<table>
<thead><tr><th></th><th scope="col">setosa</th><th scope="col">versicolor</th><th scope="col">virginica</th><th scope="col">MeanDecreaseAccuracy</th><th scope="col">MeanDecreaseGini</th></tr></thead>
<tbody>
	<tr><th scope="row">Sepal.Length</th><td> 6.74</td><td> 8.64</td><td> 6.16</td><td>10.42</td><td> 6.41</td></tr>
	<tr><th scope="row">Sepal.Width</th><td> 3.98</td><td> 1.87</td><td> 0.93</td><td> 3.15</td><td> 1.51</td></tr>
	<tr><th scope="row">Petal.Length</th><td>22.24</td><td>31.32</td><td>26.03</td><td>33.15</td><td>31.05</td></tr>
	<tr><th scope="row">Petal.Width</th><td>22.14</td><td>32.65</td><td>30.06</td><td>34.41</td><td>30.34</td></tr>
</tbody>
</table>




```R
ipred=predict(iris.rf, newdata=test)
table(ipred, test$Species, dnn=c("Pred","True"))
```


                True
    Pred         setosa versicolor virginica
      setosa         16          0         0
      versicolor      0         11         0
      virginica       0          2        16
