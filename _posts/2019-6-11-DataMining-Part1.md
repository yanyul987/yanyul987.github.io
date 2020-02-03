---
published: true
title: 数据挖掘期末复习汇总（一）
category: Data Mining
tags: 
  - DataMining
layout: post

---

```R
library(ggplot2)
library(ggthemes)
opt=function(wid,hei) {options(repr.plot.width=wid,repr.plot.height=hei)}
# color - bright lemon - #9a4238
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
 ![class](/assets/images/201906DataMining/class_pic01.png)  
 SVM使用了非线性分类器，而logistics和decision tree都是线性的？

## 损失函数
通常机器学习每一个算法中都会有一个目标函数，算法的求解过程是通过对这个目标函数优化的过程。在分类或者回归问题中，通常使用损失函数作为其目标函数。损失函数用来评价模型的预测值和真实值不一样的程度，损失函数越好，通常模型的性能越好。不同的算法使用的损失函数不一样。  
<br />

<font color='#9a4238'>损失函数分为经验风险损失函数和结构风险损失函数。经验风险损失函数指预测结果和实际结果的差别，结构风险损失函数是指经验风险损失函数加上正则项</font>。通常表示为如下：  
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


![png](/assets/images/201906DataMining/output_11_0.png)


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
 but <font color='#9a4238'> with Ridge Reg we can estimate the model with 10000 variables using only 500 or less obs  
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
  
Lasso回归和Ridge回归**复杂度调整的程度由参数$\lambda$来控制**，<font color='#9a4238'>$\lambda$越大模型复杂度的惩罚力度越大，从而获得一个较少变量的模型。</font>  

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


![png](/assets/images/201906DataMining/output_25_0.png)


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




![png](/assets/images/201906DataMining/output_36_1.png)



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
