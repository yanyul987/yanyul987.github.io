---
published: true
title: R-ggplot2绘制柱状图
category: Data Visualization
tags: 
  - ggplot2
layout: post
excerpt_separator: <!--more-->
---



# 创建数据


```R
library(ggplot2)
data=data.frame(cut=c("Fair","Good","Very Good","Premium","Ideal"),
    price=c(4300,3800,3950,4700,3500));data
```


<table>
<caption>A data.frame: 5 × 2</caption>
<thead>
	<tr><th scope="col">cut</th><th scope="col">price</th></tr>
	<tr><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>Fair     </td><td>4300</td></tr>
	<tr><td>Good     </td><td>3800</td></tr>
	<tr><td>Very Good</td><td>3950</td></tr>
	<tr><td>Premium  </td><td>4700</td></tr>
	<tr><td>Ideal    </td><td>3500</td></tr>
</tbody>
</table>



<!--more-->
# 绘制柱状图


```R
options(repr.plot.width=4,repr.plot.height=3)
#options()语句只是为了调整Jupyter Notebook中的图片大小，在R中运行不需要设定options

p1=ggplot(data,aes(cut,price))+
    geom_bar(stat="identity",width=.5,color="black",size=0.25,alpha=.7)
    #color:描边颜色，size:描边宽度,alpha[0,1]:填充透明度
p1
```


![png](/assets/images/20190303ggplot2/output_6_0.png)


# 添加数值标签


```R
options(repr.plot.width=4,repr.plot.height=3.5)
p2=p1+geom_text(aes(label=price),size=3,vjust=-.5)
    #geom_text：添加标签；vjust为正-下移，负-上移；hjust为正-左移，负-右移
p2
```


![png](/assets/images/20190303ggplot2/output_8_0.png)


# 旋转横坐标标签、变更横纵坐标名称、设置标题


```R
#theme(axis.text.x=element_text()):设置横坐标标度格式; 
#theme(axis.title.x=element_text()):设置横坐标标签格式;
#theme(axis.title.y=element_text()):设置纵坐标标签格式
#theme(plot.title=element_text()):设置图片标题格式

options(repr.plot.width=4,repr.plot.height=4)
p3=p2+theme(axis.text.x=element_text(angle=90,hjust=1,vjust=0.5,size=9))+    #angle=90: 逆时针旋转90°; size=9:设置字号为9
    xlab("切割工艺")+ylab("价格")+     #xlab,ylab: 更改横纵坐标名称
    theme(axis.title.x=element_text(size=12),
        axis.title.y=element_text(size=12))+    #设置横纵坐标标签字号
    ggtitle("!!!TITLE!!!")+
    theme(plot.title=element_text(hjust=.5,size=12))    #标题位置及其字号，hjust=0:居左，hjust=.5:居中，hjust=1:居右
p3
```


![png](/assets/images/20190303ggplot2/output_10_0.png)


# 标签自动换行
面对过长的标签，一种办法是旋转90°，另一种方法是设置标签达到一定长度后自动换行  
对于英文同样适用


```R
library(stringr) #使用str_wrap()函数需要加载stringr包

options(repr.plot.width=4,repr.plot.height=2.6)
p4=p1+scale_x_discrete(labels=function(x) str_wrap(x,width=5))
p4
```


![png](/assets/images/20190303ggplot2/output_12_0.png)


# 填充颜色


```R
options(repr.plot.width=5,repr.plot.height=2.6)
p5=ggplot(data,aes(cut,price,fill=price))+   #用price变量的值来设定颜色
    geom_bar(stat="identity",width=.5)+
    scale_fill_distiller(palette="Spectral")
    #"Spectral"是一个色板，可以尝试"Accent","Greens"等不同palette
p5
```


![png](/assets/images/20190303ggplot2/output_14_0.png)


price是连续型变量，故用scale_fill_distiller()设置色板  
也可用离散型变量来上色，例如cut，但要用scale_fill_brewer()设置色板  


```R
options(repr.plot.width=5,repr.plot.height=2.6)
p6=ggplot(data,aes(cut,price,fill=cut))+   #用price变量的值来设定颜色
    geom_bar(stat="identity",width=.5)+
    scale_fill_brewer(palette="Spectral")
p6
```


![png](/assets/images/20190303ggplot2/output_16_0.png)


去掉图例：在geom_bar中设置参数show.legend=FALSE


```R
options(repr.plot.width=3.8,repr.plot.height=2.6)
p7=ggplot(data,aes(cut,price))+
    geom_bar(stat="identity",width=.5,show.legend=FALSE)+
    aes(fill=price)+
    scale_fill_distiller(palette="Spectral")
p7
```


![png](/assets/images/20190303ggplot2/output_18_0.png)


**<font size="3">色板一览</font>**


```R
#查看所有的色板：RColorBrewer::display.brewer.all()
options(repr.plot.width=5,repr.plot.height=7)
RColorBrewer::display.brewer.all()
```


![png](/assets/images/20190303ggplot2/output_20_0.png)



```R
# 展示色板
getplot=function(palette_name){
    ggplot(data,aes(cut,price,fill=price))+
    geom_bar(stat="identity",width=.5,show.legend=FALSE)+
    scale_fill_distiller(palette=palette_name)+
    ggtitle(paste(palette_name))+
    theme(plot.title=element_text(hjust=.5,size=11))
}

options(repr.plot.width=8,repr.plot.height=6)

p1=getplot("Spectral")
p2=getplot("Accent")
p3=getplot("Purples")
p4=getplot("Greens")

library(Rmisc)
library(plyr)
multiplot(p1,p2,p3,p4,cols=2)
```


![png](/assets/images/20190303ggplot2/output_21_0.png)


# 主题设置


```R
#设置ggplot主题：
library(ggthemes)
options(repr.plot.width=5,repr.plot.height=2.6)
ggplot(data,aes(cut,price,fill=price))+
    theme_bw()+
    geom_bar(stat="identity",width=.5,color="black",size=0.25,alpha=.55,show.legend=FALSE)+
    scale_fill_distiller(palette="Spectral")
```


![png](/assets/images/20190303ggplot2/output_23_0.png)


theme_bw()是一个主题，可以尝试其他主题  
如theme_economist、theme_fivethirtyeight、theme_wsj


```R
#展示主题
library(ggthemes)
library(stringr)
getplot=function(themename){
    eval(parse(text=paste0("base=ggplot(data)+", themename, "()")))
    out=base+aes(cut,price,fill=price)+
        geom_bar(stat="identity",width=.5,color="black",size=0.25,alpha=.55,show.legend=FALSE)+
        xlab("")+ylab("")+
        scale_fill_distiller(palette="Spectral")+
        scale_x_discrete(labels=function(x) str_wrap(x,width=5))+
        ggtitle(themename)+
        theme(plot.title=element_text(hjust=.5,size=13), axis.text.x=element_text(size=10))
}                     
options(repr.plot.width=8,repr.plot.height=6)
p1=getplot("theme_bw")
p2=getplot("theme_economist")
p3=getplot("theme_fivethirtyeight")
p4=getplot("theme_wsj")

library(Rmisc)
library(plyr)
multiplot(p1,p2,p3,p4,cols=2)
```

    Warning message:
    "package 'Rmisc' was built under R version 3.5.2"Loading required package: lattice
    


![png](/assets/images/20190303ggplot2/output_25_1.png)


# 排序


```R
options(repr.plot.width=5,repr.plot.height=2.6)

p1=ggplot(data,aes(reorder(cut,price),price,fill=price))+
    theme_bw()+
    geom_bar(stat="identity",width=.5,color="black",size=0.25,alpha=.55,show.legend=FALSE)+
    scale_fill_distiller(palette="Spectral")
p1  #reorder(cut,price):将cut按照与之对应的price的值从小到大排序
    #若想从大到小：reorder(cut,-price)

p2=ggplot(data,aes(reorder(cut,-price),price,fill=price))+
    theme_bw()+
    geom_bar(stat="identity",width=.5,color="black",size=0.25,alpha=.55,show.legend=FALSE)+
    scale_fill_distiller(palette="Spectral")
p2 
    #由于fill=price，颜色是按照price的大小排序的，所以对cut进行重排不会改变每个cut水平对应的颜色
    #例如cut=Premium对应的price值是最大的，所以对应的始终是红色
```


![png](/assets/images/20190303ggplot2/output_27_0.png)



![png](/assets/images/20190303ggplot2/output_27_1.png)

