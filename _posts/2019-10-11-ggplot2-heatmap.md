---
published: true
title: R-ggplot2绘制热力图
category: Data Visualization
tags: 
  - ggplot2
layout: post
excerpt_separator: <!--more-->
---


# <font>数据读入与预处理</font>

## <font>读入通报数据data和地图数据mapWLD</font>

运行本段程序需要读入SPS_1995_2018.csv, dfreplace.csv  
将这两个文件放在同一个路径中，然后用setwd设置R的运行目录为该路径


```R
setwd("D:\\my\\own\\file")
library(dplyr) 
library(ggthemes)
```   
<!--more--> 


```R
data=read.csv("SPS_1995_2018.csv", header=TRUE) 
names(data)=c("symbol","country","type","date")
print(paste0(sum(is.na(data))," NA values found in data")) #检查数据是否有空值
head(data) #展示数据前6个观测
str(data) #展示数据结构（变量属性）
```

    [1] "0 NA values found in data"
    


<table>
<caption>A data.frame: 6 × 4</caption>
<thead>
	<tr><th scope="col">symbol</th><th scope="col">country</th><th scope="col">type</th><th scope="col">date</th></tr>
	<tr><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;fct&gt;</th></tr>
</thead>
<tbody>
	<tr><td>G/SPS/N/KOR/212/Add.12     </td><td>Korea, Republic of</td><td>Addenda regular</td><td>19/03/2019</td></tr>
	<tr><td>G/SPS/N/THA/155/Rev.2/Add.1</td><td>Thailand          </td><td>Addenda regular</td><td>19/03/2019</td></tr>
	<tr><td>G/SPS/N/THA/247/Add.1      </td><td>Thailand          </td><td>Addenda regular</td><td>19/03/2019</td></tr>
	<tr><td>G/SPS/N/THA/248/Add.2      </td><td>Thailand          </td><td>Addenda regular</td><td>19/03/2019</td></tr>
	<tr><td>G/SPS/N/THA/249/Add.1      </td><td>Thailand          </td><td>Addenda regular</td><td>19/03/2019</td></tr>
	<tr><td>G/SPS/N/THA/250/Add.1      </td><td>Thailand          </td><td>Addenda regular</td><td>19/03/2019</td></tr>
</tbody>
</table>



    'data.frame':	24050 obs. of  4 variables:
     $ symbol : Factor w/ 23812 levels "G/SPS/N/AFG/1",..: 12286 17797 17965 17968 17970 17973 21122 6547 6552 6554 ...
     $ country: Factor w/ 128 levels "Afghanistan",..: 64 112 112 112 112 112 122 24 24 24 ...
     $ type   : Factor w/ 8 levels "Addenda emergency",..: 2 2 2 2 2 2 2 2 2 2 ...
     $ date   : Factor w/ 4167 levels "01/02/2002","01/02/2007",..: 2531 2531 2531 2531 2531 2531 2531 1954 1954 1954 ...
    


```R
data=data[data$type=="Regular notification",]; #只分析regular notification对应的数据
data=data[,-3] #data$type这一列已经无用，可以删去
str(data) #再次查看数据结构
```

    'data.frame':	16136 obs. of  3 variables:
     $ symbol : Factor w/ 23812 levels "G/SPS/N/AFG/1",..: 6549 6550 6551 6553 6555 10388 10391 11959 11960 11961 ...
     $ country: Factor w/ 128 levels "Afghanistan",..: 24 24 24 24 24 40 40 60 60 60 ...
     $ date   : Factor w/ 4167 levels "01/02/2002","01/02/2007",..: 2075 2643 2643 2643 2643 2643 2643 2643 2643 2643 ...
    


```R
library(maps)
library(ggplot2)
mapWLD=map_data("world") #提取maps包中的世界地图数据，存储于mapWLD数据框中
    # note that map_data()这一函数是ggplot2包中的，所以要先library(ggplot2)
head(mapWLD) #展示mapWLD前6行
```


<table>
<caption>A data.frame: 6 × 6</caption>
<thead>
	<tr><th scope="col">long</th><th scope="col">lat</th><th scope="col">group</th><th scope="col">order</th><th scope="col">region</th><th scope="col">subregion</th></tr>
	<tr><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;int&gt;</th><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;chr&gt;</th></tr>
</thead>
<tbody>
	<tr><td>-69.89912</td><td>12.45200</td><td>1</td><td>1</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-69.89571</td><td>12.42300</td><td>1</td><td>2</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-69.94219</td><td>12.43853</td><td>1</td><td>3</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-70.00415</td><td>12.50049</td><td>1</td><td>4</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-70.06612</td><td>12.54697</td><td>1</td><td>5</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-70.05088</td><td>12.59707</td><td>1</td><td>6</td><td>Aruba</td><td>NA</td></tr>
</tbody>
</table>



注意事项   
>如果library(maps)失败，或者library(任何一个包)失败  
有可能是尚未在电脑中安装这个包  
install.packages("maps")即可解决问题

## <font>mapWLD与data中国家名称的匹配性检验</font>

接下来这一部分是在匹配  
>(1) 地图数据框mapWLD和  
(2) SPS通报数据框data  

中的国家名称


```R
# 查看mapWLD中的国家名称
countrylist=unique(mapWLD$region)
head(countrylist, n = 20)
# 将mapWLD中的国家名称保存到列表countrylist中，
    # 后面如果用到countrylist，请记住它是地图数据中的国家名称！
```


<ol class="list-inline">
	<li>'Aruba'</li>
	<li>'Afghanistan'</li>
	<li>'Angola'</li>
	<li>'Anguilla'</li>
	<li>'Albania'</li>
	<li>'Finland'</li>
	<li>'Andorra'</li>
	<li>'United Arab Emirates'</li>
	<li>'Argentina'</li>
	<li>'Armenia'</li>
	<li>'American Samoa'</li>
	<li>'Antarctica'</li>
	<li>'Australia'</li>
	<li>'French Southern and Antarctic Lands'</li>
	<li>'Antigua'</li>
	<li>'Barbuda'</li>
	<li>'Austria'</li>
	<li>'Azerbaijan'</li>
	<li>'Burundi'</li>
	<li>'Belgium'</li>
</ol>




```R
# 查看data中的国家名称
colnames(data)[2] = "country" # 将data的第二列改名为“country”
levels(data$country) %>% head(n = 20)
```


<ol class="list-inline">
	<li>'Afghanistan'</li>
	<li>'Albania'</li>
	<li>'Antigua and Barbuda'</li>
	<li>'Argentina'</li>
	<li>'Armenia'</li>
	<li>'Australia'</li>
	<li>'Austria'</li>
	<li>'Bahrain, Kingdom of'</li>
	<li>'Barbados'</li>
	<li>'Belgium'</li>
	<li>'Belize'</li>
	<li>'Benin'</li>
	<li>'Bolivia, Plurinational State of'</li>
	<li>'Botswana'</li>
	<li>'Brazil'</li>
	<li>'Brunei Darussalam'</li>
	<li>'Bulgaria'</li>
	<li>'Burkina Faso'</li>
	<li>'Burundi'</li>
	<li>'C?te d\'Ivoire'</li>
</ol>




```R
# 检查data$country中与mapWLD$region不匹配的项
df=data.frame(country=levels(data$country)) # 用data$country的水平（即取值）构建一个新的数据框df
data$country=as.character(data$country) # 将data$country从factor变为字符串，这一步跳过也可
df$match=(df$country %in% countrylist) 
  # 在df中新建一列match，若df$country 在 countrylist中，则match = 1, 否则 match = 0
df[!df$match,] # 查看df中match为0的行
```


<table>
<caption>A data.frame: 27 × 2</caption>
<thead>
	<tr><th></th><th scope="col">country</th><th scope="col">match</th></tr>
	<tr><th></th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;lgl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope="row">3</th><td>Antigua and Barbuda              </td><td>FALSE</td></tr>
	<tr><th scope="row">8</th><td>Bahrain, Kingdom of              </td><td>FALSE</td></tr>
	<tr><th scope="row">13</th><td>Bolivia, Plurinational State of  </td><td>FALSE</td></tr>
	<tr><th scope="row">16</th><td>Brunei Darussalam                </td><td>FALSE</td></tr>
	<tr><th scope="row">20</th><td>C?te d'Ivoire                    </td><td>FALSE</td></tr>
	<tr><th scope="row">21</th><td>Cabo Verde                       </td><td>FALSE</td></tr>
	<tr><th scope="row">26</th><td>Chinese Taipei                   </td><td>FALSE</td></tr>
	<tr><th scope="row">39</th><td>Eswatini                         </td><td>FALSE</td></tr>
	<tr><th scope="row">40</th><td>European Union                   </td><td>FALSE</td></tr>
	<tr><th scope="row">52</th><td>Hong Kong, China                 </td><td>FALSE</td></tr>
	<tr><th scope="row">64</th><td>Korea, Republic of               </td><td>FALSE</td></tr>
	<tr><th scope="row">65</th><td>Kuwait, the State of             </td><td>FALSE</td></tr>
	<tr><th scope="row">66</th><td>Kyrgyz Republic                  </td><td>FALSE</td></tr>
	<tr><th scope="row">67</th><td>Lao People's Democratic Republic </td><td>FALSE</td></tr>
	<tr><th scope="row">70</th><td>Macao, China                     </td><td>FALSE</td></tr>
	<tr><th scope="row">77</th><td>Moldova, Republic of             </td><td>FALSE</td></tr>
	<tr><th scope="row">87</th><td>North Macedonia                  </td><td>FALSE</td></tr>
	<tr><th scope="row">98</th><td>Russian Federation               </td><td>FALSE</td></tr>
	<tr><th scope="row">100</th><td>Saint Vincent and the Grenadines </td><td>FALSE</td></tr>
	<tr><th scope="row">101</th><td>Saudi Arabia, Kingdom of         </td><td>FALSE</td></tr>
	<tr><th scope="row">105</th><td>Slovak Republic                  </td><td>FALSE</td></tr>
	<tr><th scope="row">113</th><td>The Gambia                       </td><td>FALSE</td></tr>
	<tr><th scope="row">115</th><td>Trinidad and Tobago              </td><td>FALSE</td></tr>
	<tr><th scope="row">121</th><td>United Kingdom                   </td><td>FALSE</td></tr>
	<tr><th scope="row">122</th><td>United States of America         </td><td>FALSE</td></tr>
	<tr><th scope="row">124</th><td>Venezuela, Bolivarian Republic of</td><td>FALSE</td></tr>
	<tr><th scope="row">125</th><td>Viet Nam                         </td><td>FALSE</td></tr>
</tbody>
</table>



我已经事先将需要替换的数据整理到dfreplace中，  
要注意这一步是纯手工操作，没有捷径可走。


```R
# 读入dfreplace
dfreplace=read.csv("dfreplace.csv",header=TRUE)[,-1]
dfreplace$before=as.character(dfreplace$before);
dfreplace$after=as.character(dfreplace$after);
# 查看dfreplace的头几行
head(dfreplace)
```


<table>
<caption>A data.frame: 6 × 2</caption>
<thead>
	<tr><th scope="col">before</th><th scope="col">after</th></tr>
	<tr><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;chr&gt;</th></tr>
</thead>
<tbody>
	<tr><td>United States of America</td><td>USA         </td></tr>
	<tr><td>Korea, Republic of      </td><td>South Korea </td></tr>
	<tr><td>Saudi Arabia, Kingdom of</td><td>Saudi Arabia</td></tr>
	<tr><td>Bahrain, Kingdom of     </td><td>Bahrain     </td></tr>
	<tr><td>Kuwait, the State of    </td><td>Kuwait      </td></tr>
	<tr><td>Russian Federation      </td><td>Russia      </td></tr>
</tbody>
</table>



这一步将data与dfreplace合并（按照字段data\\$country和dfreplace\\$before）  
可以对比stata中的merge来理解  
>若data\\$country在dfreplace\\$before中，则合并后的data\\$after不为空值，  
若data\\$country不在dfreplace\\$before中，则data\\$after为空值  
换句话说，data\\$after不为空的行，就是data和mapWLD中国家名称不匹配的行，是需要进行名称替换的行


```R
head(data) # merge之前
data = merge(data, dfreplace, by.x = "country", by.y = "before", all.x = TRUE)
head(data) # merge之后
loc = which(!is.na(data$after)) # loc找出所有data$after不为空的行序号，也就是需要替换国家名称的行
data[loc,]$country = data[loc,]$after #对国家名称进行替换
#此时data$after这一行不再有用，可以删去
data = data[,-which(colnames(data) == "after")]
head(data)
```


<table>
<caption>A data.frame: 6 × 3</caption>
<thead>
	<tr><th></th><th scope="col">symbol</th><th scope="col">country</th><th scope="col">date</th></tr>
	<tr><th></th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;fct&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope="row">60</th><td>G/SPS/N/CHL/585/Rev.1</td><td>Chile         </td><td>16/01/2019</td></tr>
	<tr><th scope="row">75</th><td>G/SPS/N/CHL/586      </td><td>Chile         </td><td>19/12/2018</td></tr>
	<tr><th scope="row">76</th><td>G/SPS/N/CHL/587      </td><td>Chile         </td><td>19/12/2018</td></tr>
	<tr><th scope="row">77</th><td>G/SPS/N/CHL/588      </td><td>Chile         </td><td>19/12/2018</td></tr>
	<tr><th scope="row">78</th><td>G/SPS/N/CHL/589      </td><td>Chile         </td><td>19/12/2018</td></tr>
	<tr><th scope="row">79</th><td>G/SPS/N/EU/289       </td><td>European Union</td><td>19/12/2018</td></tr>
</tbody>
</table>




<table>
<caption>A data.frame: 6 × 4</caption>
<thead>
	<tr><th scope="col">country</th><th scope="col">symbol</th><th scope="col">date</th><th scope="col">after</th></tr>
	<tr><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;chr&gt;</th></tr>
</thead>
<tbody>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/1  </td><td>08/08/2016</td><td>NA</td></tr>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/2  </td><td>08/08/2016</td><td>NA</td></tr>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/3  </td><td>08/08/2016</td><td>NA</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/182</td><td>20/02/2015</td><td>NA</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/160</td><td>12/04/2013</td><td>NA</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/189</td><td>09/06/2016</td><td>NA</td></tr>
</tbody>
</table>




<table>
<caption>A data.frame: 6 × 3</caption>
<thead>
	<tr><th scope="col">country</th><th scope="col">symbol</th><th scope="col">date</th></tr>
	<tr><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;fct&gt;</th></tr>
</thead>
<tbody>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/1  </td><td>08/08/2016</td></tr>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/2  </td><td>08/08/2016</td></tr>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/3  </td><td>08/08/2016</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/182</td><td>20/02/2015</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/160</td><td>12/04/2013</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/189</td><td>09/06/2016</td></tr>
</tbody>
</table>




```R
# 除了用上面的merge方式进行替换，也可以用下面的for循环
# 但是循环效率较低，一般尽可能避免用循环语句
if (FALSE) { # if(FALSE) {...} 括号中的内容将不被执行，可当作多行注释使用
    for (i in 1:nrow(dfreplace)){
        data$country[data$country==dfreplace[i,1]]=dfreplace[i,2]
    }
    data$country=as.factor(data$country)
}
#再检查一次未匹配的国家名
df=data.frame(country=unique(data$country))
df$match=(df$country %in% countrylist)
df[!df$match,]
```


<table>
<caption>A data.frame: 7 × 2</caption>
<thead>
	<tr><th></th><th scope="col">country</th><th scope="col">match</th></tr>
	<tr><th></th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;lgl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope="row">3</th><td>Antigua and Barbuda             </td><td>FALSE</td></tr>
	<tr><th scope="row">17</th><td>C?te d'Ivoire                   </td><td>FALSE</td></tr>
	<tr><th scope="row">36</th><td>Eswatini                        </td><td>FALSE</td></tr>
	<tr><th scope="row">37</th><td>European Union                  </td><td>FALSE</td></tr>
	<tr><th scope="row">49</th><td>Hong Kong, China                </td><td>FALSE</td></tr>
	<tr><th scope="row">67</th><td>Macao, China                    </td><td>FALSE</td></tr>
	<tr><th scope="row">96</th><td>Saint Vincent and the Grenadines</td><td>FALSE</td></tr>
</tbody>
</table>



只剩下一些地图数据中没有的国家，在此不做处理。

## <font>绘图最终准备：将notification数据加入到mapWLD中</font>  
由于ggplot函数要用地图数据框mapWLD绘制地图  
我们只需计算出各国发出的总通报数  
将通报数作为新的一列加入到mapWLD中即可  
这一步同样用merge进行

a) 计算各国发出的总通报数


```R
data$count = 1
t = tapply(data$count, data$country, sum)
head(t, n=10)
df = data.frame(country = names(t), count = as.vector(t))
head(df, n=10)
```


<dl class="dl-horizontal">
	<dt>Afghanistan</dt>
		<dd>3</dd>
	<dt>Albania</dt>
		<dd>27</dd>
	<dt>Antigua and Barbuda</dt>
		<dd>4</dd>
	<dt>Argentina</dt>
		<dd>203</dd>
	<dt>Armenia</dt>
		<dd>26</dd>
	<dt>Australia</dt>
		<dd>430</dd>
	<dt>Bahrain</dt>
		<dd>191</dd>
	<dt>Belgium</dt>
		<dd>10</dd>
	<dt>Belize</dt>
		<dd>9</dd>
	<dt>Benin</dt>
		<dd>5</dd>
</dl>




<table>
<caption>A data.frame: 10 × 2</caption>
<thead>
	<tr><th scope="col">country</th><th scope="col">count</th></tr>
	<tr><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>Afghanistan        </td><td>  3</td></tr>
	<tr><td>Albania            </td><td> 27</td></tr>
	<tr><td>Antigua and Barbuda</td><td>  4</td></tr>
	<tr><td>Argentina          </td><td>203</td></tr>
	<tr><td>Armenia            </td><td> 26</td></tr>
	<tr><td>Australia          </td><td>430</td></tr>
	<tr><td>Bahrain            </td><td>191</td></tr>
	<tr><td>Belgium            </td><td> 10</td></tr>
	<tr><td>Belize             </td><td>  9</td></tr>
	<tr><td>Benin              </td><td>  5</td></tr>
</tbody>
</table>



b) 合并mapWLD数据和上面计算出的通报数据（df）


```R
head(mapWLD, n=3) # 合并前
mapWLD=merge(mapWLD, df, by.x="region",by.y="country", all.x=TRUE)
head(mapWLD, n=3) # 合并后，可以看到order这一列顺序乱了
mapWLD=mapWLD[order(mapWLD$order),] # 将mapWLD再按照order由小到大的顺序排列好
```


<table>
<caption>A data.frame: 3 × 6</caption>
<thead>
	<tr><th scope="col">long</th><th scope="col">lat</th><th scope="col">group</th><th scope="col">order</th><th scope="col">region</th><th scope="col">subregion</th></tr>
	<tr><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;int&gt;</th><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;chr&gt;</th></tr>
</thead>
<tbody>
	<tr><td>-69.89912</td><td>12.45200</td><td>1</td><td>1</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-69.89571</td><td>12.42300</td><td>1</td><td>2</td><td>Aruba</td><td>NA</td></tr>
	<tr><td>-69.94219</td><td>12.43853</td><td>1</td><td>3</td><td>Aruba</td><td>NA</td></tr>
</tbody>
</table>




<table>
<caption>A data.frame: 3 × 7</caption>
<thead>
	<tr><th scope="col">region</th><th scope="col">long</th><th scope="col">lat</th><th scope="col">group</th><th scope="col">order</th><th scope="col">subregion</th><th scope="col">count</th></tr>
	<tr><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;int&gt;</th><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>Afghanistan</td><td>73.90781</td><td>36.85293</td><td>2</td><td> 28</td><td>NA</td><td>3</td></tr>
	<tr><td>Afghanistan</td><td>66.34687</td><td>30.80278</td><td>2</td><td>147</td><td>NA</td><td>3</td></tr>
	<tr><td>Afghanistan</td><td>73.11680</td><td>36.86856</td><td>2</td><td> 32</td><td>NA</td><td>3</td></tr>
</tbody>
</table>



# <font>绘图！</font>    
准备工作终于全部完成！  

## <font>初步绘制</font>

其实只要有了mapWLD就可以绘制出世界地图，代码非常，非常简单！


```R
options(repr.plot.width=7,repr.plot.height=3.5) # 这一句只是在设置长宽比，不设置也可以
# win.graph(width=7, height=3.5)
    # 要注意，在R中设置长宽比的语句是win.graph(width=7, height=3.5)，而不是上面这一句options()
    # options只是在jupyter notebook中好用
ggplot()+
  geom_polygon(data = mapWLD, aes(x = long, y = lat, group = group))
```


![png](/assets/images/20191011ggplot2/output_28_0.png)


回顾一下上面的工作，  
其实我们只是想在最基础的地图上加入可以决定颜色的变量，也就是SPS notification数据  
换言之，我们想将各国总通报数给加到mapWLD里去  
之所以大费周章，是因为两个数据框中的国家名称并不完全统一  
需要先确认尽可能多的国家名称能对上


```R
# 这里和上面的主要区别仅在于加入了fill = count这一项，
# 并通过scale_fill_gradient设置了要填充的颜色，其余都是可有可无的微调
pic0 = ggplot()+
  geom_polygon(data = mapWLD, 
    aes(x = long, y = lat, group = group, fill = count), 
    color = "grey60", size = 0.05)+ # 调整了地图边线的颜色（浅灰）和粗细（0.05)，使其更美观
  scale_fill_gradient(name="Count of\nnotifications",
    low="#FFF5F0",high="#A50F15", na.value = "#F2F2F2")+ 
     # scale_fill_gradient()设置了渐变填充颜色的两个端点值，以及当count为NA时的值
  ylim(c(-57,83)) # 将南极北极去掉，因为这两个区域没有带来更多的信息
pic0
```


![png](/assets/images/20191011ggplot2/output_30_0.png)


## <font>细节调整</font>

最后可以微调更多细节使图片更好看  
我觉得这也是ggplot绘图的优势所在  
并不是说折线图/散点图/柱状图/热力图别的软件绘制不了  
而是每一个部分都可以很灵活地调整  
<br />  
大家可以将下面的每个语句删掉再加上，调整设置的具体数值，感受一下它们起到的作用  
想查看具体可以做出哪些设置，可以运行下面这一句：  
?ggplot::theme


```R
# 这一部分是为了在data中新建出year这一变量，
# 从而在设置下面第二张图的标题时，可以用上min(year)和max(year)的信息
data$date = as.character(data$date)
data$year = substr(data$date, 7, 10)
head(data)
```


<table>
<caption>A data.frame: 6 × 5</caption>
<thead>
	<tr><th scope="col">country</th><th scope="col">symbol</th><th scope="col">date</th><th scope="col">count</th><th scope="col">year</th></tr>
	<tr><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;fct&gt;</th><th scope="col">&lt;chr&gt;</th><th scope="col">&lt;dbl&gt;</th><th scope="col">&lt;chr&gt;</th></tr>
</thead>
<tbody>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/1  </td><td>08/08/2016</td><td>1</td><td>2016</td></tr>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/2  </td><td>08/08/2016</td><td>1</td><td>2016</td></tr>
	<tr><td>Afghanistan</td><td>G/SPS/N/AFG/3  </td><td>08/08/2016</td><td>1</td><td>2016</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/182</td><td>20/02/2015</td><td>1</td><td>2015</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/160</td><td>12/04/2013</td><td>1</td><td>2013</td></tr>
	<tr><td>Albania    </td><td>G/SPS/N/ALB/189</td><td>09/06/2016</td><td>1</td><td>2016</td></tr>
</tbody>
</table>



如果下面绘图报错，可以注释掉（暂时删去）某些语句，看看是哪一句有问题  
比如我的电脑上有family = "serif"这一字体，可能大家的电脑上没有，就会报错


```R
pic1 = pic0 + 
  theme_bw()+
  ylab("")+
  xlab("")+
  ggtitle( paste0( "SPS Notification by Country, ", min(data$year), ' - ', max(data$year) ) )+
  theme(panel.border = element_blank(), 
        panel.grid = element_blank(), 
        plot.title = element_text(size = 12, family = "serif", face = "bold", hjust = 0.5), 
        legend.title = element_text(size = 9, family = "serif", face = "bold"), 
        legend.text = element_text(size = 8, family = "serif", face = "bold"),
        axis.text = element_blank(), 
        axis.ticks = element_blank() )
pic1
```


![png](/assets/images/20191011ggplot2/output_35_0.png)


## <font>保存图片</font>


```R
?ggsave
```


```R
ggsave(filename = 'C:\\my\\own\\file\\map01.png', plot = pic1, width = 14, height = 7,
      units = "cm", dpi = 300)
# filename中的保存路径一定要换成想保存的路径，至少是自己电脑上有的路径！
# width, height, units, dpi这些参数可以随意调整，具体用法见?ggsave
```
