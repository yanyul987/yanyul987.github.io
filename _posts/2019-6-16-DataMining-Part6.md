---
published: true
title: 数据挖掘期末复习汇总（六）——神经网络
category: Data Mining
tags: 
  - DataMining
  - NeuralNetwork
layout: post
excerpt_separator: <!--more-->

---


# 神经网络  
NNET is essentially about minimizing cost function  
<font color='#9a4238'>复习要点：BP算法，<b>优化机制，PPT中的文字/思想部分</b> </font>

go to https://blog.csdn.net/illikang/article/details/82019945  
https://www.youtube.com/watch?v=aircAruvnKk  
自己写神经网络代码，参考 https://blog.csdn.net/kMD8d5R/article/details/85086412 【不完整】

<!--more-->

the simplest nnet mode - https://www.youtube.com/watch?v=aircAruvnKk  
example used through out: hand-written digits recognition  
28*28 pixel grid  
   
>some basic ideas to keep in mind:  
1. 设计一个神经网络时，输入层与输出层的节点数往往是固定的，中间层则可以自由指定；
2. 神经网络结构图中的拓扑与箭头代表着预测过程时数据的流向，跟训练时的数据流有一定的区别；
3. 结构图里的关键不是圆圈（代表“神经元”），而是连接线（代表“神经元”之间的连接）。每个连接线对应一个不同的权重（其值称为权值），这是需要训练得到的。一个神经网络的训练算法就是**让权重的值调整到最佳**，以使得整个网络的预测效果最好。  
4. 通常我们说$k$层神经网络，是说有多少个连接层而非节点层。  

![basic](/assets/images/201906DataMining/nnet_pic00.jpg){:width="40%"}

## The HISTORY of NNET  
### MP模型/神经元模型  
（因提出者为心理学家McCulloch和数学家Pitts而得名，参考神经元结构，也叫神经元模型）  
 MP模型中，权重的值都是预先设置的，因此不能学习。  
 * 同时期还有Hebb模型，提出了调整权重以使机器学习，为学习算法奠定基础。

### 单层神经网络（单连接层，两层神经元，感知器模型）
 * 首个可以学习的人工神经网络  
 ![单层](/assets/images/201906DataMining/nnet_pic02.jpg){:width="500"}  
 * 从前一层到后一层的计算可以用矩阵表示：$g(W \cdot a) = z$  
 * 与神经元模型不同，感知器中的权值是通过训练得到的。感知器类似一个逻辑回归模型，可以做线性分类任务。决策分界就是在二维的数据平面中划出一条直线，当数据的维度是3维的时候，就是划出一个平面，当数据的维度是n维时，就是划出一个n-1维的超平面。  
 * 感知器模型只能做线性分类任务，对XOR（异或）这样的简单分类任务都无法解决。

### 两层神经网络（多层感知器）  
 * 1986年，Rumelhar和Hinton等人提出了反向传播（Backpropagation，BP）算法，解决了两层神经网络所需要的复杂计算量问题。【BP算法是两层神经网络的计算思路】  
![两层1](/assets/images/201906DataMining/nnet_pic03.jpg){:width="500"}   
![两层2](/assets/images/201906DataMining/nnet_pic04.jpg){:width="500"}  
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

![connections](/assets/images/201906DataMining/nnet_pic01.jpg){:width="500"}  
我们使用a来表示输入，用w来表示权值。一个表示连接的有向箭头可以这样理解：在初端，传递的信号大小仍然是a，端中间有加权参数w，经过这个加权后的信号会变成a$\times$w，因此在连接的末端，信号的大小就变成了a$\times$w。  
z在输入和权值的线性加权和（$\sum(a\cdot w)$）叠加了一个激活函数$g()$的值。在MP模型里，激活函数$g()$是$sgn$函数，更常用的是$sigmoid$和$ReLU$等  

## How to achieve that through an algorithm?
we start all those unknown parameters i.e. weights & bias **randomly**  
so the initial performance would be terrible  
  
>COST FUNCTION  (input: all unknown para including weights and bias, output: how bad this model is)  
  损失函数$E(w)=1/2 (T_i-o_i )^2$：  
  其中 $T$（Target,目标值即真实值） ，$O$（Output,模型输出）  
 * the function value will be small if output is correct  
 * would be large if output is trash  
 * average cost(over all sample data) captures how lousy the network is for the full panel of sample  

So the prob would then be how to minimize the cost function - 如何优化参数，能够让损失函数的值最小。  
One possible solution - > **gradient descent 梯度下降**  
  
* 导数tell us which changes in parameters ( or changing which para) matter most  

## Interesting stuffs (from the landscape literature)
using structured data to train a network allows one to achieve local minima much more quickly(quick drop in accuracy)  
using a randomly labeled data on the otherhand does not  


## BP神经网络

<font size="3">* 特点：两层激活，从后向前（先C-B再B-A）  
![BP](/assets/images/201906DataMining/nnet_pic05.png)    
  
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
<thead><tr><th></th><th scope="col">Sepal.Length</th><th scope="col">Sepal.Width</th><th scope="col">Petal.Length</th><th scope="col">Petal.Width</th><th scope="col">Species</th></tr></thead>
<tbody>
	<tr><th scope="row">40</th><td>5.1       </td><td>3.4       </td><td>1.5       </td><td>0.2       </td><td>setosa    </td></tr>
	<tr><th scope="row">42</th><td>4.5       </td><td>2.3       </td><td>1.3       </td><td>0.3       </td><td>setosa    </td></tr>
	<tr><th scope="row">62</th><td>5.9       </td><td>3.0       </td><td>4.2       </td><td>1.5       </td><td>versicolor</td></tr>
	<tr><th scope="row">13</th><td>4.8       </td><td>3.0       </td><td>1.4       </td><td>0.1       </td><td>setosa    </td></tr>
	<tr><th scope="row">56</th><td>5.7       </td><td>2.8       </td><td>4.5       </td><td>1.3       </td><td>versicolor</td></tr>
	<tr><th scope="row">80</th><td>5.7       </td><td>2.6       </td><td>3.5       </td><td>1.0       </td><td>versicolor</td></tr>
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


![png](/assets/images/201906DataMining/output_193_0.png)


设置更多参数  
见https://www.r-bloggers.com/visualizing-neural-networks-from-the-nnet-package/  
![table](/assets/images/201906DataMining/nnet_plot_pic01.png)

## 卷积神经网络

--To be continued--
