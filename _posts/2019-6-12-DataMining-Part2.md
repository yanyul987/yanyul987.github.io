---
published: true
title: 数据挖掘期末复习汇总（二）
category: Data Mining
tags: 
  - DataMining
  - Clustering
layout: post

---

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
![距离图](/assets/images/201906DataMining/clustering_pic01.png)  
到中心点距离相同的点用同一种颜色表示  
<br />  

1. Manhattan distance / Block distance   
$d_{ij}(1)=\sum_{k=1}^{p}|x_{ik}-x_{jk}|$  
$d_{AB}=|x_A-x_B|+|y_A-y_B|$
2. Euclidean distance  
$d_{ij}(2)=(\sum_{k=1}^{p}|x_{ik}-x_{jk}|^2)^{1/2}$  
$d_{AB}=\sqrt{(x_A-x_B)^2+(y_A-y_B)^2}$  
 * <font color='#9a4238'>the most frequently used  
 * 优点：有非常明确的空间距离概念
 * 缺点  
    没有区分量纲的不同，将所有变量视为影响相同的 —— 适用于已做标准化处理的数据（preprocessed data）  
    受维度的影响？</font>  

3. Chebyshev distance  
$d_{ij}(\inf)=max|x_{ik}-x_{jk}|$  

4. Mahalanobis distance 马氏距离  
$d_{ij}(M)=((X_i-X_j)' A^{-1} (X_i-X_j))^{\frac{1}{2}}$  
$𝐴$ represents the variance matrix, $𝑋_𝑖$ represents sample 𝑖 with 𝑝 dimensions  
 * <font color='#9a4238'>马氏距离又称广义欧氏距离  
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


![png](/assets/images/201906DataMining/output_46_0.png)



![png](/assets/images/201906DataMining/output_46_1.png)



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




![png](/assets/images/201906DataMining/output_49_1.png)



```R
opt(10,10)
plot(pamk,col=pamk$cluster)
```

    Warning message in if (color) {:
    "the condition has length > 1 and only the first element will be used"Warning message in if (color) c(2, 4, 6, 3) else 5:
    "the condition has length > 1 and only the first element will be used"


![png](/assets/images/201906DataMining/output_50_1.png)



![png](/assets/images/201906DataMining/output_50_2.png)


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

![em](/assets/images/201906DataMining/em_pic01.png)

<font color='#9a4238'>  
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


![png](/assets/images/201906DataMining/output_58_0.png)



```R
hc=hclust(dist(dmat))
clus=cutree(hc,4)

opt(4,4)
image(t(dmat),yaxt="n")
opt(4,4)
image(t(dmat[order(clus),]),yaxt="n")
```


![png](/assets/images/201906DataMining/output_59_0.png)



![png](/assets/images/201906DataMining/output_59_1.png)



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


![png](/assets/images/201906DataMining/output_63_0.png)



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
<font color='#9a4238'>比较一个点到所在类各点的平均距离和**最近的**其他类各点的平均距离</font>  
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
* <font color='#9a4238'>$\bar{sw}$值越大，聚类效果越好，从而确定类数$k$</font>  
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


![png](/assets/images/201906DataMining/output_72_1.png)



![png](/assets/images/201906DataMining/output_72_2.png)



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


![png](/assets/images/201906DataMining/output_73_2.png)



![png](/assets/images/201906DataMining/output_73_3.png)


<font color='#9a4238'>分为3类时的sw_avg(0.55)比4类(0.49)大，说明聚成3类效果更好</font>

### WSS(Within-cluster Sum of Squares)  
$D_r=\sum_{i \in C_r}\sum_{j \in C_r}||x_i-x_j||^2=2n_r \sum_{i \in C_r}||x_i-\bar{x}||^2$  
$W_k=\sum_{r=1}^{k}\frac{1}{2n_r}D_r$  
![WSS](/assets/images/201906DataMining/clustering_pic02.png)  
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
<font color='#9a4238'>the larger Gap the better (find the k that maximizes Gap(k)</font>  
![gap](/assets/images/201906DataMining/clustering_pic03.png)  
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



![png](/assets/images/201906DataMining/output_78_1.png)



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





![png](/assets/images/201906DataMining/output_79_2.png)


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
![rand index](/assets/images/201906DataMining/clustering_pic04.jpg)

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
 ![距离](/assets/images/201906DataMining/clustering_pic06.png)  
2. 得到共表(Cophenetic)矩阵  
  > 共表矩阵的计算：使用在合并类的过程中使用的最小距离来填充距离矩阵的下三角。  
  例：开始有6个类：A，B，C，D，E，F$\rightarrow$将距离最小为0.5的D和F聚为一类(D,F)$\rightarrow$将距离最小为0.71的A和B聚为一类(A,B)$\rightarrow$将距离最小为1.00的(D,F)和E聚为一类(D,F,E)$\rightarrow$将距离最小为1.41的(D,F,E)和C聚为一类(D,F,E,C)$\rightarrow$将距离最小为2.50的(D,F,E,C)和(A,B)聚为一类(D,F,E,C,A,B)$\rightarrow$聚类结束  
  对应的共表矩阵是：  
  ![共表](/assets/images/201906DataMining/clustering_pic05.png)
3. 两个矩阵（对应生成两个向量？）的相关系数即为共表型相关系数  
  
<font color='#9a4238'>共表型相关系数：越大越好</font>


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