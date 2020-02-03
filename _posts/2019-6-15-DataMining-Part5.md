---
published: true
title: 数据挖掘期末复习汇总（五）——LDA&SVM
category: Data Mining
tags: 
  - DataMining
layout: post

---

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

* <font color='#9a4238'>掌握线性可分情况，其他情况了解，回归情况不要求  
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
![svm示意](/assets/images/201906DataMining/svm_pic01.png)  
在进行分类的时候，遇到一个新的数据点x，将x代入f(x) 中，如果f(x)小于0则将x的类别赋为-1，如果f(x)大于0则将x的类别赋为1。  
<br />
2. the BEST SPLIT  
对一个数据点进行分类，当超平面离数据点的“间隔”(margin)越大，分类的确信度（confidence）也越大。所以，为了使得分类的确信度尽量高，需要让所选择的超平面能够最大化这个“间隔”值。这个间隔就是下图中的Gap的一半。  
![svm示意2](/assets/images/201906DataMining/svm_pic02.png)

SVM的作用是分类器，本质是有约束的最优化问题(constrained optimization problem）  
1. 最优化 - 让路最宽  
$max \tilde{\gamma}$  
可以somehow转化成$max \frac{1}{||\omega||} $  
2. 约束条件 - “点都不在路上”  
$y_i(\omega^T x_i+b)>=1,  i=1,2,...,n$  
![svm示意3](/assets/images/201906DataMining/svm_pic03.png)  
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




![png](/assets/images/201906DataMining/output_170_1.png)



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


![png](/assets/images/201906DataMining/output_173_0.png)



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
    
