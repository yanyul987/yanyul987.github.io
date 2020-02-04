---
published: true
title: 数据挖掘期末复习汇总（三）——关联规则
category: Data Mining
tags: 
  - DataMining
  - AssociationRules
layout: post

---

# 关联规则  
Association Rules  
<font color='#9a4238'>掌握support confidence lift等指标</font>

<!--more-->

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
    


<ol class="list-inline">
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


<dl class="dl-horizontal">
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


<dl class="dl-horizontal">
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


<dl class="dl-horizontal">
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


![png](/assets/images/201906DataMining/output_104_0.png)



```R
#绘图：按排序查看
itemFrequencyPlot(data3, topN=10,col = "blue",
  xlab = paste("Proportion of Market Baskets Containing Item",  
  "\n(Item Relative Frequency or Support)"))
```


![png](/assets/images/201906DataMining/output_105_0.png)


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
<thead><tr><th scope="col">rules</th><th scope="col">support</th><th scope="col">confidence</th><th scope="col">lift</th><th scope="col">count</th></tr></thead>
<tbody>
	<tr><td><span style="white-space:pre-wrap">{Lip Liner} =&gt; {Lipstick}                  </span></td><td>0.01210826                                                                            </td><td>0.34000000                                                                            </td><td>2.824615                                                                              </td><td>17                                                                                    </td></tr>
	<tr><td><span style="white-space:pre-wrap">{Lipstick} =&gt; {Lip Liner}                  </span></td><td>0.01210826                                                                            </td><td>0.10059172                                                                            </td><td>2.824615                                                                              </td><td>17                                                                                    </td></tr>
	<tr><td><span style="white-space:pre-wrap">{Makeup} =&gt; {Foundation}                   </span></td><td>0.01139601                                                                            </td><td>0.34042553                                                                            </td><td>2.844985                                                                              </td><td>16                                                                                    </td></tr>
	<tr><td><span style="white-space:pre-wrap">{Foundation} =&gt; {Makeup}                   </span></td><td>0.01139601                                                                            </td><td>0.09523810                                                                            </td><td>2.844985                                                                              </td><td>16                                                                                    </td></tr>
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