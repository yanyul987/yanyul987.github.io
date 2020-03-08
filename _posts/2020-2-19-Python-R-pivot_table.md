---
published: true
title: Python & R数据分析函数对照
category: Python
tags: 
  - python
layout: post
excerpt_separator: <!--more-->
---

记录Python和R中一些功能接近的函数

习惯了用R来处理数据和画图，最近开始用Python做数据分析，总是在寻找R中的table(),tapply()等函数的替代品。因此记录一下Python和R数据分析方面功能接近的函数。

<!--more-->

## tapply的python替代

以上篇文章中的豆瓣综艺数据为例


```python
import pandas as pd
import numpy as np

# ...import data... 
# 省略数据读入部分

df[["name", "country", "director", "year", "actor", "num_actors", "episodes"]].head()
```

<div style="overflow-x:auto;">

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>country</th>
      <th>director</th>
      <th>year</th>
      <th>actor</th>
      <th>num_actors</th>
      <th>episodes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>姜食堂 第二季 강식당  시즌2</td>
      <td>韩国</td>
      <td>罗英石</td>
      <td>2019</td>
      <td>姜虎东 / 李寿根 / 殷志源 / 安宰贤 / 宋旻浩 / 表志勋</td>
      <td>6.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>姜食堂 第三季 강식당 시즌3</td>
      <td>韩国</td>
      <td>罗英石</td>
      <td>2019</td>
      <td>姜虎东 / 李寿根 / 殷志源 / 安宰贤 / 曹圭贤 / 宋旻浩 / 表志勋</td>
      <td>7.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>非正式会谈 第三季</td>
      <td>中国大陆</td>
      <td>NaN</td>
      <td>2016</td>
      <td>大左 / 杨迪 / 贝乐泰 / 功必扬 / 华波波 / 浅井悠佑 / 田原皓 / 陈铭 / 陈超</td>
      <td>9.0</td>
      <td>80.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>妻子的浪漫旅行 第三季</td>
      <td>中国大陆</td>
      <td>蔡欢</td>
      <td>2019</td>
      <td>谢娜 / 李娜 / 杨千嬅 / 霍思燕 / 唐一菲 / 魏大勋 / 丁子高 / 杜江 / 凌...</td>
      <td>10.0</td>
      <td>99.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>大逃脱 第二季 대탈출 시즌2</td>
      <td>韩国</td>
      <td>郑中渊</td>
      <td>2019</td>
      <td>姜虎东 / 金钟民 / 金东炫 / 申东熙 / 柳炳宰 / 表志勋</td>
      <td>6.0</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>


```python
# 计算各国制作的节目数量  
# %R: table(df$country)
df.groupby("country").agg({"name": "count"})
df.pivot_table(index="country", values = "name", aggfunc="count")
# pd.pivot_table(df, index="country", values="name", aggfunc="count") # try them, you'll get the same output
```



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>中国台湾</th>
      <td>1</td>
    </tr>
    <tr>
      <th>中国大陆</th>
      <td>143</td>
    </tr>
    <tr>
      <th>中国香港</th>
      <td>1</td>
    </tr>
    <tr>
      <th>日本</th>
      <td>1</td>
    </tr>
    <tr>
      <th>美国</th>
      <td>3</td>
    </tr>
    <tr>
      <th>韩国</th>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>



注意groupby().agg()和pivot_table()返回的，并不是常见的两列数据框，一列为country一列为name  
其本质上是一列而非两列数据


```python
dt = df.groupby("country").agg({"name": "count"})
dt.shape
list(dt)
```




    (6, 1)

    ['name']



将dt转为数据框：reset_index()  
ref: https://blog.csdn.net/weixin_43474731/article/details/100186154


```python
dt2 = dt.reset_index(); dt2
dt2.shape
dt3 = df.pivot_table(index="country", values = "name", aggfunc="count").reset_index(); dt3
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>中国台湾</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>中国大陆</td>
      <td>143</td>
    </tr>
    <tr>
      <th>2</th>
      <td>中国香港</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>日本</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>美国</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>韩国</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>






    (6, 2)






<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>中国台湾</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>中国大陆</td>
      <td>143</td>
    </tr>
    <tr>
      <th>2</th>
      <td>中国香港</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>日本</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>美国</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>韩国</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 在此基础上，按数量从大到小排序  
# %R: table(df$country) %>% sort(decreasing=TRUE)
dt2.sort_values("name", ascending=False)
dt3.sort_values("name", ascending=False)
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>中国大陆</td>
      <td>143</td>
    </tr>
    <tr>
      <th>5</th>
      <td>韩国</td>
      <td>31</td>
    </tr>
    <tr>
      <th>4</th>
      <td>美国</td>
      <td>3</td>
    </tr>
    <tr>
      <th>0</th>
      <td>中国台湾</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>中国香港</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>日本</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 更复杂的玩法
df.pivot_table(index="country", values = ["episodes", "num_actors"], aggfunc = [np.mean, np.max])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">mean</th>
      <th colspan="2" halign="left">amax</th>
    </tr>
    <tr>
      <th></th>
      <th>episodes</th>
      <th>num_actors</th>
      <th>episodes</th>
      <th>num_actors</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>中国台湾</th>
      <td>45.000000</td>
      <td>3.000000</td>
      <td>45.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>中国大陆</th>
      <td>80.800000</td>
      <td>16.644928</td>
      <td>175.0</td>
      <td>116.0</td>
    </tr>
    <tr>
      <th>中国香港</th>
      <td>23.000000</td>
      <td>365.000000</td>
      <td>23.0</td>
      <td>365.0</td>
    </tr>
    <tr>
      <th>日本</th>
      <td>NaN</td>
      <td>15.000000</td>
      <td>NaN</td>
      <td>15.0</td>
    </tr>
    <tr>
      <th>美国</th>
      <td>47.500000</td>
      <td>4.666667</td>
      <td>50.0</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>韩国</th>
      <td>77.206897</td>
      <td>8.612903</td>
      <td>120.0</td>
      <td>108.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.groupby("country").agg({"name":"count", "episodes":np.mean, "num_actors":np.mean})
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>episodes</th>
      <th>num_actors</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>中国台湾</th>
      <td>1</td>
      <td>45.000000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>中国大陆</th>
      <td>143</td>
      <td>80.800000</td>
      <td>16.644928</td>
    </tr>
    <tr>
      <th>中国香港</th>
      <td>1</td>
      <td>23.000000</td>
      <td>365.000000</td>
    </tr>
    <tr>
      <th>日本</th>
      <td>1</td>
      <td>NaN</td>
      <td>15.000000</td>
    </tr>
    <tr>
      <th>美国</th>
      <td>3</td>
      <td>47.500000</td>
      <td>4.666667</td>
    </tr>
    <tr>
      <th>韩国</th>
      <td>31</td>
      <td>77.206897</td>
      <td>8.612903</td>
    </tr>
  </tbody>
</table>
</div>



类似R-merge(df1, df2, by.x="", by.y="", all.x=TRUE)的运算  


```python
df1 = df.pivot_table(index="country", values = "name", aggfunc="count").reset_index(); df1
df2 = pd.DataFrame({"country":["中国台湾", "中国大陆", "中国香港", "日本", "美国", "韩国"], 
                   "iso":["TWN", "CHN", "HKG", "JPN", "USA", "KOR"]}); df2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>中国台湾</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>中国大陆</td>
      <td>143</td>
    </tr>
    <tr>
      <th>2</th>
      <td>中国香港</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>日本</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>美国</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>韩国</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>






<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>iso</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>中国台湾</td>
      <td>TWN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>中国大陆</td>
      <td>CHN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>中国香港</td>
      <td>HKG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>日本</td>
      <td>JPN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>美国</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>5</th>
      <td>韩国</td>
      <td>KOR</td>
    </tr>
  </tbody>
</table>
</div>




```python
pd.merge(df1, df2, on = "country", how="left")

# 如果两个数据框里的变量名不一致，可以用left_on和right_on分别指定
# pd.merge(df1, df2, left_on = "国家", right_on = "country", how="left")
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>name</th>
      <th>iso</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>中国台湾</td>
      <td>1</td>
      <td>TWN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>中国大陆</td>
      <td>143</td>
      <td>CHN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>中国香港</td>
      <td>1</td>
      <td>HKG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>日本</td>
      <td>1</td>
      <td>JPN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>美国</td>
      <td>3</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>5</th>
      <td>韩国</td>
      <td>31</td>
      <td>KOR</td>
    </tr>
  </tbody>
</table>
</div>


