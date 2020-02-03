---
published: true
title: 数据挖掘期末复习汇总
category: Data Mining
tags: 
  - DataMining
  - 课内
layout: post

---

```R
library(ggplot2)
library(ggthemes)
opt=function(wid,hei) {options(repr.plot.width=wid,repr.plot.height=hei)}
# color - bright lemon - #F8D86A
```

# General View 

> * 不稳定的算法: 决策树，神经网络，MARS (multivariate splines)，和子集回归(subset regression)等等；  
* 稳定的算法: 岭回归(ridge regression)，最近邻方法(K-nearest neighbor)，和线性判别方法(Linear discriminate)等等。

## Classification Methods
FAMILY of classification methods!  
 * Logistic Regression  
 * Clustering (K Nearest Neighbors etc.)   
 * Decision Tress  
 * Naive Bayes?  
 * SVM  
 * Neural Networks  
 ![class](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/class_pic01.png)  
 SVM使用了非线性分类器，而logistics和decision tree都是线性的？

## 损失函数
通常机器学习每一个算法中都会有一个目标函数，算法的求解过程是通过对这个目标函数优化的过程。在分类或者回归问题中，通常使用损失函数作为其目标函数。损失函数用来评价模型的预测值和真实值不一样的程度，损失函数越好，通常模型的性能越好。不同的算法使用的损失函数不一样。  
<br />

<font color='#F8D86A'>损失函数分为经验风险损失函数和结构风险损失函数。经验风险损失函数指预测结果和实际结果的差别，结构风险损失函数是指经验风险损失函数加上正则项</font>。通常表示为如下：  
$\theta^* = argmin \frac{1}{N} \sum_{i=1}^N L(y_i,f(x_i, \theta_i)+\lambda\Phi(\theta))$  
其中$\frac{1}{N} \sum_{i=1}^N L(y_i,f(x_i, \theta_i)$是经验风险函数，$L(\dot{})$表示损失函数  
$\lambda\Phi(\theta)$是正则化项(regularizer)或惩罚项(penalty term)，它可以是$L1$、$L2$或者其他正则函数。  

**常见的损失函数**  
1. 0-1损失函数  
$L(Y,f(X))=\begin{cases}
  1,Y\neq f(X)\\
  0,Y= f(X)
\end{cases}$  
感知机用的是这种损失函数，但相等这一条件过于严格，可以放宽到满足$|Y-f(X)|<T$时认为相等  

2. 绝对值损失函数  
$L(Y,f(X))=|Y-f(X)|$  
  * 最小一乘法  
3. log对数损失函数  
$L(Y,P(Y|X))=-\log{P(Y|X)}$  
  * logistic回归的损失函数就是对数损失函数  
4. 指数损失函数  
标准形式：$L(Y,f(X))=\exp{(-Y f(X))}$  
  * AdaBoost采用指数损失函数  
5. 平方损失函数  
$L(Y,f(X))=\sum_N{(Y-f(X))^2}$  
  * 最小二乘法  
6. Hinge Loss 
$L(y)=\max{(0,1-ty)}$  
  * 用于最大间隔分类，例如支持向量机SVM  


# 多元回归的变量选择  
<font color=#F8D864>**简单考，选择判断**</font>  
1. 多重共线性的定义  
 * 共线性：某一对自变量的相关系数很高(say 0.98)
 * 多重共线性：虽然没有任何一对极高的相关系数，但多对自变量之间的相关系数都充分高（say 0.85），也即，虽然ind3~ind1的拟合效果不佳（ind1无法充分解释ind3的方差），ind3~ind1+ind2的拟合效果却很好（ind3造成的方差可以充分被ind1和ind2解释/accounted for）
2. 多重共线性的出现  
 * F检验通过，T检验不通过
 >一元回归中F和T检验等价，但多元回归中F检验用来检验总体回归的显著性，而T检验是检验各个参数各自的显著性  
 * 回归系数t检验不显著，有些系数absurdly变号  
 * 显著性水平下降？（R^2很高)
 * 标准差膨胀（VIF）
 * 增删一个自变量导致模型发生显著的变化
 * 【worst case】模型不收敛，no solution  
3. 多重共线性的度量
 * Tolerance  
 $Tolerance=1-R^2$  
 越大越好  
 $<0.1(或0.2)$时可能存在多重共线性问题  
 * VIF(variance inflation factor)  
 $VIF=\frac{1}{Tolearance}=\frac{1}{1-R^2}$  
 越小越好  
 $>10(或4)$时可能存在多重共线性问题  
 直观理解：$VIF=10$ 说明标准差(standard error）会膨胀10倍（相较无多重共线性的情况）  
4. 多重共线性的解决方法  
 * 删去一些多余的变量：子集回归、向前向后逐步回归   
 * 将相似的变量予以加总再引入模型
 * 增大样本容量（控制$R^2$等？）




```R
data(mtcars)
str(mtcars)
```

    'data.frame':	32 obs. of  11 variables:
     $ mpg : num  21 21 22.8 21.4 18.7 18.1 14.3 24.4 22.8 19.2 ...
     $ cyl : num  6 6 4 6 8 6 8 4 4 6 ...
     $ disp: num  160 160 108 258 360 ...
     $ hp  : num  110 110 93 110 175 105 245 62 95 123 ...
     $ drat: num  3.9 3.9 3.85 3.08 3.15 2.76 3.21 3.69 3.92 3.92 ...
     $ wt  : num  2.62 2.88 2.32 3.21 3.44 ...
     $ qsec: num  16.5 17 18.6 19.4 17 ...
     $ vs  : num  0 0 1 1 0 1 0 1 1 1 ...
     $ am  : num  1 1 1 0 0 0 0 0 0 0 ...
     $ gear: num  4 4 4 3 3 3 3 4 4 4 ...
     $ carb: num  4 4 1 1 2 1 4 2 2 4 ...
    


```R
mfull=lm(mpg~.,data=mtcars)
summary(mfull)
```


    
    Call:
    lm(formula = mpg ~ ., data = mtcars)
    
    Residuals:
        Min      1Q  Median      3Q     Max 
    -3.4506 -1.6044 -0.1196  1.2193  4.6271 
    
    Coefficients:
                Estimate Std. Error t value Pr(>|t|)  
    (Intercept) 12.30337   18.71788   0.657   0.5181  
    cyl         -0.11144    1.04502  -0.107   0.9161  
    disp         0.01334    0.01786   0.747   0.4635  
    hp          -0.02148    0.02177  -0.987   0.3350  
    drat         0.78711    1.63537   0.481   0.6353  
    wt          -3.71530    1.89441  -1.961   0.0633 .
    qsec         0.82104    0.73084   1.123   0.2739  
    vs           0.31776    2.10451   0.151   0.8814  
    am           2.52023    2.05665   1.225   0.2340  
    gear         0.65541    1.49326   0.439   0.6652  
    carb        -0.19942    0.82875  -0.241   0.8122  
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 2.65 on 21 degrees of freedom
    Multiple R-squared:  0.869,	Adjusted R-squared:  0.8066 
    F-statistic: 13.93 on 10 and 21 DF,  p-value: 3.793e-07
    


note that AIC & BIC: the lower the merrier


```R
step(mfull, direction="backward")
```

    Start:  AIC=70.9
    mpg ~ cyl + disp + hp + drat + wt + qsec + vs + am + gear + carb
    
           Df Sum of Sq    RSS    AIC
    - cyl   1    0.0799 147.57 68.915
    - vs    1    0.1601 147.66 68.932
    - carb  1    0.4067 147.90 68.986
    - gear  1    1.3531 148.85 69.190
    - drat  1    1.6270 149.12 69.249
    - disp  1    3.9167 151.41 69.736
    - hp    1    6.8399 154.33 70.348
    - qsec  1    8.8641 156.36 70.765
    <none>              147.49 70.898
    - am    1   10.5467 158.04 71.108
    - wt    1   27.0144 174.51 74.280
    
    Step:  AIC=68.92
    mpg ~ disp + hp + drat + wt + qsec + vs + am + gear + carb
    
           Df Sum of Sq    RSS    AIC
    - vs    1    0.2685 147.84 66.973
    - carb  1    0.5201 148.09 67.028
    - gear  1    1.8211 149.40 67.308
    - drat  1    1.9826 149.56 67.342
    - disp  1    3.9009 151.47 67.750
    - hp    1    7.3632 154.94 68.473
    <none>              147.57 68.915
    - qsec  1   10.0933 157.67 69.032
    - am    1   11.8359 159.41 69.384
    - wt    1   27.0280 174.60 72.297
    
    Step:  AIC=66.97
    mpg ~ disp + hp + drat + wt + qsec + am + gear + carb
    
           Df Sum of Sq    RSS    AIC
    - carb  1    0.6855 148.53 65.121
    - gear  1    2.1437 149.99 65.434
    - drat  1    2.2139 150.06 65.449
    - disp  1    3.6467 151.49 65.753
    - hp    1    7.1060 154.95 66.475
    <none>              147.84 66.973
    - am    1   11.5694 159.41 67.384
    - qsec  1   15.6830 163.53 68.200
    - wt    1   27.3799 175.22 70.410
    
    Step:  AIC=65.12
    mpg ~ disp + hp + drat + wt + qsec + am + gear
    
           Df Sum of Sq    RSS    AIC
    - gear  1     1.565 150.09 63.457
    - drat  1     1.932 150.46 63.535
    <none>              148.53 65.121
    - disp  1    10.110 158.64 65.229
    - am    1    12.323 160.85 65.672
    - hp    1    14.826 163.35 66.166
    - qsec  1    26.408 174.94 68.358
    - wt    1    69.127 217.66 75.350
    
    Step:  AIC=63.46
    mpg ~ disp + hp + drat + wt + qsec + am
    
           Df Sum of Sq    RSS    AIC
    - drat  1     3.345 153.44 62.162
    - disp  1     8.545 158.64 63.229
    <none>              150.09 63.457
    - hp    1    13.285 163.38 64.171
    - am    1    20.036 170.13 65.466
    - qsec  1    25.574 175.67 66.491
    - wt    1    67.572 217.66 73.351
    
    Step:  AIC=62.16
    mpg ~ disp + hp + wt + qsec + am
    
           Df Sum of Sq    RSS    AIC
    - disp  1     6.629 160.07 61.515
    <none>              153.44 62.162
    - hp    1    12.572 166.01 62.682
    - qsec  1    26.470 179.91 65.255
    - am    1    32.198 185.63 66.258
    - wt    1    69.043 222.48 72.051
    
    Step:  AIC=61.52
    mpg ~ hp + wt + qsec + am
    
           Df Sum of Sq    RSS    AIC
    - hp    1     9.219 169.29 61.307
    <none>              160.07 61.515
    - qsec  1    20.225 180.29 63.323
    - am    1    25.993 186.06 64.331
    - wt    1    78.494 238.56 72.284
    
    Step:  AIC=61.31
    mpg ~ wt + qsec + am
    
           Df Sum of Sq    RSS    AIC
    <none>              169.29 61.307
    - am    1    26.178 195.46 63.908
    - qsec  1   109.034 278.32 75.217
    - wt    1   183.347 352.63 82.790
    


    
    Call:
    lm(formula = mpg ~ wt + qsec + am, data = mtcars)
    
    Coefficients:
    (Intercept)           wt         qsec           am  
          9.618       -3.917        1.226        2.936  
    



```R
mmin=lm(mpg~1, data=mtcars)
step(mmin, direction='forward',scope=~cyl+disp+hp+drat+wt+qsec+vs+am+gear+carb,k=2)
```

    Start:  AIC=115.94
    mpg ~ 1
    
           Df Sum of Sq     RSS     AIC
    + wt    1    847.73  278.32  73.217
    + cyl   1    817.71  308.33  76.494
    + disp  1    808.89  317.16  77.397
    + hp    1    678.37  447.67  88.427
    + drat  1    522.48  603.57  97.988
    + vs    1    496.53  629.52  99.335
    + am    1    405.15  720.90 103.672
    + carb  1    341.78  784.27 106.369
    + gear  1    259.75  866.30 109.552
    + qsec  1    197.39  928.66 111.776
    <none>              1126.05 115.943
    
    Step:  AIC=73.22
    mpg ~ wt
    
           Df Sum of Sq    RSS    AIC
    + cyl   1    87.150 191.17 63.198
    + hp    1    83.274 195.05 63.840
    + qsec  1    82.858 195.46 63.908
    + vs    1    54.228 224.09 68.283
    + carb  1    44.602 233.72 69.628
    + disp  1    31.639 246.68 71.356
    <none>              278.32 73.217
    + drat  1     9.081 269.24 74.156
    + gear  1     1.137 277.19 75.086
    + am    1     0.002 278.32 75.217
    
    Step:  AIC=63.2
    mpg ~ wt + cyl
    
           Df Sum of Sq    RSS    AIC
    + hp    1   14.5514 176.62 62.665
    + carb  1   13.7724 177.40 62.805
    <none>              191.17 63.198
    + qsec  1   10.5674 180.60 63.378
    + gear  1    3.0281 188.14 64.687
    + disp  1    2.6796 188.49 64.746
    + vs    1    0.7059 190.47 65.080
    + am    1    0.1249 191.05 65.177
    + drat  1    0.0010 191.17 65.198
    
    Step:  AIC=62.66
    mpg ~ wt + cyl + hp
    
           Df Sum of Sq    RSS    AIC
    <none>              176.62 62.665
    + am    1    6.6228 170.00 63.442
    + disp  1    6.1762 170.44 63.526
    + carb  1    2.5187 174.10 64.205
    + drat  1    2.2453 174.38 64.255
    + qsec  1    1.4010 175.22 64.410
    + gear  1    0.8558 175.76 64.509
    + vs    1    0.0599 176.56 64.654
    


    
    Call:
    lm(formula = mpg ~ wt + cyl + hp, data = mtcars)
    
    Coefficients:
    (Intercept)           wt          cyl           hp  
       38.75179     -3.16697     -0.94162     -0.01804  
    



```R
library(leaps)
msub=regsubsets(mpg~., data=mtcars)
plot(msub)
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_11_0.png)


# Ridge & Lasso回归

解决多重共线性 - 岭回归和Lasso https://blog.csdn.net/bbbeoy/article/details/72526164  
Lasso - https://blog.csdn.net/qq_38650208/article/details/88766738 [讲得很清楚]  
Lars - https://blog.csdn.net/nya0731/article/details/79895307  
 https://blog.csdn.net/mousever/article/details/50513525


岭回归和Lasso回归在经验损失函数(adopted by OLS)的基础上加入了正则项/惩罚项，以适当的缩小系数，甚至缩小至0从而减少自变量的个数（Lasso）。  
* Hence, they have more bias than the least squared methods, but less variance  
* in other words, they provide a slightly worse fit with much better long-term prediction  

<br />  
  
traditional ***least squared regression*** seeks to minimize **the sum of the squared residual**  
$$\sum_{i=1}^{n}(\hat{y_i}-y_i)^2$$  

## Ridge Regression  
go to https://www.youtube.com/watch?v=Q81RR3yKn30  
<br />  

**Ridge regression** adds **a penalty to the slope**  on top of that:  
$$\sum_{i=1}^{n}(\hat{y_i}-y_i)^2 + \lambda\ast coef^2, \quad where\, \lambda \in [0,+\inf) $$  
$\lambda$ here is an important parameter, and is determined through **cross-validation**  
when $\lambda = 0$ ridge reg is no different from least squared reg  

>Why add penalties to the slope, though?  
 -- small sample size can lead to poorly fitted least squared reg (though the results look well for the **training set**, it doesn't do well for the **testing set**)  类似于overfitting   
 -- when the slope of the line is steep, the prediction for y is very sensitive to relatively small changes in x  
 -- ridge reg basically **shrink the paramiters**  
 
>if there is a lot of independent variables i.e. a lot of parameters/ coefficients to be estimated  
 ridge reg minimize $\lambda \ast (coef_1^2+coef_2^2+...+coef_p^2)$  
 note that the **y-intercept** is not included  
 
>Ridge Reg can also be applied to logistics regression  
 * because logit reg is solved using maximum likelihood rather than least squared, it tends to optimize(maximize) the sum of the likelihood  
 * ridge reg applied to logit tends to optimize the sum of the likelihood + $\lambda \ast coef^2$  
 
 > one REALLY COOL thing about ridge reg  
 -- we know that to estimate a model with 2 variables through least squared, there needs to be at least 2 obs in the datasets  
 to estimate a model with 10000 avriables through least squared (e.g. the influence of 10000 genes on the size of mice), there needs to be at leasts 10000 obs, which is very unlikely  
 but <font color='#F8D86A'> with Ridge Reg we can estimate the model with 10000 variables using only 500 or less obs  
 to summ up, Ridge Reg allows us to estimate a model where p>n or p>>n 高维数据集! </font> 
 how? think about it -- cross-validation has a lot to do with it.
 

## LASSO  
The Least Absolute **Shrinkage and Selectionator** Operator  
  
Lasso回归模型是常用线性回归的模型，当模型维度较高时，Lasso算法通过求解**稀疏解**对模型进行**变量选择**。Lars算法则提供了一种快速求解该模型的方法。  

like ridge reg, it adds a penalty to the size of parameters, but in diff forms  
$$\sum_{i=1}^{n}(\hat{y_i}-y_i)^2 + \lambda\times |coef|, where \lambda \in [0,+\inf) $$  
$\lambda$ is again determined using cross-validation  
  
more variables?  
$\lambda \times (|coef_1|+|coef_2|+...+|coef_p|)$  
  

## summing up about Lasso and Rdige  
Lasso回归和Ridge回归都是**Elastic Net广义线性模型的特例**。  
参数$\alpha$控制对高相关性数据时建模的形状。
* Lasso回归，$\alpha$=1(R语言glmnet的默认值),  
* Rigde回归，$\alpha$=0，  
* 一般的elastic net 0<$\alpha$<1.    
  
Lasso回归和Ridge回归**复杂度调整的程度由参数$\lambda$来控制**，<font color='#F8D86A'>$\lambda$越大模型复杂度的惩罚力度越大，从而获得一个较少变量的模型。</font>  

从向量选择的角度直观地理解Lasso回归和Lars  
  
最基础的向量选择模型：stepwise  
->ridge 惩罚和lasso 惩罚的不同  
->lasso求解系数的方法：lars  
  
同：
 * 二者都是有偏估计  
  
异：
 * Lasso构造的是一阶惩罚函数（绝对值形式），从而其约束条件更为尖锐，回归的估计参数更易为0，所以可以使一些变量系数为0，**得到的模型更简洁**；而岭回归系数为0的可能性很小。  
 * **比起岭回归，Lasso 收敛更快。**

LARS是一个适用于高维数据的回归算法。  
  
* 优点：  
 * 特别适合于特征维度p远高于样本数n的情况。  
 * 算法的最坏计算复杂度和最小二乘法类似，但是其计算速度几乎和前向选择算法一样  
 * 可以产生分段线性结果的完整路径，这在模型的交叉验证中极为有用  

* 缺点：  
 * 由于LARS的迭代方向是根据目标的残差而定，所以该算法对样本的噪声极为敏感。


```R
library(lars)
data(diabetes)

length(diabetes$y)
dim(diabetes$x)
dim(diabetes$x2)

colnames(diabetes$x)
colnames(diabetes$x2)
```

    Warning message:
    "package 'lars' was built under R version 3.5.2"Loaded lars 1.2
    
    


442



<ol class=list-inline>
	<li>442</li>
	<li>10</li>
</ol>




<ol class=list-inline>
	<li>442</li>
	<li>64</li>
</ol>




<ol class=list-inline>
	<li>'age'</li>
	<li>'sex'</li>
	<li>'bmi'</li>
	<li>'map'</li>
	<li>'tc'</li>
	<li>'ldl'</li>
	<li>'hdl'</li>
	<li>'tch'</li>
	<li>'ltg'</li>
	<li>'glu'</li>
</ol>




<ol class=list-inline>
	<li>'age'</li>
	<li>'sex'</li>
	<li>'bmi'</li>
	<li>'map'</li>
	<li>'tc'</li>
	<li>'ldl'</li>
	<li>'hdl'</li>
	<li>'tch'</li>
	<li>'ltg'</li>
	<li>'glu'</li>
	<li>'age^2'</li>
	<li>'bmi^2'</li>
	<li>'map^2'</li>
	<li>'tc^2'</li>
	<li>'ldl^2'</li>
	<li>'hdl^2'</li>
	<li>'tch^2'</li>
	<li>'ltg^2'</li>
	<li>'glu^2'</li>
	<li>'age:sex'</li>
	<li>'age:bmi'</li>
	<li>'age:map'</li>
	<li>'age:tc'</li>
	<li>'age:ldl'</li>
	<li>'age:hdl'</li>
	<li>'age:tch'</li>
	<li>'age:ltg'</li>
	<li>'age:glu'</li>
	<li>'sex:bmi'</li>
	<li>'sex:map'</li>
	<li>'sex:tc'</li>
	<li>'sex:ldl'</li>
	<li>'sex:hdl'</li>
	<li>'sex:tch'</li>
	<li>'sex:ltg'</li>
	<li>'sex:glu'</li>
	<li>'bmi:map'</li>
	<li>'bmi:tc'</li>
	<li>'bmi:ldl'</li>
	<li>'bmi:hdl'</li>
	<li>'bmi:tch'</li>
	<li>'bmi:ltg'</li>
	<li>'bmi:glu'</li>
	<li>'map:tc'</li>
	<li>'map:ldl'</li>
	<li>'map:hdl'</li>
	<li>'map:tch'</li>
	<li>'map:ltg'</li>
	<li>'map:glu'</li>
	<li>'tc:ldl'</li>
	<li>'tc:hdl'</li>
	<li>'tc:tch'</li>
	<li>'tc:ltg'</li>
	<li>'tc:glu'</li>
	<li>'ldl:hdl'</li>
	<li>'ldl:tch'</li>
	<li>'ldl:ltg'</li>
	<li>'ldl:glu'</li>
	<li>'hdl:tch'</li>
	<li>'hdl:ltg'</li>
	<li>'hdl:glu'</li>
	<li>'tch:ltg'</li>
	<li>'tch:glu'</li>
	<li>'ltg:glu'</li>
</ol>



lars::lars()
>type - 表示所使用的回归方法，包括（lasso, lar, forward.stagewise, stepwise），选择不同的回归方法将得到不同的解路径；    
normalize - 表示是否对变量进行归一化，当为TRUE时，程序将对x和y进行L2正则化；  
intercept - 表示是否对变量进行中心化，当为TRUE时，程序将对x和y分别减去其均值。


```R
attach(diabetes)

mlasso = lars(x,y,type="lasso")
mlars = lars(x,y,type="lar")
mforw = lars(x,y,type="forward.stagewise")
mstep = lars(x,y,type="stepwise")
```


```R
mlasso
```


    
    Call:
    lars(x = x, y = y, type = "lasso")
    R-squared: 0.518 
    Sequence of LASSO moves:
         bmi ltg map hdl sex glu tc tch ldl age hdl hdl
    Var    3   9   4   7   2  10  5   8   6   1  -7   7
    Step   1   2   3   4   5   6  7   8   9  10  11  12


可以查看$R^2$  
"Sequence of LASSO moves" 是在进行Lasso回归时自变量被选入的顺序


```R
opt(6,5)
opar=par(mfrow=c(2,2))
plot(mlasso)
plot(mlars)
plot(mforw)
plot(mstep)
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_25_0.png)


竖线对应Lasso中迭代的次数（对应上面查看mlasso时的step），对应的系数值不为0的变量即为被选入的。

## 选定模型


```R
summary(mlasso)
```


<table>
<thead><tr><th></th><th scope=col>Df</th><th scope=col>Rss</th><th scope=col>Cp</th></tr></thead>
<tbody>
	<tr><th scope=row>0</th><td> 1        </td><td>2621009   </td><td>453.726255</td></tr>
	<tr><th scope=row>1</th><td> 2        </td><td>2510465   </td><td>418.032217</td></tr>
	<tr><th scope=row>2</th><td> 3        </td><td>1700369   </td><td>143.801193</td></tr>
	<tr><th scope=row>3</th><td> 4        </td><td>1527165   </td><td> 86.741078</td></tr>
	<tr><th scope=row>4</th><td> 5        </td><td>1365734   </td><td> 33.695679</td></tr>
	<tr><th scope=row>5</th><td> 6        </td><td>1324118   </td><td> 21.505224</td></tr>
	<tr><th scope=row>6</th><td> 7        </td><td>1308932   </td><td> 18.327003</td></tr>
	<tr><th scope=row>7</th><td> 8        </td><td>1275355   </td><td>  8.877493</td></tr>
	<tr><th scope=row>8</th><td> 9        </td><td>1270233   </td><td>  9.131148</td></tr>
	<tr><th scope=row>9</th><td>10        </td><td>1269390   </td><td> 10.843547</td></tr>
	<tr><th scope=row>10</th><td>11        </td><td>1264977   </td><td> 11.338975</td></tr>
	<tr><th scope=row>11</th><td>10        </td><td>1264765   </td><td>  9.266761</td></tr>
	<tr><th scope=row>12</th><td>11        </td><td>1263983   </td><td> 11.000000</td></tr>
</tbody>
</table>



最前面一列对应迭代次数(step)，Df=3对应迭代次数=2  
上表显示了Lasso回归中所有的cp值（**MallowsCp统计量，是对共线性的判断**），选取Cp最小的  
此例中对应最小Cp值的step=7（or 8?）


```R
mlasso$Cp
```


<dl class=dl-horizontal>
	<dt>0</dt>
		<dd>453.726255006272</dd>
	<dt>1</dt>
		<dd>418.032217290298</dd>
	<dt>2</dt>
		<dd>143.801193388963</dd>
	<dt>3</dt>
		<dd>86.7410780632947</dd>
	<dt>4</dt>
		<dd>33.6956791165279</dd>
	<dt>5</dt>
		<dd>21.5052237966864</dd>
	<dt>6</dt>
		<dd>18.3270029796993</dd>
	<dt>7</dt>
		<dd>8.87749261791703</dd>
	<dt>8</dt>
		<dd>9.13114827526573</dd>
	<dt>9</dt>
		<dd>10.8435467724511</dd>
	<dt>10</dt>
		<dd>11.3389749748865</dd>
	<dt>11</dt>
		<dd>9.26676056353836</dd>
	<dt>12</dt>
		<dd>11</dd>
</dl>




```R
mlasso$Cp[which.min(mlasso$Cp)]
```


<strong>7:</strong> 8.87749261791703


## 获取系数


```R
mlasso$beta[which.min(mlasso$Cp),]
```


<dl class=dl-horizontal>
	<dt>age</dt>
		<dd>0</dd>
	<dt>sex</dt>
		<dd>-197.756501135154</dd>
	<dt>bmi</dt>
		<dd>522.26484701812</dd>
	<dt>map</dt>
		<dd>297.15973688909</dd>
	<dt>tc</dt>
		<dd>-103.946248766907</dd>
	<dt>ldl</dt>
		<dd>0</dd>
	<dt>hdl</dt>
		<dd>-223.926033335305</dd>
	<dt>tch</dt>
		<dd>0</dd>
	<dt>ltg</dt>
		<dd>514.749480847553</dd>
	<dt>glu</dt>
		<dd>54.7676806302465</dd>
</dl>




```R
# 获取截距
predict(mlasso,data.frame(age=0,sex=0,bmi=0,map=0,tc=0,ld1=0,hd1=0,tch=0,ltg=0,glu=0),s=8)
# 这里的s是之前的Df,迭代次数step+1？
# data.frame中自变量的数量和数据框中进行lasso拟合的自变量数目相同，都要写上。
# 输出结果中的fit即为所求
```


<dl>
	<dt>$s</dt>
		<dd>8</dd>
	<dt>$fraction</dt>
		<dd>0.583333333333333</dd>
	<dt>$mode</dt>
		<dd>'step'</dd>
	<dt>$fit</dt>
		<dd>152.133484162896</dd>
</dl>



## 交叉验证  
cross validation <-> cv <-> cv.lars()


```R
cv1=cv.lars(x,y,type="lasso",mode="step")
cv1
```


<dl>
	<dt>$index</dt>
		<dd><ol class=list-inline>
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
	<li>5</li>
	<li>6</li>
	<li>7</li>
	<li>8</li>
	<li>9</li>
	<li>10</li>
	<li>11</li>
	<li>12</li>
	<li>13</li>
</ol>
</dd>
	<dt>$cv</dt>
		<dd><ol class=list-inline>
	<li>5954.14703966155</li>
	<li>5719.75327158815</li>
	<li>3936.26036159499</li>
	<li>3542.21475453098</li>
	<li>3174.73439926476</li>
	<li>3093.62293542858</li>
	<li>3052.86108022496</li>
	<li>3000.99425581899</li>
	<li>2991.04092539401</li>
	<li>2986.87193371072</li>
	<li>2996.37212226779</li>
	<li>3002.67997104454</li>
	<li>2994.06531553897</li>
</ol>
</dd>
	<dt>$cv.error</dt>
		<dd><ol class=list-inline>
	<li>382.751667681927</li>
	<li>378.33153118742</li>
	<li>251.721868718799</li>
	<li>237.04148107866</li>
	<li>198.178667993397</li>
	<li>197.207727753711</li>
	<li>189.444291666093</li>
	<li>185.876507776068</li>
	<li>184.786117379773</li>
	<li>178.732543497282</li>
	<li>181.337920241993</li>
	<li>178.638475876547</li>
	<li>179.153194912029</li>
</ol>
</dd>
	<dt>$mode</dt>
		<dd>'step'</dd>
</dl>




![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_36_1.png)



```R
cv1$index[which.min(cv1$cv)]
min(cv1$cv)
```


10



2986.87193371072



```R
# step=10对应的回归系数
mlasso$beta[10,]
```


<dl class=dl-horizontal>
	<dt>age</dt>
		<dd>0</dd>
	<dt>sex</dt>
		<dd>-227.175798242917</dd>
	<dt>bmi</dt>
		<dd>526.390594350024</dd>
	<dt>map</dt>
		<dd>314.950467216784</dd>
	<dt>tc</dt>
		<dd>-237.34097311995</dd>
	<dt>ldl</dt>
		<dd>33.628274416008</dd>
	<dt>hdl</dt>
		<dd>-134.599352051782</dd>
	<dt>tch</dt>
		<dd>111.384128693504</dd>
	<dt>ltg</dt>
		<dd>545.48259721323</dd>
	<dt>glu</dt>
		<dd>64.606670131436</dd>
</dl>



# 聚类

## the general idea  
1. 监督学习和无监督学习？
监督学习通常用来进行判别分析。  
无监督学习，是事先不知道样本属于哪一类。  
但事先知道样本的分类也可以进行聚类，可以用来判断分类是否正确。  
聚类是一种<b>无监督学习方法</b>。  
<br />  
  
2. 什么是聚类？  
聚类，就是将数据按一定的规则、方法分成几类的方法。  
聚类的最终目标，就是使<b>同一类之间的方差尽量小，使不同类之间的方差尽量大</b>。  
聚类通常的步骤是：
>1、Patten representation/准备工作：事先想一想准备分成几类，选用哪些变量，想对多少样本进行聚类，数据如何进行初步处理（如标准化，正态化，同向化等）。很多这些前期工作可能无法实现，有很多可能也没有必要。  
2、Patten proximity measure/距离度量方法的确定：点和点之间、类和类之间的距离应如何度量？用欧式距离？马氏距离？兰氏距离？还是自己创造一种更符合实际情况的距离？  
除了距离度量方法的确定，还有类和类之间的连接方法的确定，是应该用最短距离法，还是用最长距离法？不同的方法可能带来不同的分类结果。  
3、Grouping/聚类：确定了上述三步之后，就可以进行聚类了。但聚类过程也并非容易，当一个类与另外两个类的距离相等，该如何继续呢？  
4、Data abstraction/抽象出各类的含义：这一步并非必要。可以有也可以没有。给每个类定义一个含义，这样可以更直观，更方便。  
5、Cluster assessment/类评估：评价一下分类是否有意义，是否准确。  


 无论是什么聚类方法，基本的步骤都如上所示。区别主要在于，不同的聚类方法在计算类间距离和连接方式时候有所区别。
<br />  

3. 目前广泛的聚类方法主要有两种：<b>一种是系统聚类，一种是K均值聚类</b>。  
<br />  
<b>层次聚类/系统聚类</b>　  
 * 系统聚类无需事先确定需要分几类。  
 * 系统聚类一般用树状图(dendrogram)表示。  

>层次聚类方法具体又可分为凝聚的，分裂的两种方案。 　　  
1. 由n个类聚成1类——**凝聚的层次聚类**是一种<b>自底向上</b>的策略（agglomerative approach），首先将每个对象作为一个簇，然后合并这些原子簇为越来越大的簇，直到所有的对象都在一个簇中，或者某个终结条件被满足，绝大多数层次聚类方法属于这一类，它们只是在簇间相似度的定义上有所不同。   
2. 由1个类分成n类——**分裂的层次聚类**与凝聚的层次聚类相反，采用<b>自顶向下</b>的策略（divisive approach），它首先将所有对象置于同一个簇中，然后逐渐细分为越来越小的簇，直到每个对象自成一簇，或者达到了某个终止条件。  
我们并不重点介绍分离方法，因为它从本质上和系统聚类方法是一样的。   
但是它也有**优点**，当数据实际情况只是分成很少的几类时，用分离法能很快分出来，用系统聚类就需要分很久。   

<br />  
**K-means聚类**  
* 需要事先确定类数  
* 更适用于大样本集  

## Distances
>Minkowski Distance 闵氏距离通式  
$d_{ij}(q)=(\sum_{k=1}^{p}|x_{ik}-x_{jk}|^q)^{1/q}$  

<br />
![距离图](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/clustering_pic01.png)  
到中心点距离相同的点用同一种颜色表示  
<br />  

1. Manhattan distance / Block distance   
$d_{ij}(1)=\sum_{k=1}^{p}|x_{ik}-x_{jk}|$  
$d_{AB}=|x_A-x_B|+|y_A-y_B|$
2. Euclidean distance  
$d_{ij}(2)=(\sum_{k=1}^{p}|x_{ik}-x_{jk}|^2)^{1/2}$  
$d_{AB}=\sqrt{(x_A-x_B)^2+(y_A-y_B)^2}$  
 * <font color='#F8D86A'>the most frequently used  
 * 优点：有非常明确的空间距离概念
 * 缺点  
    没有区分量纲的不同，将所有变量视为影响相同的 —— 适用于已做标准化处理的数据（preprocessed data）  
    受维度的影响？</font>  

3. Chebyshev distance  
$d_{ij}(\inf)=max|x_{ik}-x_{jk}|$  

4. Mahalanobis distance 马氏距离  
$d_{ij}(M)=((X_i-X_j)' A^{-1} (X_i-X_j))^{\frac{1}{2}}$  
$𝐴$ represents the variance matrix, $𝑋_𝑖$ represents sample 𝑖 with 𝑝 dimensions  
 * <font color='#F8D86A'>马氏距离又称广义欧氏距离  
 * Taken variables’ correlation into consideration, if variables are independent, Mahalanobis distance turn into weighted Euclidean distance(如果各变量之间相互独立，即观测变量的协方差矩阵是对角矩阵，则马氏距离就退化为用各个观测指标的**标准差的倒数作为权数的加权欧氏距离**).   
 * 不受量纲影响。Taken variables’ variation into consideration,  and dimension problems can be solved.</font>  

<br />  
**距离的选择：**   
实际中，聚类分析前不妨试探性地多选择几个距离公式分别进行聚类，然后对聚类分析的结果进行对比分析，以确定最合适的距离测度方法。
    


## Linkage  
ri,sj - the ith / jth observation from group r/s  
1. Single Linkage 最短距离法  
$d_c(r,s)=\min{d(x_{ri},x_{sj})}, i=1,2,...,n_r, j=1,2,...,n_s$  
 * most oftenly used  
 * disadvantages: **chaining链式结果**. This comes about when clusters are not well separated当两个类之间不能很好地分离, and snake-like chains can form. Observations at opposite ends of the chain can be very dissimilar, but yet they end up in the same cluster. 
 * Another issue with single linkage is that **it does not take the cluster structure into account/没有考虑到距离结构**.  
2. Complete Linkage 最长距离法  
$d_c(r,s)=\max{d(x_{ri},x_{sj})}, i=1,2,...,n_r, j=1,2,...,n_s$  
 * Complete linkage is not susceptible to chaining不易表现出链式结果.
 * The resulting clusters tend to be spherical, and it **has difficulty recovering nonspherical groups**.但结果呈球形。  
 * Like single linkage, complete linkage **does not account for cluster structure**.没有考虑类的距离结构。  
3. Average Linkage 平均距离法   
$d_c(r,s)=\frac{1}{n_r n_s}\sum_{i=1}^{n_r} \sum_{j=1}^{n_s}d(x_{ri},x_{sj}),  i=1,2,...,n_r,  j=1,2,...,n_s$  
 * This method tends to combine clusters that have small variances, and it also tends to **produce clusters with approximately equal variance**.
 * It is relatively robust and **does take the cluster structure into account**.  
4. Centroild Linkage重心法   
$d_c(r,s)=d(\bar{x_r},\bar{x_s})$, where $\bar{x_r}$ is the avg of obs in the r-th cluster and $\bar{x_s}$ the avg of obs in the s-th cluster.考虑了全部点的信息，但存在信息损失。  

## K-means

K-means 是事先确定类数（K）的，即R中kmeans()函数的centers参数  
比层次聚类法更适用于大数据集（大数据集要从n类聚到1类太难了）  
<br />  

K-means聚类的目标：  
To partition the data into k groups such that the within-group sum-of-squares is minimized.  
Distortion Function $J(c,\mu)=\sum_{i=1}^n ||x^{(i)}-\mu_{c^{(i)}}||^2$ measures the sum of squared distances between each training example $x^{(i)}$ and the cluster centroid $\mu_{c^{(i)}}$ to which it has been assigned.  
<br />  

**K-means聚类的步骤**  
>1.Specify the number of clusters k.  
2. Determine initial cluster centroids (randomly).   
3. Calculate the distance between each observation and each cluster centroid.  
4. Assign every observation to the closest cluster.  
5. Calculate the centroid of every cluster using the observations that were just grouped there.  
6. Repeat steps 3 through 5 until no more changes in result.  

<br />  
K-means 算法一定会收敛至局部最优，但不一定收敛至全局最优。  
>J must monotonically decrease, and the value of J must converge. However, the distortion function J is a non-convex function, and so coordinate descent on J is not guaranteed to converge to the global minimum. In other words, k-means can be susceptible to local optima.  
To avoid this, **use different random initial values for the cluster centroids $\mu^j$**. Then, out of all the different clusterings found, **pick the one that gives the lowest distortion $J(c,\mu)$**.


use **heatmap** to get a basic idea of what kmeans does


```R
data(iris)
dmat=as.matrix(iris[sample(1:100),-5])
km=kmeans(dmat, centers=3)
opt(4,4)
image(t(dmat),yaxt="n")
image(t(dmat[order(km$cluster),]),yaxt="n")
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_46_0.png)



![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_46_1.png)



```R
library(ggplot2)
library(ggthemes)
library(cluster)
data=read.csv("DRfull.csv",header=TRUE)
head(data)
```


<table>
<thead><tr><th scope=col>Country.or.Area</th><th scope=col>D1</th><th scope=col>D12</th><th scope=col>D13</th><th scope=col>D15</th><th scope=col>D16</th><th scope=col>D17</th><th scope=col>D18</th><th scope=col>D2</th><th scope=col>D5</th><th scope=col>...</th><th scope=col>R14F</th><th scope=col>R14M</th><th scope=col>R14B</th><th scope=col>R6F</th><th scope=col>R6M</th><th scope=col>R6B</th><th scope=col>R8R</th><th scope=col>R8U</th><th scope=col>R9R</th><th scope=col>R9U</th></tr></thead>
<tbody>
	<tr><td>Afghanistan        </td><td>-2.4               </td><td>29825              </td><td>24                 </td><td>16.20              </td><td> 3.82              </td><td>47.42              </td><td>5.14               </td><td> 60                </td><td>35.3               </td><td>...                </td><td> 9.5               </td><td> 8.9               </td><td> 9.2               </td><td> 3.200             </td><td>13.100             </td><td> 9.800             </td><td>56                 </td><td>90                 </td><td>23                 </td><td>47                 </td></tr>
	<tr><td>Albania            </td><td>-0.3               </td><td> 3162              </td><td>55                 </td><td>32.56              </td><td>14.93              </td><td>21.33              </td><td>1.76               </td><td>111                </td><td>12.8               </td><td>...                </td><td> 9.0               </td><td>10.3               </td><td> 9.6               </td><td> 6.700             </td><td>17.600             </td><td>11.800             </td><td>94                 </td><td>97                 </td><td>86                 </td><td>95                 </td></tr>
	<tr><td>Algeria            </td><td>-1.9               </td><td>38482              </td><td>74                 </td><td>26.62              </td><td> 7.17              </td><td>27.42              </td><td>2.82               </td><td> 98                </td><td>24.6               </td><td>...                </td><td> 9.3               </td><td> 9.0               </td><td> 9.2               </td><td> 5.700             </td><td>25.500             </td><td>13.800             </td><td>79                 </td><td>85                 </td><td>88                 </td><td>98                 </td></tr>
	<tr><td>Angola             </td><td>-3.1               </td><td>20821              </td><td>60                 </td><td>16.18              </td><td> 3.84              </td><td>47.58              </td><td>5.98               </td><td> 47                </td><td>44.8               </td><td>...                </td><td> 8.7               </td><td> 8.2               </td><td> 8.5               </td><td>26.003             </td><td>31.837             </td><td>29.098             </td><td>34                 </td><td>68                 </td><td>20                 </td><td>87                 </td></tr>
	<tr><td>Antigua and Barbuda</td><td>-1.1               </td><td>   89              </td><td>30                 </td><td>30.26              </td><td>12.35              </td><td>25.96              </td><td>2.10               </td><td>143                </td><td>16.6               </td><td>...                </td><td>12.0               </td><td>11.3               </td><td>11.7               </td><td>15.900             </td><td>24.300             </td><td>20.100             </td><td>98                 </td><td>98                 </td><td>91                 </td><td>91                 </td></tr>
	<tr><td>Argentina          </td><td>-0.9               </td><td>41087              </td><td>93                 </td><td>30.83              </td><td>14.97              </td><td>24.42              </td><td>2.19               </td><td>152                </td><td>16.9               </td><td>...                </td><td>10.3               </td><td>11.0               </td><td>10.6               </td><td>29.700             </td><td>26.100             </td><td>28.000             </td><td>95                 </td><td>99                 </td><td>99                 </td><td>97                 </td></tr>
</tbody>
</table>




```R
df=data.frame(k=1:8,sw=NA)
pamlist=paste0("pam",df$k)
for (k in 1:8){
  pamk=pam(data[,-1],k)
  df[k,]$sw=pamk$silinfo$avg.width
}
```


```R
opt(5,2.5)
# plot(df$k,df$sw)
ggplot(df)+
  theme_bw()+
  geom_point(aes(k,sw),pch=19,size=2,col="#525288")+
  geom_line(aes(k,sw),col="#525288")+
  scale_x_continuous(breaks=1:8)
```




![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_49_1.png)



```R
opt(10,10)
plot(pamk,col=pamk$cluster)
```

    Warning message in if (color) {:
    "the condition has length > 1 and only the first element will be used"Warning message in if (color) c(2, 4, 6, 3) else 5:
    "the condition has length > 1 and only the first element will be used"


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_50_1.png)



![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_50_2.png)


B: number of monte carlo bootstrap samples


```R
gskmn=clusGap(data[,-1],FUN=kmeans,K.max=8,B=500)
gskmn
```


    Clustering Gap statistic ["clusGap"] from call:
    clusGap(x = data[, -1], FUNcluster = kmeans, K.max = 8, B = 500)
    B=500 simulated reference sets, k = 1..8; spaceH0="scaledPCA"
     --> Number of clusters (method 'firstSEmax', SE.factor=1): 1
             logW   E.logW      gap     SE.sim
    [1,] 14.94491 16.79945 1.854544 0.03294085
    [2,] 14.44529 16.11440 1.669101 0.03483898
    [3,] 14.09952 15.71344 1.613925 0.03457841
    [4,] 13.91641 15.43862 1.522205 0.03382952
    [5,] 13.65569 15.23681 1.581113 0.03518960
    [6,] 13.55290 15.08178 1.528885 0.04347128
    [7,] 13.37492 14.95503 1.580110 0.05228916
    [8,] 13.32660 14.84724 1.520637 0.05024694


## EM（Expectation Maximization）  
参考https://blog.csdn.net/zhihua_oba/article/details/73776553  
EM算法有很多的应用，最广泛的就是GMM混合高斯模型、聚类、HMM等等。我们讨论的是EM在聚类中的应用。  
<br />
We know that clustering methods can be devided into two types:  
1. hard clustering: clusters do not overlap  
2. soft clustering: clusters may overlap  

**Mixture models**:
probabilistically-grounded way of doing soft clustering  
think of **each cluster** as a generative model that corresponds to a **probability distribution/PD** (Gaussian or multinomial) $\rightarrow$ we want to discover the parameters of the PD, i.e. the mean and covariance of the pdf   
EM allows us to figure out those parameters  
<br />
>so basically, using EM, we will find out the pdf of all k clusters, therefore can calc the prob of all sample points belonging to each clusters, and assign the sample points to the most likely cluster  

![em](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/em_pic01.png)

<font color='#F8D86A'>  
**EM的步骤**  
1. 初始化分布参数$\theta$（向量）  
2. E步骤【聚类过程】：根据当前参数$\theta$的值计算出样本$x^{(i)}\in j$的后验概率估计值（期望Expectation, hence E）  
$$w_j^{(i)}=p(c^{(i)}=j|x^{(i)};\theta)$$  
其中$c^{(i)}$是类别,$x^{(i)}$是样本  
3. M步骤【更新centroid】：将似然函数(Maximization, hence M)最大化以获得新的参数值:  
$$\theta=argmax_{\theta}\sum_i \sum_j w_j^{(i)}\log{\frac{p(x^{(i)},c^{(i)};\theta)}{w_j^{(i)}}}$$
重复E、M步骤直至收敛。
</font>

EM与K-means的异同：  
>异：EM is very much like k-means， except that  
 * K-means is a hard clustering method -- in k-means, a point either contribute to this centroid or that, we get an assignment $c(i)$
 * EM is a soft clustering method -- in EM, a point gives a little bit of its mass to every centroid, we get the probability for i to belong to all cluster $w_j^{(i)}$  

>同：Similar to K-means, EM is also susceptible to local optima，对初始值敏感，容易得到局部最优解而非全局最优解。 so reinitializing at several different initial parameters may be a good idea.

## 系统聚类

<font size=3>**to get a basic idea**</font>   
the heatmap function is very quick to visualize the organization of these high dimensional data


```R
data(iris)
set.seed(143)
dmat=as.matrix(iris[sample(1:50),-5])
opt(5,5)
heatmap(dmat)
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_58_0.png)



```R
hc=hclust(dist(dmat))
clus=cutree(hc,4)

opt(4,4)
image(t(dmat),yaxt="n")
opt(4,4)
image(t(dmat[order(clus),]),yaxt="n")
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_59_0.png)



![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_59_1.png)



```R
data(iris)
n=nrow(iris)
loc=sample(n, .7*n)
train=iris[loc,]
test=iris[-loc,]
```

<font size=3><b>?dist</b></font>  
><font color="red"> 默认欧氏距离</font>  

注意之前提到过，欧式距离受量纲影响，因此适用于经过了标准化的数据  

<font size=3><b>?rect.hclust</b></font>  
> Draws rectangles around the branches of a dendrogram highlighting the corresponding clusters.


```R
iris.hcl=hclust(dist(iris[,1:4]))     ###hclust for Hierarchical Clustering 层次聚类
opt(6,4)
plot(iris.hcl,labels=FALSE,hang=-1)
re=rect.hclust(iris.hcl,k=3,border=c("#C04851","#7A7374","#2E317C"))
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_63_0.png)



```R
iris.id=cutree(iris.hcl,3)
#按照分成3类，得到样本的分类结果
iris.id
```


<ol class=list-inline>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>1</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>3</li>
	<li>2</li>
	<li>3</li>
	<li>3</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>3</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
	<li>2</li>
</ol>



<font size=4>choose(n,k)</font>  
>返回$C_n^k$的值，即$\frac{n!}{(n-k)!k!}$

## 聚类评价

【需要掌握】评价聚类效果的关键指标：轮廓系数和gap

### Silhouette指数/轮廓系数

1990年,Kaufman和Rousseeuw提出了silhouette统计量对最优聚类以及最优聚类数进行估计。  
  
<br />  
<font color='#F8D86A'>比较一个点到所在类各点的平均距离和**最近的**其他类各点的平均距离</font>  
对观测值$i$，定义$a_i$为$i$与所在类的其它点之间的平均距离  
对于任何一个其他的类$c$，定义$d(i,c)$为$i$与$c$类（除自身）中的所有点的平均距离，让$b_i$代表$d(i,c)$的最小值  
  
<br />  
  
则对于观测值$i$，其Silhouette width为：  
$sw_i = \frac{b_i-a_i}{max(a_i,b_i)}$  ,$sw_i \in (-1,1)$  
* $sw_i$ 靠近1：$a_i$靠近0，$i$距离当前所在类较近  
* $sw_i$ 靠近0：$a_i$和$b_i$差不多大  
* $sw_i$ 靠近-1：$b_i$相对于$a_i$很小，说明$i$没有被较好地归类  
  
对所有观测值取平均得到avg sw为：  
$\bar{sw} = \frac{1}{n}\sum_{i=1}^{n}sw_i$  
用$\bar{sw}$评价聚类效果：  
* <font color='#F8D86A'>$\bar{sw}$值越大，聚类效果越好，从而确定类数$k$</font>  
According to Kaufman and Rousseeuw, 
 * an average silhouette width greater than 0.5 indicates a reasonable partition of the data
 * a value of less than 0.2 would indicate that the data do not exhibit cluster structure.

>pam: a more robust version of k-means


```R
library(cluster)

data=iris[,1:4]

pam3=pam(data,3)
pam3$silinfo$avg.width
  # 相当于mean(pam3$silinfo$widths[,3])
min(pam3$silinfo$widths[,3])
max(pam3$silinfo$widths[,3])
# silhouette(pam3) # 相当于pam3$silinfo$widths
```


0.55281901235641



0.0263588124292912



0.853905051398459



```R
opt(4,4)
plot(pam3,col=pam3$cluster)#Silhouette　Plot
```

    Warning message in if (color) {:
    "the condition has length > 1 and only the first element will be used"Warning message in if (color) c(2, 4, 6, 3) else 5:
    "the condition has length > 1 and only the first element will be used"


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_72_1.png)



![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_72_2.png)



```R
pam4=pam(data,4)
pam4$silinfo$avg.width #pam3的sw_avg比pam4的大，说明聚成3类效果更好
opt(4,4)
plot(pam4,col=pam4$cluster)　
```


0.48969717913026


    Warning message in if (color) {:
    "the condition has length > 1 and only the first element will be used"Warning message in if (color) c(2, 4, 6, 3) else 5:
    "the condition has length > 1 and only the first element will be used"


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_73_2.png)



![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_73_3.png)


<font color='#F8D86A'>分为3类时的sw_avg(0.55)比4类(0.49)大，说明聚成3类效果更好</font>

### WSS(Within-cluster Sum of Squares)  
$D_r=\sum_{i \in C_r}\sum_{j \in C_r}||x_i-x_j||^2=2n_r \sum_{i \in C_r}||x_i-\bar{x}||^2$  
$W_k=\sum_{r=1}^{k}\frac{1}{2n_r}D_r$  
![WSS](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/clustering_pic02.png)  
>problem of using $W_k$ to determine $k$  
 - no reference clustering to compare?  
 - the differences $W_k-W_{k-1}$ are not normalized for comparison  

### Gap指数

2001年,Tibshirani et al.提出了Gap Statistic method对最优聚类以及最优聚类数进行估计。  
建立在上述$W_k$之上：假设存在一个参考分布，比较$W_k$和$W_k$在参考分布下的期望值的差异  
<br />  
**参考分布的选取**  
* 一维情况：$[0,1]$上的均匀分布  
* 多维情况： 
  * 简单均匀分布  
  * 利用奇异值分解(SVD)方法得到关于原数据的主成分部分。从一组服从均匀分布的主成分数据中产生参考特征。  

<br />  
**参考分布下期望值的求取**  
产生$B$个服从参考分布的参考数据集，用在这些参考数据集下的$\log{(W_k^*)}$来估计$E_n^*[\log{(W_k^*)}]$  
<br />  
   
$Gap_n(k)=E_n^*(\log{W_k})-\log{W_k}$   
<font color='#F8D86A'>the larger Gap the better (find the k that maximizes Gap(k)</font>  
![gap](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/clustering_pic03.png)  
but according to '1-standard-error' theory, we don't expect to use the k with the largest gap statistics  
but instead, we use the minimum k that satisfies:  
$Gap(k)>Gap(k+1)-s_{k+1}$  


```R
library(cluster)
data(iris)
data=iris[,1:4]

gskmn=clusGap(data,FUN=kmeans,K.max=8,B=500)# clusGap: Gap Statistic 方法
gskmn#显示最优的聚类数ｋ
opt(5,5)
plot(gskmn)#画图Gap值
```


    Clustering Gap statistic ["clusGap"] from call:
    clusGap(x = data, FUNcluster = kmeans, K.max = 8, B = 500)
    B=500 simulated reference sets, k = 1..8; spaceH0="scaledPCA"
     --> Number of clusters (method 'firstSEmax', SE.factor=1): 4
             logW   E.logW        gap     SE.sim
    [1,] 4.551642 4.640852 0.08921047 0.02854059
    [2,] 3.808397 4.191117 0.38271951 0.02429483
    [3,] 3.519407 4.012884 0.49347687 0.02641911
    [4,] 3.368124 3.920477 0.55235364 0.02470481
    [5,] 3.301857 3.838111 0.53625411 0.02421139
    [6,] 3.224683 3.763125 0.53844187 0.02420139
    [7,] 3.112055 3.700077 0.58802177 0.02524475
    [8,] 3.127535 3.647548 0.52001255 0.02804035



![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_78_1.png)



```R
logW=gskmn$Tab[,1]
E.logW=gskmn$Tab[,2]
gap=gskmn$Tab[,3]
sum(gap!=E.logW-logW)

dft=data.frame(n=rep(1:length(logW),2),val=c(logW,E.logW),lab=rep(c("logW","E.logW"),each=length(logW)))
opt(7,4)
ggplot(dft)+
  theme_bw()+
  geom_line(aes(n,val,color=lab))+
  geom_point(aes(n,val,color=lab),pch=19)+
  scale_color_manual(name="",values=c("#f2572d","#0067a6"))
```


0





![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_79_2.png)


### 兰德指数(Rand Index, RI)

Rand指数的实质：观察一对样本点$x_i$和$x_j$在两种聚类方法中被组合的方式是否相似  
* 相似：
 * 在$G1$和$G2$中，$x_i$和$x_j$均同类  
 * 在$G1$和$G2$中，$x_i$和$x_j$均不同类  
* 不同：
 * 在$G1$中，$x_i$和$x_j$同类，在$G2$中不同类  
 * 在$G1$中不同类，在$G2$中同类  

例如样本为${a,b,c,d,e,f}$，G1:${(a,b,c),(d,e,f)}$，G2:${(a,b),(c,d,e),f}$  
则由下表知，RI=9/15=0.6  
![rand index](./clustering_pic04.jpg)

匹配矩阵$N$  
$RI$可以表示为一个很长的公式  


```R
choose(150,2)
prod(1:150)/prod(1:148)/prod(1:2)
```


11175



11175



```R
data(iris)
hc=hclust(dist(iris[,-5]))
iris.id=cutree(hc,3)
# 计算Rand Index
n=table(iris.id, iris$Species); n
ntot2=sum(n^2)
nitot=rowSums(n)   ##ni.
nitot2=sum(nitot^2)
njtot=colSums(n)   ##n.j
njtot2=sum(njtot^2)

RI=(choose(150,2)+ntot2-0.5*nitot2-0.5*njtot2)/choose(150,2)
RI
```


           
    iris.id setosa versicolor virginica
          1     50          0         0
          2      0         23        49
          3      0         27         1



0.836778523489933



```R
#调整的rand index: pdfCluster::adj.rand.index
library(pdfCluster)   
adj.rand.index(iris.id,iris[,5])   
```


0.64225125183629


### 共表型相关系数

https://www.cnblogs.com/f-young/p/9248247.html  
Essentially: to measure 聚类方法得到的结构和原始数据结构的相似性，相似则优（即未破坏原始数据的结构）  
<br />  

Cophenetic矩阵$H$的数值是根据原始数据点之间的距离矩阵得来的，其中$n_{ij}$是样本点$i$和$j$第一次被聚为一类时，该类内各点之间距离的最小值。  
<br />  

1. 得到原始数据点的距离矩阵  
 ![距离](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/clustering_pic06.png)  
2. 得到共表(Cophenetic)矩阵  
  > 共表矩阵的计算：使用在合并类的过程中使用的最小距离来填充距离矩阵的下三角。  
  例：开始有6个类：A，B，C，D，E，F$\rightarrow$将距离最小为0.5的D和F聚为一类(D,F)$\rightarrow$将距离最小为0.71的A和B聚为一类(A,B)$\rightarrow$将距离最小为1.00的(D,F)和E聚为一类(D,F,E)$\rightarrow$将距离最小为1.41的(D,F,E)和C聚为一类(D,F,E,C)$\rightarrow$将距离最小为2.50的(D,F,E,C)和(A,B)聚为一类(D,F,E,C,A,B)$\rightarrow$聚类结束  
  对应的共表矩阵是：  
  ![共表](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/clustering_pic05.png)
3. 两个矩阵（对应生成两个向量？）的相关系数即为共表型相关系数  
  
<font color='#F8D86A'>共表型相关系数：越大越好</font>


```R
d=dist(iris[,1:4])   #鸢尾属植物各个样本点的距离矩阵

# 比较最小距离法与最大距离法的优劣

hc1=hclust(d,"complete") #最大距离法
d1=cophenetic(hc1) #共表矩阵
cor(d,d1)  #共表矩阵与距离矩阵之间的相关系数

hc2=hclust(d,"single")  ##最小距离法
d2=cophenetic(hc2) ##共表矩阵
cor(d,d2)  ##相关系数
```


0.726985683628462



0.863878677307658


输出结果分别为0.7269857、0.8638787  
采用最小距离法要好于最大距离法。

# 关联规则  
Association Rules  
<font color='#F8D86A'>掌握support confidence lift等指标</font>

1. Support  
$supp(x)=prob(x)$  
2. Confidence  
$conf(x\rightarrow y)=\frac{prob(x\cap y)}{prob(x)}=prob(y|x)$  
 * support和confidence通常用50%作为阈值来判断是否有用  
3. Life / Improvement  
$lift(x\rightarrow y)=\frac{prob(x\cap y)}{prob(x)prob(y)}=\frac{prob(y|x)}{prob(y)}$
 * 若$lift(x \rightarrow y)>1$，则$x\rightarrow y$是有用的规则，即买了x的人更容易买y  
4. Conviction  
$conv(x\rightarrow y)=\frac{1-supp(x)}{1-conf(x\rightarrow y)}$

**Strengths of Association Rules**  
1. It produces easy to understand results
2. It supports undirected data mining
3. It works on variable length data
4. Rules are relatively easy to compute  

<br />  
**Weaknesses of Association Rules**  
1. It an exponentially growth algorithm
2. It is difficult to determine the optimal number of items
3. It discounts rare items
4. It limited on the support that it provides attributes


```R
# prep school

#rev:列表反向输出
#class:看变量属性
#拆分变量：separate(data,id,sep="_",c("code","date"))
#合并变量：unite(data,vs_am,c("vs","am"))
  #data(mtcars)
  #dtemp=mtcars[,c(8,9)]
  #dtemp=unite(dtemp,"vs_am",vs,am)
```


```R
library(Matrix)
library(grid)
library(arules)
# library(arulesViz)
# library(tidyr)
```

* 数据简介

某公司100名用户2016年在该商店的购买记录。这100名用户共有3365条有效的交易记录。
主要变量包括用户ID、商店、购买时间、品牌、产品类型等。  
本案例中选取
>用户ID（customer_sid）  
购买时间（date）  
品牌（vendor_code）以及  
产品类型（分为department_name、category_name、subcategory_name三个级别）  

进行数据分析。


```R
data=read.csv("data.csv") #3365 obs
str(data)
levels(data$subcategory_name)
```

    'data.frame':	3365 obs. of  3 variables:
     $ X               : int  1 2 3 4 5 6 7 8 9 10 ...
     $ id              : Factor w/ 1403 levels "-9.16805e+18_2016/1/24",..: 1402 1402 1402 1402 1402 1402 1402 1402 1403 1403 ...
     $ subcategory_name: Factor w/ 79 levels "Accessories",..: 10 44 63 51 37 8 31 59 77 56 ...
    


<ol class=list-inline>
	<li>'Accessories'</li>
	<li>'Bags'</li>
	<li>'Base &amp; Top Coat'</li>
	<li>'BB &amp; CC Cream'</li>
	<li>'Blemish Solutions'</li>
	<li>'Blush'</li>
	<li>'Body'</li>
	<li>'Body Moisturiser'</li>
	<li>'Body Sunscreen'</li>
	<li>'Bronzer'</li>
	<li>'Brow'</li>
	<li>'Brush Cleaner'</li>
	<li>'Brush sets'</li>
	<li>'Call to Action / Redemption'</li>
	<li>'Candles'</li>
	<li>'Cleanser'</li>
	<li>'Collection'</li>
	<li>'Concealer'</li>
	<li>'Conditioner'</li>
	<li>'Dry Shampoo'</li>
	<li>'Electronic Devices'</li>
	<li>'Exfoliate &amp; Scrub'</li>
	<li>'Eye &amp; Brow'</li>
	<li>'Eye Care'</li>
	<li>'Eye Primer &amp; Base'</li>
	<li>'Eyelash Conditioning'</li>
	<li>'Eyeliner'</li>
	<li>'Face Sunscreen'</li>
	<li>'False Lashes'</li>
	<li>'Foundation'</li>
	<li>'Fragrance'</li>
	<li>'Grooming'</li>
	<li>'Hair'</li>
	<li>'Hand Cream'</li>
	<li>'Illuminator'</li>
	<li>'Kits'</li>
	<li>'Kits &amp; Sets'</li>
	<li>'Lip'</li>
	<li>'Lip Care'</li>
	<li>'Lip Crayon '</li>
	<li>'Lip Gloss'</li>
	<li>'Lip Liner'</li>
	<li>'Lip Prep'</li>
	<li>'Lipstick'</li>
	<li>'Loose Powder'</li>
	<li>'Makeup'</li>
	<li>'Makeup Remover'</li>
	<li>'Mani &amp; Pedi'</li>
	<li>'Mascara'</li>
	<li>'Masks'</li>
	<li>'Moisturiser'</li>
	<li>'Multi'</li>
	<li>'Nail Polish'</li>
	<li>'Nail Treatment'</li>
	<li>'Neck Care'</li>
	<li>'Oils &amp; Serums'</li>
	<li>'Other'</li>
	<li>'Packs'</li>
	<li>'Palettes &amp; Sets'</li>
	<li>'Peels'</li>
	<li>'Powder '</li>
	<li>'Pressed Powder'</li>
	<li>'Primer'</li>
	<li>'Scrubs &amp; Exfoliators'</li>
	<li>'Sets'</li>
	<li>'Setting Sprays'</li>
	<li>'Shadow'</li>
	<li>'Shampoo'</li>
	<li>'Skincare'</li>
	<li>'Soap'</li>
	<li>'Sprays and Diffusers'</li>
	<li>'Styling'</li>
	<li>'Tanning'</li>
	<li>'Tinted Moisturiser'</li>
	<li>'Tinted Sunscreen'</li>
	<li>'Toner &amp; Mist'</li>
	<li>'Travel'</li>
	<li>'Treatment'</li>
	<li>'Wash &amp; Bathe'</li>
</ol>




```R
data3=read.transactions("data.csv",format="single",sep=",",cols=c(2,3)) 

#每个交易的项目数
basketSize=size(data3);
summary(basketSize) 
length(basketSize) #3366
sum(basketSize) #3366
```


       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       1.00    1.00    2.00    2.16    3.00   12.00 



1404



3033



```R
#支持度
itemFreq=itemFrequency(data3)
itemFreq[1:5]
sum(itemFreq)  #1："平均一个transaction购买的item个数"
```


<dl class=dl-horizontal>
	<dt>Accessories</dt>
		<dd>0.00498575498575499</dd>
	<dt>Bags</dt>
		<dd>0.00142450142450142</dd>
	<dt>Base &amp; Top Coat</dt>
		<dd>0.000712250712250712</dd>
	<dt>BB &amp; CC Cream</dt>
		<dd>0.0128205128205128</dd>
	<dt>Blemish Solutions</dt>
		<dd>0.0156695156695157</dd>
</dl>




2.16025641025641



```R
#查看basketSize的分布：密度曲线
itemCount=(itemFreq/sum(itemFreq))*sum(basketSize)
  #itemCount表示每个item出现的次数。Support(X) = Xs/N, N是总的交易数,Xs就是Item X的count。
summary(itemCount)
```


       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       1.00    6.00   21.50   37.91   51.25  274.00 



```R
#按每个项目出现次数排序
orderedItem=sort(itemCount,decreasing=T)
orderedItem[1:10]
```


<dl class=dl-horizontal>
	<dt>Call to Action / Redemption</dt>
		<dd>274</dd>
	<dt>Lipstick</dt>
		<dd>169</dd>
	<dt>Foundation</dt>
		<dd>168</dd>
	<dt>Cleanser</dt>
		<dd>129</dd>
	<dt>Mascara</dt>
		<dd>126</dd>
	<dt>Concealer</dt>
		<dd>121</dd>
	<dt>Moisturiser</dt>
		<dd>119</dd>
	<dt>Primer</dt>
		<dd>92</dd>
	<dt>Fragrance</dt>
		<dd>89</dd>
	<dt>Shadow</dt>
		<dd>89</dd>
</dl>




```R
#按支持度support排序
orderedItemFreq = sort(itemFrequency(data3),decreasing=T)
orderedItemFreq[1:10]
```


<dl class=dl-horizontal>
	<dt>Call to Action / Redemption</dt>
		<dd>0.195156695156695</dd>
	<dt>Lipstick</dt>
		<dd>0.12037037037037</dd>
	<dt>Foundation</dt>
		<dd>0.11965811965812</dd>
	<dt>Cleanser</dt>
		<dd>0.0918803418803419</dd>
	<dt>Mascara</dt>
		<dd>0.0897435897435897</dd>
	<dt>Concealer</dt>
		<dd>0.0861823361823362</dd>
	<dt>Moisturiser</dt>
		<dd>0.0847578347578348</dd>
	<dt>Primer</dt>
		<dd>0.0655270655270655</dd>
	<dt>Fragrance</dt>
		<dd>0.0633903133903134</dd>
	<dt>Shadow</dt>
		<dd>0.0633903133903134</dd>
</dl>




```R
#绘图：按某一最小支持度查看
itemFrequencyPlot(data3, support=0.05,col = "blue",
  xlab=paste("Proportion of Market Baskets Containing Item",  
  "\n(Item Relative Frequency or Support)"))
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_104_0.png)



```R
#绘图：按排序查看
itemFrequencyPlot(data3, topN=10,col = "blue",
  xlab = paste("Proportion of Market Baskets Containing Item",  
  "\n(Item Relative Frequency or Support)"))
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_105_0.png)


* 进行规则挖掘


```R
data=data3

#求频繁项集
fsets=eclat(data, parameter=list(support =0.05,maxlen=10))
inspect(fsets[1:10])
inspect(sort(fsets, by = "support")[1:10])
```

    Eclat
    
    parameter specification:
     tidLists support minlen maxlen            target   ext
        FALSE    0.05      1     10 frequent itemsets FALSE
    
    algorithmic control:
     sparse sort verbose
          7   -2    TRUE
    
    Absolute minimum support count: 70 
    
    create itemset ... 
    set transactions ...[80 item(s), 1404 transaction(s)] done [0.00s].
    sorting and recoding items ... [15 item(s)] done [0.00s].
    creating sparse bit matrix ... [15 row(s), 1404 column(s)] done [0.00s].
    writing  ... [15 set(s)] done [0.00s].
    Creating S4 object  ... done [0.00s].
         items                         support    count
    [1]  {Call to Action / Redemption} 0.19515670 274  
    [2]  {Foundation}                  0.11965812 168  
    [3]  {Lipstick}                    0.12037037 169  
    [4]  {Concealer}                   0.08618234 121  
    [5]  {Mascara}                     0.08974359 126  
    [6]  {Cleanser}                    0.09188034 129  
    [7]  {Moisturiser}                 0.08475783 119  
    [8]  {Primer}                      0.06552707  92  
    [9]  {Shadow}                      0.06339031  89  
    [10] {Eyeliner}                    0.05840456  82  
         items                         support    count
    [1]  {Call to Action / Redemption} 0.19515670 274  
    [2]  {Lipstick}                    0.12037037 169  
    [3]  {Foundation}                  0.11965812 168  
    [4]  {Cleanser}                    0.09188034 129  
    [5]  {Mascara}                     0.08974359 126  
    [6]  {Concealer}                   0.08618234 121  
    [7]  {Moisturiser}                 0.08475783 119  
    [8]  {Primer}                      0.06552707  92  
    [9]  {Shadow}                      0.06339031  89  
    [10] {Fragrance}                   0.06339031  89  
    


```R
#求规则
rules=apriori(data,parameter=list(support=0.01,confidence=0.01,minlen=2))
summary(rules)
```

    Apriori
    
    Parameter specification:
     confidence minval smax arem  aval originalSupport maxtime support minlen
           0.01    0.1    1 none FALSE            TRUE       5    0.01      2
     maxlen target   ext
         10  rules FALSE
    
    Algorithmic control:
     filter tree heap memopt load sort verbose
        0.1 TRUE TRUE  FALSE TRUE    2    TRUE
    
    Absolute minimum support count: 14 
    
    set item appearances ...[0 item(s)] done [0.00s].
    set transactions ...[80 item(s), 1404 transaction(s)] done [0.00s].
    sorting and recoding items ... [48 item(s)] done [0.00s].
    creating transaction tree ... done [0.00s].
    checking subsets of size 1 2 3 done [0.00s].
    writing ... [48 rule(s)] done [0.00s].
    creating S4 object  ... done [0.00s].
    


    set of 48 rules
    
    rule length distribution (lhs + rhs):sizes
     2 
    48 
    
       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
          2       2       2       2       2       2 
    
    summary of quality measures:
        support          confidence           lift            count      
     Min.   :0.01068   Min.   :0.05474   Min.   :0.7320   Min.   :15.00  
     1st Qu.:0.01140   1st Qu.:0.10008   1st Qu.:0.8931   1st Qu.:16.00  
     Median :0.01211   Median :0.14168   Median :1.3696   Median :17.00  
     Mean   :0.01386   Mean   :0.14946   Mean   :1.5292   Mean   :19.46  
     3rd Qu.:0.01531   3rd Qu.:0.18134   3rd Qu.:1.8198   3rd Qu.:21.50  
     Max.   :0.02279   Max.   :0.34043   Max.   :3.0781   Max.   :32.00  
    
    mining info:
     data ntransactions support confidence
     data          1404    0.01       0.01



```R
inspect(sort(rules, by = "support")[1:5])   
print("----------------")
inspect(sort(rules, by = "confidence")[1:5])
print("----------------")
inspect(sort(rules, by = "lift")[1:5])
```

        lhs                              rhs                           support   
    [1] {Moisturiser}                 => {Cleanser}                    0.02279202
    [2] {Cleanser}                    => {Moisturiser}                 0.02279202
    [3] {Cleanser}                    => {Call to Action / Redemption} 0.02065527
    [4] {Call to Action / Redemption} => {Cleanser}                    0.02065527
    [5] {Foundation}                  => {Call to Action / Redemption} 0.01923077
        confidence lift      count
    [1] 0.2689076  2.9267149 32   
    [2] 0.2480620  2.9267149 32   
    [3] 0.2248062  1.1519267 29   
    [4] 0.1058394  1.1519267 29   
    [5] 0.1607143  0.8235141 27   
    [1] "----------------"
        lhs              rhs                           support    confidence
    [1] {Makeup}      => {Foundation}                  0.01139601 0.3404255 
    [2] {Lip Liner}   => {Lipstick}                    0.01210826 0.3400000 
    [3] {Moisturiser} => {Cleanser}                    0.02279202 0.2689076 
    [4] {Cleanser}    => {Moisturiser}                 0.02279202 0.2480620 
    [5] {Lip Care}    => {Call to Action / Redemption} 0.01068376 0.2343750 
        lift     count
    [1] 2.844985 16   
    [2] 2.824615 17   
    [3] 2.926715 32   
    [4] 2.926715 32   
    [5] 1.200958 15   
    [1] "----------------"
        lhs              rhs           support    confidence lift     count
    [1] {Eyeliner}    => {Shadow}      0.01139601 0.1951220  3.078104 16   
    [2] {Shadow}      => {Eyeliner}    0.01139601 0.1797753  3.078104 16   
    [3] {Cleanser}    => {Moisturiser} 0.02279202 0.2480620  2.926715 32   
    [4] {Moisturiser} => {Cleanser}    0.02279202 0.2689076  2.926715 32   
    [5] {Makeup}      => {Foundation}  0.01139601 0.3404255  2.844985 16   
    


```R
#搜索规则
rules_Foundation = subset(rules,rhs %in% c("Foundation"))  
inspect(sort(rules_Foundation,by="support"))
inspect(sort(rules_Foundation,by="confidence"))

#保存结果
rules_df = as(rules, "data.frame") 
head(rules_df)
# write.csv(rules_df,"rules.csv")
```

        lhs                              rhs          support    confidence
    [1] {Call to Action / Redemption} => {Foundation} 0.01923077 0.09854015
    [2] {Concealer}                   => {Foundation} 0.01851852 0.21487603
    [3] {Primer}                      => {Foundation} 0.01282051 0.19565217
    [4] {Shadow}                      => {Foundation} 0.01210826 0.19101124
    [5] {Mascara}                     => {Foundation} 0.01210826 0.13492063
    [6] {Makeup}                      => {Foundation} 0.01139601 0.34042553
    [7] {Lipstick}                    => {Foundation} 0.01139601 0.09467456
        lift      count
    [1] 0.8235141 27   
    [2] 1.7957497 26   
    [3] 1.6350932 18   
    [4] 1.5963082 17   
    [5] 1.1275510 17   
    [6] 2.8449848 16   
    [7] 0.7912088 16   
        lhs                              rhs          support    confidence
    [1] {Makeup}                      => {Foundation} 0.01139601 0.34042553
    [2] {Concealer}                   => {Foundation} 0.01851852 0.21487603
    [3] {Primer}                      => {Foundation} 0.01282051 0.19565217
    [4] {Shadow}                      => {Foundation} 0.01210826 0.19101124
    [5] {Mascara}                     => {Foundation} 0.01210826 0.13492063
    [6] {Call to Action / Redemption} => {Foundation} 0.01923077 0.09854015
    [7] {Lipstick}                    => {Foundation} 0.01139601 0.09467456
        lift      count
    [1] 2.8449848 16   
    [2] 1.7957497 26   
    [3] 1.6350932 18   
    [4] 1.5963082 17   
    [5] 1.1275510 17   
    [6] 0.8235141 27   
    [7] 0.7912088 16   
    


<table>
<thead><tr><th scope=col>rules</th><th scope=col>support</th><th scope=col>confidence</th><th scope=col>lift</th><th scope=col>count</th></tr></thead>
<tbody>
	<tr><td><span style=white-space:pre-wrap>{Lip Liner} =&gt; {Lipstick}                  </span></td><td>0.01210826                                                                            </td><td>0.34000000                                                                            </td><td>2.824615                                                                              </td><td>17                                                                                    </td></tr>
	<tr><td><span style=white-space:pre-wrap>{Lipstick} =&gt; {Lip Liner}                  </span></td><td>0.01210826                                                                            </td><td>0.10059172                                                                            </td><td>2.824615                                                                              </td><td>17                                                                                    </td></tr>
	<tr><td><span style=white-space:pre-wrap>{Makeup} =&gt; {Foundation}                   </span></td><td>0.01139601                                                                            </td><td>0.34042553                                                                            </td><td>2.844985                                                                              </td><td>16                                                                                    </td></tr>
	<tr><td><span style=white-space:pre-wrap>{Foundation} =&gt; {Makeup}                   </span></td><td>0.01139601                                                                            </td><td>0.09523810                                                                            </td><td>2.844985                                                                              </td><td>16                                                                                    </td></tr>
	<tr><td>{Lip Care} =&gt; {Call to Action / Redemption}</td><td>0.01068376                                    </td><td>0.23437500                                    </td><td>1.200958                                      </td><td>15                                            </td></tr>
	<tr><td>{Call to Action / Redemption} =&gt; {Lip Care}</td><td>0.01068376                                    </td><td>0.05474453                                    </td><td>1.200958                                      </td><td>15                                            </td></tr>
</tbody>
</table>



* 图形展示


```R
#散点图
opt(4,4)
plot(rules,control=list(jitter=2,col=rev(brewer.pal(9,"Greens")[4:9])),shading="lift") 
# problem here: can't deal with package "arulesViz"
```

    Warning message:
    "package 'arulesViz' was built under R version 3.5.3"


    Error: package or namespace load failed for 'arulesViz' in loadNamespace(j <- i[[1L]], c(lib.loc, .libPaths()), versionCheck = vI[[j]]):
     namespace 'jsonlite' 1.5 is already loaded, but >= 1.6 is required
    Traceback:
    

    1. library(arulesViz)

    2. tryCatch({
     .     attr(package, "LibPath") <- which.lib.loc
     .     ns <- loadNamespace(package, lib.loc)
     .     env <- attachNamespace(ns, pos = pos, deps)
     . }, error = function(e) {
     .     P <- if (!is.null(cc <- conditionCall(e))) 
     .         paste(" in", deparse(cc)[1L])
     .     else ""
     .     msg <- gettextf("package or namespace load failed for %s%s:\n %s", 
     .         sQuote(package), P, conditionMessage(e))
     .     if (logical.return) 
     .         message(paste("Error:", msg), domain = NA)
     .     else stop(msg, call. = FALSE, domain = NA)
     . })

    3. tryCatchList(expr, classes, parentenv, handlers)

    4. tryCatchOne(expr, names, parentenv, handlers[[1L]])

    5. value[[3L]](cond)

    6. stop(msg, call. = FALSE, domain = NA)



```R
# Grouped Matrix
plot(rules, method="grouped",control=list(col = rev(brewer.pal(9, "Greens")[4:9]))) 
#关联规则的图形展示
plot(rules,measure="confidence",method="graph",control=list(type="items"),shading="lift")
```

# 树

## Intuition  
![tree](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/tree_pic01.png)

## 单棵树

### Information Gain  
>def: 拆分前和拆分后的熵的差值  
expected entropy drop after split  

$I=-(p_1 \log_2{p_1}+ ... +p_k \log_2{p_k})=-\sum_{i=1}^k p_i \log_2{p_i}$  
$Gain(X)=I(n,n_1)-E(X)$  
其中$I(n,n_1)=-(p \log_2{p}+(1-p) \log_2{(1-p)})$，$p=\frac{n_1}{n}$  
$E(X)=\frac{m_1}{n}\ast I(m_1,m_{11})+\frac{m_2}{n}\ast I(m_2,m_{21})+ ... + \frac{m_k}{n}\ast I(m_k,m_{k1})$  
  
<br />  
* An Example  
![infogain](./information_gain.jpg)  
**拆分前**的熵值（不按年龄区分时，16个样本，4有12无）--$I(16,4)$  
$I(16,4)=-(4/16 * log_2{4/16}+12/16 * log_2{12/16})=0.8113$  
**拆分后**的熵值（按年龄区分，6/16在第一组，10/16在第二组，第一组1有5无，第二组3有10无）  
$E(年龄)=(6/16)*I(6,1)+(10/16)*I(10,3)=0.7946$  
$Gain(年龄)=I(16,4)-E(年龄)=0.0167$  
<font color='#F8D86A'>信息增益越大越好 **等价于** 拆分后的熵值越小越好（拆分前的熵值对于所有下级节点而言都一样）</font>  


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
![info_gain](./information_gain.jpg)  

$Gini(split_j)=weighted\, sum\,  of\,  Gini(node)=\frac{n_1}{n} Gini(n_1,n_{11})+ ... +\frac{n_k}{n} Gini(n_k,n_{k1})$  
其中$Gini(n,a)=1-(\frac{a}{n})^2-(1-\frac{a}{n})^2$  
>worst case: $Gini(n,\frac{n}{2})=1-1/4-1/4=1/2$  
best case: $Gini(n,n)=Gini(n,0)=1-1-0=0$  
  
<font color='#F8D86A'>$Gini$系数越小越好</font>  


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
<thead><tr><th></th><th scope=col>Sepal.Length</th><th scope=col>Sepal.Width</th><th scope=col>Petal.Length</th><th scope=col>Petal.Width</th><th scope=col>Species</th></tr></thead>
<tbody>
	<tr><th scope=row>9</th><td>4.4       </td><td>2.9       </td><td>1.4       </td><td>0.2       </td><td>setosa    </td></tr>
	<tr><th scope=row>43</th><td>4.4       </td><td>3.2       </td><td>1.3       </td><td>0.2       </td><td>setosa    </td></tr>
	<tr><th scope=row>77</th><td>6.8       </td><td>2.8       </td><td>4.8       </td><td>1.4       </td><td>versicolor</td></tr>
	<tr><th scope=row>44</th><td>5.0       </td><td>3.5       </td><td>1.6       </td><td>0.6       </td><td>setosa    </td></tr>
	<tr><th scope=row>105</th><td>6.5       </td><td>3.0       </td><td>5.8       </td><td>2.2       </td><td>virginica </td></tr>
	<tr><th scope=row>87</th><td>6.7       </td><td>3.1       </td><td>4.7       </td><td>1.5       </td><td>versicolor</td></tr>
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
    


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_126_1.png)


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


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_134_0.png)


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

自从AdaBoost 方法提出之后，大量模拟和应用结果显示该方法可以大大降低基分类器(比如决策树)的预测误差，Breiman(1996)曾指出AdaBoost 方法是现有的分类方法中最好的一种。Breiman(1996)认为<font color='#F8D86A'>最主要的原因是AdaBoost方法的**自适应的重新抽样的方法**，而不是其具体定义的更新p(n)的公式。</font>  

<font color='#F8D86A'>二分类Adaboost算法是最小化指数损失$L(y,f(x))=\exp{(-yf(x))}$的分步向前可加模型。</font>


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
<thead><tr><th scope=col>i</th><th scope=col>error</th></tr></thead>
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






![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_148_2.png)



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


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_150_0.png)


## Random Forest

与bagging只在数据（行）的层面上随机不同，<font color='#F8D86A'>随机森林在变量（列）的使用和数据（行）的使用上都进行随机化，生成多颗分类树，最后汇总其结果。</font> 这样随机森林算法就可以**降低树与树之间的相关关系**，并且尽可能的控制平均方差。  

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
<thead><tr><th></th><th scope=col>setosa</th><th scope=col>versicolor</th><th scope=col>virginica</th><th scope=col>MeanDecreaseAccuracy</th><th scope=col>MeanDecreaseGini</th></tr></thead>
<tbody>
	<tr><th scope=row>Sepal.Length</th><td> 6.74</td><td> 8.64</td><td> 6.16</td><td>10.42</td><td> 6.41</td></tr>
	<tr><th scope=row>Sepal.Width</th><td> 3.98</td><td> 1.87</td><td> 0.93</td><td> 3.15</td><td> 1.51</td></tr>
	<tr><th scope=row>Petal.Length</th><td>22.24</td><td>31.32</td><td>26.03</td><td>33.15</td><td>31.05</td></tr>
	<tr><th scope=row>Petal.Width</th><td>22.14</td><td>32.65</td><td>30.06</td><td>34.41</td><td>30.34</td></tr>
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


# LDA & SVM

also to solve the classification problem  
  
read the following if interested  
>https://blog.csdn.net/v_july_v/article/details/7624837#t34  
https://www.zhihu.com/question/24627  
https://www.csie.ntu.edu.tw/~cjlin/libsvm/![image.png](attachment:image.png)

## LDA (Linear Discriminant)


```R
library(MASS) #要用到MASS::lda函数
data(iris)
iris.lda=lda(Species~.,data=iris)
iris.lda
```


    Call:
    lda(Species ~ ., data = iris)
    
    Prior probabilities of groups:
        setosa versicolor  virginica 
     0.3333333  0.3333333  0.3333333 
    
    Group means:
               Sepal.Length Sepal.Width Petal.Length Petal.Width
    setosa            5.006       3.428        1.462       0.246
    versicolor        5.936       2.770        4.260       1.326
    virginica         6.588       2.974        5.552       2.026
    
    Coefficients of linear discriminants:
                        LD1         LD2
    Sepal.Length -0.8293776  0.02410215
    Sepal.Width  -1.5344731  2.16452123
    Petal.Length  2.2012117 -0.93192121
    Petal.Width   2.8104603  2.83918785
    
    Proportion of trace:
       LD1    LD2 
    0.9912 0.0088 



```R
ipred=predict(iris.lda)$class
table(ipred, iris$Species, dnn=c("Pred","True"))
```


                True
    Pred         setosa versicolor virginica
      setosa         50          0         0
      versicolor      0         48         1
      virginica       0          2        49


## SVM (Support Vector Machine)

* <font color='#F8D86A'>掌握线性可分情况，其他情况了解，回归情况不要求  
* 核函数为什么会提出来?</font>  
<br /> 
支持向量机(SupportVectorMachine)是Cortes和Vapnik于1995年首先提出的一种分类算法。 
<br /> 
* 优点  
 * 它在解决小样本（大样本时，稀疏也可以，但需要小心）、非线性及高维模式识别中表现出许多特有的优势。  
 * 回归和分类均可以处理，相对容易训练；不像神经网络会导致局部最优，支持向量机寻求的是**总体最优性**；  
 * 它向高维数据变换过渡比较方便；  
 * 在平衡分类器的复杂性和误差时，能够很直观地控制；可以把诸如字符串那样的**非向量数据**作为输入。  
<br /> 
* 缺点  
 * **寻找合适的核函数，调整最优的参数($c$/$\gamma$等）**计算量很大。  
 * 当数据维度p大于样本量n时，效果不好？？？  
 * 不直接提供probability estmation，需要用K-fold cross validation来计算？  

### 线性分类情况

1. the SPLIT  
二维平面上有两种不同的数据（圈和叉）。由于这些数据是线性可分的，所以可以用一条直线将这两类数据分开，这条直线就相当于一个超平面，超平面一边的数据点所对应的y全是-1 ，另一边所对应的y全是1。  
这个超平面可以用分类函数表示$f(x)=w^T \ast x+b$表示  
![svm示意](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/svm_pic01.png)  
在进行分类的时候，遇到一个新的数据点x，将x代入f(x) 中，如果f(x)小于0则将x的类别赋为-1，如果f(x)大于0则将x的类别赋为1。  
<br />
2. the BEST SPLIT  
对一个数据点进行分类，当超平面离数据点的“间隔”(margin)越大，分类的确信度（confidence）也越大。所以，为了使得分类的确信度尽量高，需要让所选择的超平面能够最大化这个“间隔”值。这个间隔就是下图中的Gap的一半。  
![svm示意2](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/svm_pic02.png)

SVM的作用是分类器，本质是有约束的最优化问题(constrained optimization problem）  
1. 最优化 - 让路最宽  
$max \tilde{\gamma}$  
可以somehow转化成$max \frac{1}{||\omega||} $  
2. 约束条件 - “点都不在路上”  
$y_i(\omega^T x_i+b)>=1,  i=1,2,...,n$  
![svm示意3](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/svm_pic03.png)  
上图中，中间的实线便是寻找到的最优超平面（Optimal Hyper Plane），其到两条虚线边界的距离相等，这个距离便是几何间隔$\tilde{\gamma}$，两条虚线间隔边界之间的距离等于$2 \tilde{\gamma}$，而**虚线间隔边界上的点则是支持向量**。由于这些支持向量刚好在虚线间隔边界上，所以它们满足$y(w^T x+b)=1$（为方便推导可以令$\hat{\gamma}=1$），而对于所有不是支持向量的点，则显然有$y(w^T x+b)>1$。

### 非线性情况   
**核函数Kernel**  
* 对于非线性的情况，SVM 的处理方法是选择一个核函数$K(·,·)$ ，通过将数据映射到高维空间，来解决在原始空间中线性不可分的问题。  
* 核函数定义：计算两个向量在隐式映射过后的空间中的内积的函数??  
* interestingly, 在SVM中需要计算的地方数据向量总是以内积的形式出现的  

<br />
核函数的价值在于它虽然也是将特征进行从低维到高维的转换，但它只需在低维上进行计算，就可将实质上的分类效果表现在了高维上，从而避免直接在高维空间中进行复杂计算。  

<br />  
**核函数形式**  
* 线性核: $K(x,y)=x^T  y$  
* 多项式核: $K(x,y)=(x^T  y + 1)^{d}$  
* 高斯核 （Radial basis function,RBF）: $K(x,y)=exp(-\frac{||x-y||^2}{2\sigma^2})$    
* Sigmoid核:  $K(x,y)=tanh(\kappa x^T  y + \theta)$  
   双曲正切函数$tanh(x)=\frac{e^x-e^{-x}}{e^x+e^{-x}}$  

### outlier的处理 —— 松弛变量   
适当允许数据点偏离超平面  
原本的约束条件$y_i(\omega^T x_i +b)>=1$改为$y_i(\omega^T x_i +b)>=1-\delta_i$  
其中$\delta_i>0$是松弛变量(slack variable)  
将目标函数$\min{\frac{1}{2}||\omega||^2}$改为$\min{\frac{1}{2}||\omega||^2+\sum_{i}^{n}C\ast\delta_i}$
 1. $C$ parameter - 惩罚项  
 用于控制目标函数中两项（“寻找 margin 最大的超平面”和“保证数据点偏差量最小”）之间的权重。  
 C低：模型容错率高，简洁  
 C高：模型容错率低，但可能过拟合（即对训练集拟合效果好，对测试集未必好）  
 2. $\gamma$ parameter
 <br />  
参数的调整（parameter tuning） - K-fold cross validation

### SVM的R实现  
e1071::svm()  
参数说明：  
  
* Type：SVM的形式，选项有：C-classification，nu-classification，one-classification (for novelty detection)，eps-regression，nu-regression。  
后面两者为利用SVM做回归时用。  
默认为C分类器，使用nu分类器会使决策边界更光滑一些，单一分类适用于所有的训练数据提取自同一个类里，然后SVM建立了一个分界线以分割该类在特征空间中所占区域和其它类在特征空间中所占区域。  
* Kernel：在非线性可分时，我们引入核函数来做非线性可分，  
核函数的价值在于它虽然也是将特征进行从低维到高维的转换，但它只需在低维上进行计算，就可将实质上的分类效果表现在了高维上，从而避免直接在高维空间中进行复杂计算。   
>R提供的核介绍如下：  
 * 线性核:$K(u,v)=u' \ast v$  
 * 多项式核:$K(u,v)=(\gamma \ast u' \ast v + coef0)^{degree}$  
 * 高斯核 （,RBF）:$K(u,v)=e^{-\gamma \ast |u-v|^2}$    
 * Sigmoid核: $K(u,v)=tanh(\gamma \ast u' \ast v + coef0)$  
默认为高斯核（RBF）,<b>优先建议高斯核</b>   

<br />  
* Degree:多项式核的次数，默认为3  
* Gamma:除去线性核外，其他的核的参数，默认为1/数据维数  
* Coef0,：多项式核与sigmoid核的参数，默认为0  
* Cost：C分类的惩罚项C的取值  
* Nu：nu分类，单一分类中nu的取值  
* Cross：做K折交叉验证，计算分类正确性。


```R
x=seq(-5,5,length.out=100)
dft=data.frame(x=x,y1=1/(1+exp(-x)),y2=1/(1+exp(-5*x)))
opt(3,3)
ggplot(dft)+
  theme_bw()+
  geom_line(aes(x,y1),col="blue")+
  geom_line(aes(x,y2),col="darkgreen")
```




![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_170_1.png)



```R
# e1071::svm
library(e1071)
data(iris)
iris.svm=svm(Species~., data=iris, method="C-classification",
            kernel="radial", cost=10, gamma=.1)
summary(iris.svm)
```

    Warning message:
    "package 'e1071' was built under R version 3.5.3"


    
    Call:
    svm(formula = Species ~ ., data = iris, method = "C-classification", 
        kernel = "radial", cost = 10, gamma = 0.1)
    
    
    Parameters:
       SVM-Type:  C-classification 
     SVM-Kernel:  radial 
           cost:  10 
    
    Number of Support Vectors:  32
    
     ( 3 16 13 )
    
    
    Number of Classes:  3 
    
    Levels: 
     setosa versicolor virginica
    
    
    



```R
iris.svm$SV                 #查看支持向量的情况
iris.svm$index           #查看支持向量的标签
iris.svm$rho               #查看分类时的截距b
```


<table>
<thead><tr><th></th><th scope=col>Sepal.Length</th><th scope=col>Sepal.Width</th><th scope=col>Petal.Length</th><th scope=col>Petal.Width</th></tr></thead>
<tbody>
	<tr><th scope=row>16</th><td>-0.17309407</td><td> 3.08045544</td><td>-1.2791040 </td><td>-1.0486668 </td></tr>
	<tr><th scope=row>24</th><td>-0.89767388</td><td> 0.55674567</td><td>-1.1658087 </td><td>-0.9174741 </td></tr>
	<tr><th scope=row>42</th><td>-1.62225369</td><td>-1.73753594</td><td>-1.3923993 </td><td>-1.1798595 </td></tr>
	<tr><th scope=row>51</th><td> 1.39682886</td><td> 0.32731751</td><td> 0.5336209 </td><td> 0.2632600 </td></tr>
	<tr><th scope=row>53</th><td> 1.27606556</td><td> 0.09788935</td><td> 0.6469162 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>54</th><td>-0.41462067</td><td>-1.73753594</td><td> 0.1370873 </td><td> 0.1320673 </td></tr>
	<tr><th scope=row>55</th><td> 0.79301235</td><td>-0.59039513</td><td> 0.4769732 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>58</th><td>-1.13920048</td><td>-1.50810778</td><td>-0.2594462 </td><td>-0.2615107 </td></tr>
	<tr><th scope=row>67</th><td>-0.29385737</td><td>-0.13153881</td><td> 0.4203256 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>69</th><td> 0.43072244</td><td>-1.96696410</td><td> 0.4203256 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>71</th><td> 0.06843254</td><td> 0.32731751</td><td> 0.5902685 </td><td> 0.7880307 </td></tr>
	<tr><th scope=row>73</th><td> 0.55148575</td><td>-1.27867961</td><td> 0.6469162 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>77</th><td> 1.15530226</td><td>-0.59039513</td><td> 0.5902685 </td><td> 0.2632600 </td></tr>
	<tr><th scope=row>78</th><td> 1.03453895</td><td>-0.13153881</td><td> 0.7035638 </td><td> 0.6568380 </td></tr>
	<tr><th scope=row>79</th><td> 0.18919584</td><td>-0.36096697</td><td> 0.4203256 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>84</th><td> 0.18919584</td><td>-0.81982329</td><td> 0.7602115 </td><td> 0.5256453 </td></tr>
	<tr><th scope=row>85</th><td>-0.53538397</td><td>-0.13153881</td><td> 0.4203256 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>86</th><td> 0.18919584</td><td> 0.78617383</td><td> 0.4203256 </td><td> 0.5256453 </td></tr>
	<tr><th scope=row>99</th><td>-0.89767388</td><td>-1.27867961</td><td>-0.4293892 </td><td>-0.1303181 </td></tr>
	<tr><th scope=row>107</th><td>-1.13920048</td><td>-1.27867961</td><td> 0.4203256 </td><td> 0.6568380 </td></tr>
	<tr><th scope=row>111</th><td> 0.79301235</td><td> 0.32731751</td><td> 0.7602115 </td><td> 1.0504160 </td></tr>
	<tr><th scope=row>119</th><td> 2.24217198</td><td>-1.04925145</td><td> 1.7798692 </td><td> 1.4439941 </td></tr>
	<tr><th scope=row>120</th><td> 0.18919584</td><td>-1.96696410</td><td> 0.7035638 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>124</th><td> 0.55148575</td><td>-0.81982329</td><td> 0.6469162 </td><td> 0.7880307 </td></tr>
	<tr><th scope=row>127</th><td> 0.43072244</td><td>-0.59039513</td><td> 0.5902685 </td><td> 0.7880307 </td></tr>
	<tr><th scope=row>128</th><td> 0.30995914</td><td>-0.13153881</td><td> 0.6469162 </td><td> 0.7880307 </td></tr>
	<tr><th scope=row>130</th><td> 1.63835547</td><td>-0.13153881</td><td> 1.1567451 </td><td> 0.5256453 </td></tr>
	<tr><th scope=row>132</th><td> 2.48369858</td><td> 1.70388647</td><td> 1.4966310 </td><td> 1.0504160 </td></tr>
	<tr><th scope=row>134</th><td> 0.55148575</td><td>-0.59039513</td><td> 0.7602115 </td><td> 0.3944526 </td></tr>
	<tr><th scope=row>135</th><td> 0.30995914</td><td>-1.04925145</td><td> 1.0434497 </td><td> 0.2632600 </td></tr>
	<tr><th scope=row>139</th><td> 0.18919584</td><td>-0.13153881</td><td> 0.5902685 </td><td> 0.7880307 </td></tr>
	<tr><th scope=row>150</th><td> 0.06843254</td><td>-0.13153881</td><td> 0.7602115 </td><td> 0.7880307 </td></tr>
</tbody>
</table>




<ol class=list-inline>
	<li>16</li>
	<li>24</li>
	<li>42</li>
	<li>51</li>
	<li>53</li>
	<li>54</li>
	<li>55</li>
	<li>58</li>
	<li>67</li>
	<li>69</li>
	<li>71</li>
	<li>73</li>
	<li>77</li>
	<li>78</li>
	<li>79</li>
	<li>84</li>
	<li>85</li>
	<li>86</li>
	<li>99</li>
	<li>107</li>
	<li>111</li>
	<li>119</li>
	<li>120</li>
	<li>124</li>
	<li>127</li>
	<li>128</li>
	<li>130</li>
	<li>132</li>
	<li>134</li>
	<li>135</li>
	<li>139</li>
	<li>150</li>
</ol>




<ol class=list-inline>
	<li>-0.260498081180212</li>
	<li>0.0200653246232933</li>
	<li>-0.0872511895970034</li>
</ol>




```R
opt(6,4)
plot(iris.svm, iris, Petal.Width~Petal.Length, 
     slice=list(Sepal.Width=3,Sepal.Length = 4))
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_173_0.png)



```R
cf=coef(iris.svm);cf 
```


    Error in coef.svm(iris.svm): Only implemented for regression or binary classification with linear kernel.
    Traceback:
    

    1. coef(iris.svm)

    2. coef.svm(iris.svm)

    3. stop("Only implemented for regression or binary classification with linear kernel.")



    Error in eval(expr, envir, enclos): object 'cf' not found
    Traceback:
    


# 神经网络  
NNET is essentially about minimizing cost function  
<font color='#F8D86A'>复习要点：BP算法，**优化机制**，**PPT中的文字/思想部分** </font>

go to https://blog.csdn.net/illikang/article/details/82019945  
https://www.youtube.com/watch?v=aircAruvnKk  
自己写神经网络代码，参考 https://blog.csdn.net/kMD8d5R/article/details/85086412 【不完整】

the simplest nnet mode - https://www.youtube.com/watch?v=aircAruvnKk  
example used throughout: hand-written digits recognition  
28*28 pixel grid  
   
>some basic ideas to keep in mind:  
1. 设计一个神经网络时，输入层与输出层的节点数往往是固定的，中间层则可以自由指定；
2. 神经网络结构图中的拓扑与箭头代表着预测过程时数据的流向，跟训练时的数据流有一定的区别；
3. 结构图里的关键不是圆圈（代表“神经元”），而是连接线（代表“神经元”之间的连接）。每个连接线对应一个不同的权重（其值称为权值），这是需要训练得到的。一个神经网络的训练算法就是**让权重的值调整到最佳**，以使得整个网络的预测效果最好。  
4. 通常我们说$k$层神经网络，是说有多少个连接层而非节点层。  

![basic](./nnet_pic00.jpg)

## crash course on HISTORY of NNET  
### MP模型/神经元模型  
（因提出者为心理学家McCulloch和数学家Pitts而得名，参考神经元结构，也叫神经元模型）  
 MP模型中，权重的值都是预先设置的，因此不能学习。  
 * 同时期还有Hebb模型，提出了调整权重以使机器学习，为学习算法奠定基础。

### 单层神经网络（单连接层，两层神经元，感知器模型）
 * 首个可以学习的人工神经网络  
 ![单层](./nnet_pic02.jpg)  
 * 从前一层到后一层的计算可以用矩阵表示：$g(W \cdot a) = z$  
 * 与神经元模型不同，感知器中的权值是通过训练得到的。感知器类似一个逻辑回归模型，可以做线性分类任务。决策分界就是在二维的数据平面中划出一条直线，当数据的维度是3维的时候，就是划出一个平面，当数据的维度是n维时，就是划出一个n-1维的超平面。  
 * 感知器模型只能做线性分类任务，对XOR（异或）这样的简单分类任务都无法解决。

### 两层神经网络（多层感知器）  
 * 1986年，Rumelhar和Hinton等人提出了反向传播（Backpropagation，BP）算法，解决了两层神经网络所需要的复杂计算量问题。【BP算法是两层神经网络的计算思路】  
![两层1](./nnet_pic03.jpg)   
![两层2](./nnet_pic04.jpg)  
 * 进一步考虑偏置（bias），矩阵表示为：  
$g(W(1) \cdot a(1)+b(1)) = a(2)$   
$g(W(2) \cdot a(2)+b(2)) = z$
 * 两层神经网络中的激活函数(active function)$g()$不再是$sgn$函数，而是平滑函数$sigmoid$。  
>神经网络的本质就是通过参数与激活函数来拟合特征与目标之间的真实函数关系（说白了就是复杂函数拟合）。初学者可能认为画神经网络的结构图是为了在程序中实现这些圆圈与线，但在一个神经网络的程序中，既没有“线”这个对象，也没有“单元”这个对象。实现一个神经网络最需要的是线性代数库。  
 * 与只能做线性分类任务的单层神经网络不同，两层神经网络可以无限逼近任意连续函数【万能近似定理（Universal Approximation Theorem）】，故可以做非线性分类任务。两层神经网络可以做非线性分类的关键--隐藏层。联想到我们一开始推导出的矩阵公式，我们知道，矩阵和向量相乘，本质上就是对向量的坐标空间进行一个变换。因此，隐藏层的参数矩阵的作用就是使得数据的原始坐标空间从线性不可分，转换成了线性可分。  
>隐藏层节点数的设置  
中间层的节点数是由设计者指定的，节点数会影响到整个模型的效果。目前业界没有完善的理论来指导节点数的设置。一般是根据经验来设置。较好的方法就是预先设定几个可选值，通过切换这几个值来看整个模型的预测效果，选择效果最好的值作为最终选择。这种方法又叫做Grid Search（网格搜索）。


### 多层神经网络  
think about this - 既然两层神经网络就能拟合一切…为什么还需要多层神经网络？


## Layers
First layer of network:
Neurons (神经元) -> sth. that hold a number

Last layer:
ten neuron each representing one digit(0-9)

two hidden layers 16 neurons each
randomly chosen?

activation of one layers determines the activation of the next 
(or, some groups of neurons firing cause others to fire?)

the brightest neuron in the output layers is the one we go with

examples : the third layers may be constituted by loops, lines(dif location/direction, dif length)
the second may be smaller parts that make up loops blablabla

## Parameters - How does one layer leads to another
each neurons in the next layer are connected to all the neurons in the previous layer  
activations from the 1st layers 784=28$\times$28 numbers (column $\alpha$)   
-> weighted sum (weights - matrix \omega) + bias   
-> activations from the 2nd layers  (also column, resulting from  $\omega \times \alpha$? ) (e.g. first layers are 784=28$\times$28 pixels then the weight is a 16*784 matrix?)  
* all 16 neurons in the 2nd layers have its set of weight and its bias  
  
>激活函数the active function  
note that the weighted sum can be any real number  
but we want the index of "brightness" to be between 0 and 1  
so we need a function to squeeze the output i.e. the active function - 作用：挤压/压缩 
for example a **sigmoid function**   
 * $sigmoid(x)=\sigma(x)$ - 多用于两层神经网络
  * 通式：$\sigma(x)=\frac{1}{1+e^{-\alpha x}}$，导数 $\sigma \dot (x) =\frac{\alpha e^{-\alpha x}}{(1+e^{-\alpha x})^2}=\alpha \sigma(x) (1-\sigma(x))$
   * $\alpha$决定S型曲线的陡峭程度
  * $\sigma(x)=\frac{1}{1+e^{-x}}$ 其导数$\sigma \dot (x) = \frac{e^{-x}}{(1+e^{-x})^2}=\sigma(x) (1-\sigma(x))$
  * $\sigma(x)=\frac{e^x}{e^x+e^{-x}}=\frac{1}{1+e^{-2x}}$
 * ReLU - 多用于多层神经网络
 * Signal - 多用于单层神经网络
 * 分段线性函数  
激活函数通常连续且可导  
激活函数的导数非常关键！要好算！  

* bias can be introduced to the sum weighted before entering sigmoid    
* weights and bias  -> unknown parameters to be determined   
  
* we can look at what each layer does by visualizing the weight (not the weight matrix, but each 1*781 row of the matrix, and reshape it into a 28$\times$28 small matrix) ( to see what importance and what directions it gives to each neurons in the previous layer)  

![connections](./nnet_pic01.jpg)  
我们使用a来表示输入，用w来表示权值。一个表示连接的有向箭头可以这样理解：在初端，传递的信号大小仍然是a，端中间有加权参数w，经过这个加权后的信号会变成a$\times$w，因此在连接的末端，信号的大小就变成了a$\times$w。  
z在输入和权值的线性加权和（$\sum(a\cdot w)$）叠加了一个激活函数$g()$的值。在MP模型里，激活函数$g()$是$sgn$函数，更常用的是$sigmoid$和$ReLU$等  

## how to achieve that throught an algorithm?
we start all those unknown parameters i.e. weights & bias **randomly**  
so the initial performance would be terrible  
  
>COST FUNCTION  (input: all unknown para including weights and bias, output: how bad this model is)  
  损失函数$E(w)=1/2 (T_i-o_i )^2$：  
  其中 $T$（Target,目标值即真实值） ，$O$（Output,模型输出）  
 * the function value will be small if output is correct  
 * would be large if output is trash  
 * average cost(over all sample data) captures how lousy the network is for the full panel of sample  

  
so the prob would then be how to minimize the cost function - 如何优化参数，能够让损失函数的值最小。  
one possible solution - > **gradient descent 梯度下降**  
  
* 导数tell us which changes in parameters ( or changing which para) matter most  

## interesting stuffs (from the landscape literature)
using structured data to train a network allows one to achieve local minima much more quickly(quick drop in accuracy)  
using a randomly labeled data on the otherhand does not  


## BP神经网络

<font size=3>* 特点：两层激活，从后向前（先C-B再B-A）  
![BP](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/nnet_pic05.png)    
  
* A 输入层，下标i  
 *  a1 输入：$x_i$  
 *  a2 输出：\  
  
* B 隐藏层，下标h  
 *  b1 输入：线性组合$z_h=\sum_{i}(w_{ih} \cdot x_i)$  
 *  b2 输出：$o_h=\sigma_h (Z_h+\alpha_h)$ （线性组合+偏移）  
	也可以用$\phi()$或$g()$表示激活函数，but usl. **sigmoid函数**   
      故BP中符号直接是$\sigma()$  
    
* C 输出层，下标k  
 *  c1 输入：线性组合$y_k=\sum_{h}(w_{hk} \cdot o_h )$  
 *  c2 输出：sigmoid激活函数（线性组合+偏移）$o_k^`=\sigma_k (y_k+\alpha_k)$  
</font>

R实现：nnet  
绘图：neural net tools (actually I haven't figure out how to use this package)  
plot.nnet (as used below)


```R
data("iris")
n=nrow(iris)
loc=sample(n,n*.7)
train=iris[loc,]
test=iris[-loc,]
head(train)
```


<table>
<thead><tr><th></th><th scope=col>Sepal.Length</th><th scope=col>Sepal.Width</th><th scope=col>Petal.Length</th><th scope=col>Petal.Width</th><th scope=col>Species</th></tr></thead>
<tbody>
	<tr><th scope=row>40</th><td>5.1       </td><td>3.4       </td><td>1.5       </td><td>0.2       </td><td>setosa    </td></tr>
	<tr><th scope=row>42</th><td>4.5       </td><td>2.3       </td><td>1.3       </td><td>0.3       </td><td>setosa    </td></tr>
	<tr><th scope=row>62</th><td>5.9       </td><td>3.0       </td><td>4.2       </td><td>1.5       </td><td>versicolor</td></tr>
	<tr><th scope=row>13</th><td>4.8       </td><td>3.0       </td><td>1.4       </td><td>0.1       </td><td>setosa    </td></tr>
	<tr><th scope=row>56</th><td>5.7       </td><td>2.8       </td><td>4.5       </td><td>1.3       </td><td>versicolor</td></tr>
	<tr><th scope=row>80</th><td>5.7       </td><td>2.6       </td><td>3.5       </td><td>1.0       </td><td>versicolor</td></tr>
</tbody>
</table>




```R
library(nnet)
iris.nn=nnet(Species~.,data=train,
  size=2,rang=.1,decay=5e-4,maxit=200)
  #size: 隐藏层节点个数，但只能有一个隐藏层，这是nnet包的一大缺陷
  #rang: initial random weights
  #decay: 默认值0，防止过拟合
summary(iris.nn)
pred=predict(iris.nn,test,type="class")
```

    # weights:  19
    initial  value 115.418251 
    iter  10 value 50.157787
    iter  20 value 47.436806
    iter  30 value 43.476192
    iter  40 value 23.077417
    iter  50 value 7.812110
    iter  60 value 5.625027
    iter  70 value 5.281502
    iter  80 value 5.042058
    iter  90 value 4.491077
    iter 100 value 3.357326
    iter 110 value 2.698912
    iter 120 value 2.446648
    iter 130 value 2.302550
    iter 140 value 2.206402
    iter 150 value 2.168187
    iter 160 value 2.142581
    iter 170 value 2.137935
    iter 180 value 2.134625
    iter 190 value 2.133921
    iter 200 value 2.133671
    final  value 2.133671 
    stopped after 200 iterations
    


    a 4-2-3 network with 19 weights
    options were - softmax modelling  decay=5e-04
     b->h1 i1->h1 i2->h1 i3->h1 i4->h1 
     -0.27  -0.74  -1.79   2.99   1.62 
     b->h2 i1->h2 i2->h2 i3->h2 i4->h2 
     27.96   1.20   4.29  -5.06 -13.10 
     b->o1 h1->o1 h2->o1 
      4.60 -11.94   7.85 
     b->o2 h1->o2 h2->o2 
    -10.85   5.13  15.39 
     b->o3 h1->o3 h2->o3 
      6.26   6.80 -23.24 



```R
tab=table(test$Species, pred, dnn=c("Target","Output"))
tab
```


                Output
    Target       setosa versicolor virginica
      setosa         12          0         0
      versicolor      0         14         0
      virginica       0          2        17



```R
# plot.nnet from github
# https://gist.githubusercontent.com/fawda123/5086859/raw/17fd6d2adec4dbcf5ce750cbd1f3e0f4be9d8b19/nnet_plot_fun.r
plot.nnet=function(mod.in,nid=T,all.out=T,all.in=T,wts.only=F,rel.rsc=5,circle.cex=5,node.labs=T,
  line.stag=NULL,cex.val=1,alpha.val=1,circle.col='lightgrey',pos.col='black',neg.col='grey',...){

  require(scales)
	
  #gets weights for neural network, output is list
  #if rescaled argument is true, weights are returned but rescaled based on abs value
  nnet.vals=function(mod.in,nid,rel.rsc){

    library(scales)
    layers=mod.in$n
    wts=mod.in$wts
    if(nid) wts=rescale(abs(wts),c(1,rel.rsc))

    indices=matrix(seq(1,layers[1]*layers[2]+layers[2]),ncol=layers[2])
    out.ls=list()
    for(i in 1:ncol(indices)){
      out.ls[[paste('hidden',i)]]=wts[indices[,i]]
      }

    if(layers[3]==1) out.ls[['out 1']]=wts[(max(indices)+1):length(wts)]
    else{
      out.indices=matrix(seq(max(indices)+1,length(wts)),ncol=layers[3])
      for(i in 1:ncol(out.indices)){
        out.ls[[paste('out',i)]]=wts[out.indices[,i]]
        }
      }

    out.ls

    }

  wts=nnet.vals(mod.in,nid=F)

  if(wts.only) return(wts)

  #par(mar=numeric(4),oma=numeric(4),family='serif')
  library(scales)
  struct=mod.in$n
  x.range=c(0,100)
  y.range=c(0,100)
  #these are all proportions from 0-1
	if(is.null(line.stag)) line.stag=0.011*circle.cex/2
  layer.x=seq(0.17,0.9,length=3)
  bias.x=c(mean(layer.x[1:2]),mean(layer.x[2:3]))
  bias.y=0.95
	in.col=bord.col=circle.col
  circle.cex=circle.cex
	
  #get variable names from nnet object
	if(is.null(mod.in$call$formula)){
		x.names=colnames(eval(mod.in$call$x))
		y.names=colnames(eval(mod.in$call$y))
		}
	else{
		forms=eval(mod.in$call$formula)
		dat.names=model.frame(forms,data=eval(mod.in$call$data))
		y.names=as.character(forms)[2]
		x.names=names(dat.names)[!names(dat.names) %in% y.names]
		}
	
	#initiate plot
  plot(x.range,y.range,type='n',axes=F,ylab='',xlab='',...)

  #function for getting y locations for input, hidden, output layers
  #input is integer value from 'struct'
  get.ys=function(lyr){
    spacing=diff(c(0*diff(y.range),0.9*diff(y.range)))/max(struct)
    seq(0.5*(diff(y.range)+spacing*(lyr-1)),0.5*(diff(y.range)-spacing*(lyr-1)),
      length=lyr)
    }

  #function for plotting nodes
  #'layer' specifies which layer, integer from 'struct'
  #'x.loc' indicates x location for layer, integer from 'layer.x'
  #'layer.name' is string indicating text to put in node
  layer.points=function(layer,x.loc,layer.name,cex=cex.val){
		x=rep(x.loc*diff(x.range),layer)
    y=get.ys(layer)
    points(x,y,pch=21,cex=circle.cex,col=in.col,bg=bord.col)
    if(node.labs) text(x,y,paste(layer.name,1:layer,sep=''),cex=cex.val)
    if(layer.name=='I' & node.labs){
      text(x-line.stag*diff(x.range),y,x.names,pos=2,cex=cex.val)
    	}	
    if(layer.name=='O' & node.labs)
      text(x+line.stag*diff(x.range),y,y.names,pos=4,cex=cex.val)
    }

  #function for plotting bias points
  #'bias.x' is vector of values for x locations
  #'bias.y' is vector for y location
  #'layer.name' is  string indicating text to put in node
  bias.points=function(bias.x,bias.y,layer.name,cex,...){
    for(val in 1:length(bias.x)){
      points(
        diff(x.range)*bias.x[val],
        bias.y*diff(y.range),
        pch=21,col=in.col,bg=bord.col,cex=circle.cex
        )
      if(node.labs)
        text(
          diff(x.range)*bias.x[val],
          bias.y*diff(y.range),
          paste(layer.name,val,sep=''),
          cex=cex.val
        )
      }
    }

  #function creates lines colored by direction and width as proportion of magnitude
  #use 'all.in' argument if you want to plot connection lines for only a single input node
  layer.lines=function(mod.in,h.layer,layer1=1,layer2=2,out.layer=F,nid,rel.rsc,all.in,pos.col,
  	neg.col,...){

    x0=rep(layer.x[layer1]*diff(x.range)+line.stag*diff(x.range),struct[layer1])
    x1=rep(layer.x[layer2]*diff(x.range)-line.stag*diff(x.range),struct[layer1])

    if(out.layer==T){

      y0=get.ys(struct[layer1])
      y1=rep(get.ys(struct[layer2])[h.layer],struct[layer1])
      src.str=paste('out',h.layer)

      wts=nnet.vals(mod.in,nid=F,rel.rsc)
      wts=wts[grep(src.str,names(wts))][[1]][-1]
      wts.rs=nnet.vals(mod.in,nid=T,rel.rsc)
      wts.rs=wts.rs[grep(src.str,names(wts.rs))][[1]][-1]

      cols=rep(pos.col,struct[layer1])
      cols[wts<0]=neg.col

      if(nid) segments(x0,y0,x1,y1,col=cols,lwd=wts.rs)
      else segments(x0,y0,x1,y1)

      }
      
    else{

      if(is.logical(all.in)) all.in=h.layer
      else all.in=which(x.names==all.in)

      y0=rep(get.ys(struct[layer1])[all.in],struct[2])
      y1=get.ys(struct[layer2])
      src.str='hidden'

      wts=nnet.vals(mod.in,nid=F,rel.rsc)
      wts=unlist(lapply(wts[grep(src.str,names(wts))],function(x) x[all.in+1]))
      wts.rs=nnet.vals(mod.in,nid=T,rel.rsc)
      wts.rs=unlist(lapply(wts.rs[grep(src.str,names(wts.rs))],function(x) x[all.in+1]))

      cols=rep(pos.col,struct[layer2])
      cols[wts<0]=neg.col

      if(nid) segments(x0,y0,x1,y1,col=cols,lwd=wts.rs)
      else segments(x0,y0,x1,y1)

      }

    }

  bias.lines=function(bias.x,mod.in,nid,rel.rsc,all.out,pos.col,neg.col,...){

    if(is.logical(all.out)) all.out=1:struct[3]
    else all.out=which(y.names==all.out)
    
    for(val in 1:length(bias.x)){

      wts=nnet.vals(mod.in,nid=F,rel.rsc)
      wts.rs=nnet.vals(mod.in,nid=T,rel.rsc)

      if(val==1){
        wts=wts[grep('out',names(wts),invert=T)]
        wts.rs=wts.rs[grep('out',names(wts.rs),invert=T)]
        }

      if(val==2){
        wts=wts[grep('out',names(wts))]
        wts.rs=wts.rs[grep('out',names(wts.rs))]
        }

      cols=rep(pos.col,length(wts))
      cols[unlist(lapply(wts,function(x) x[1]))<0]=neg.col
      wts.rs=unlist(lapply(wts.rs,function(x) x[1]))

      if(nid==F){
        wts.rs=rep(1,struct[val+1])
        cols=rep('black',struct[val+1])
        }

      if(val==1){
        segments(
          rep(diff(x.range)*bias.x[val]+diff(x.range)*line.stag,struct[val+1]),
          rep(bias.y*diff(y.range),struct[val+1]),
          rep(diff(x.range)*layer.x[val+1]-diff(x.range)*line.stag,struct[val+1]),
          get.ys(struct[val+1]),
          lwd=wts.rs,
          col=cols
          )
        }

      if(val==2){
        segments(
          rep(diff(x.range)*bias.x[val]+diff(x.range)*line.stag,struct[val+1]),
          rep(bias.y*diff(y.range),struct[val+1]),
          rep(diff(x.range)*layer.x[val+1]-diff(x.range)*line.stag,struct[val+1]),
          get.ys(struct[val+1])[all.out],
          lwd=wts.rs[all.out],
          col=cols[all.out]
          )
        }

      }
    }

  #use functions to plot connections between layers
  #bias lines
  bias.lines(bias.x,mod.in,nid=nid,rel.rsc=rel.rsc,all.out=all.out,pos.col=alpha(pos.col,alpha.val),
  	neg.col=alpha(neg.col,alpha.val))

  #layer lines, makes use of arguments to plot all or for individual layers
  #starts with input-hidden
  #uses 'all.in' argument to plot connection lines for all input nodes or a single node
  if(is.logical(all.in)){
    mapply(
      function(x) layer.lines(mod.in,x,layer1=1,layer2=2,nid=nid,rel.rsc=rel.rsc,all.in=all.in,
      	pos.col=alpha(pos.col,alpha.val),neg.col=alpha(neg.col,alpha.val)),
      1:struct[1]
      )
    }
  else{
    node.in=which(x.names==all.in)
    layer.lines(mod.in,node.in,layer1=1,layer2=2,nid=nid,rel.rsc=rel.rsc,all.in=all.in,
    	pos.col=alpha(pos.col,alpha.val),neg.col=alpha(neg.col,alpha.val))
    }

  #lines for hidden-output
  #uses 'all.out' argument to plot connection lines for all output nodes or a single node
  if(is.logical(all.out))
    mapply(
      function(x) layer.lines(mod.in,x,layer1=2,layer2=3,out.layer=T,nid=nid,rel.rsc=rel.rsc,
        all.in=all.in,pos.col=alpha(pos.col,alpha.val),neg.col=alpha(neg.col,alpha.val)),
      1:struct[3]
      )
  else{
    all.out=which(y.names==all.out)
    layer.lines(mod.in,all.out,layer1=2,layer2=3,out.layer=T,nid=nid,rel.rsc=rel.rsc,
    	pos.col=pos.col,neg.col=neg.col)
    }

  #use functions to plot nodes
  layer.points(struct[1],layer.x[1],'I')
  layer.points(struct[2],layer.x[2],'H')
  layer.points(struct[3],layer.x[3],'O')
  bias.points(bias.x,bias.y,'B')

  }
```


```R
plot.nnet(iris.nn,cex=.7)
```


![png](https://github.com/yanyul987/yanyul987.github.io/tree/master/_posts/image/2019-6-22-DataMining/output_193_0.png)


设置更多参数  
见https://www.r-bloggers.com/visualizing-neural-networks-from-the-nnet-package/  
![table](./nnet_plot_pic01.png)

## 卷积神经网络
