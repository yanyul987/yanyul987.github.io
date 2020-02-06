---
published: true
title: Python爬虫-以豆瓣电影为例
category: Web Scraping
tags: 
  - python
  - selenium
  - beautifulsoup
layout: post
excerpt_separator: <!--more-->
---

```python
import pandas as pd
import time
from bs4 import BeautifulSoup
from selenium import webdriver
```

# 豆瓣电影

## 确定目标页面/信息


```python
url = "https://movie.douban.com/explore#!type=movie&tag=%E7%83%AD%E9%97%A8&sort=recommend&page_limit=20&page_start=60"
```


```python
driver = webdriver.Chrome()
```

webdriver.Chrome()一句可能会报错：  
'chromedriver' executable needs to be in PATH. Please see https://sites.google.com/a/chromium.org/chromedriver/home  

>找到Chrome安装路径下的application文件夹：  
例如 C:\Program Files (x86)\Google\Chrome\Application  
(1) 将解压后的chromedriver.exe驱动文件复制到该目录。  
(2) 并将该目录添加到path中  
(3) 可能需要将chrome的版本更新到77版  


```python
driver.get(url)
```
<!--more-->

```python
'''
# 不断点击“加载更多”可参考以下代码
flag = 1;
i = 0; 
while (flag == 1):
    try:
        i = i +1;
        # driver.find_element_by_class_name("more").click()
        driver.find_element_by_link_text("加载更多").click()
        time.sleep(1)
    except:
        flag = 0;
'''
```

    3
    

## 抓取页面所有数据


```python
bs = BeautifulSoup(driver.page_source, "html.parser")
# BeautifulSoup: 解析页面
```

## 筛选所需信息

逐层深入（定位标签）：  
>.find("标签", {"元素名": "字段"})  
.find_all("标签", {"元素名": "字段"})  
.select("标签.字段")，其中字段必须是元素class的字段

在标签内定位元素：  
>.attrs["元素名"]

在bs.select("a.item")中我们可以得到电影的链接、名字、封面图、评分等信息


```python
bs1 = bs.select("a.item")
bs1[0]
```




    <a class="item" href="https://movie.douban.com/subject/30306570/?tag=\u70ed\u95e8&amp;from=gaia_video" target="_blank">\n<div class="cover-wp" data-id="30306570" data-isnew="false">\n<img alt="\u56e7\u5988" data-x="1080" data-y="1542" src="https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2581835383.jpg"/>\n</div>\n<p>\n                \n\n                \u56e7\u5988\n\n                \n                    <strong>5.9</strong>\n</p>\n</a>




```python
bs1 = bs.select("a.item")
len(bs1)
```




    20




```python
score = [float(ibs.select("p strong")[0].text) for ibs in bs1]
score
```




    [5.9,
     7.5,
     6.3,
     7.5,
     8.5,
     8.3,
     8.7,
     8.3,
     6.4,
     7.0,
     8.6,
     8.9,
     6.0,
     8.6,
     8.7,
     6.1,
     8.3,
     8.2,
     8.5,
     8.4]




```python
title = [ibs.select("div img")[0].attrs["alt"] for ibs in bs1]
# title = [bs.select("div.cover-wp")[i].select("img")[0].attrs["alt"] for i in len(bs1)]
for t in title: 
    print(t)
```

    囧妈
    半个喜剧
    葛蕾丝的坠落
    正义的慈悲
    1917
    小妇人
    小丑
    阳光普照
    只有芸知道
    被光抓走的人
    82年生的金智英
    爱尔兰人
    宠爱
    婚姻故事
    寄生虫
    两只老虎
    少年的你
    利刃出鞘
    极速车王
    乔乔的异想世界
    


```python
href = [ibs.attrs["href"] for ibs in bs1]
href
```




    [u'https://movie.douban.com/subject/30306570/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/30269016/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/34937473/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/26581206/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/30252495/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/26348103/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/27119724/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/30292777/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/33436135/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/30394535/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/30327842/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/6981153/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/33417046/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/27202818/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/27010768/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/30196805/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/30166972/?tag=\u70ed\u95e8&from=gaia_video',
     u'https://movie.douban.com/subject/30318116/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/6538866/?tag=\u70ed\u95e8&from=gaia',
     u'https://movie.douban.com/subject/30170546/?tag=\u70ed\u95e8&from=gaia']



构建数据框  
下面是Python的字典对象


```python
test_dict = {"id":[1,2,3,4,5,6],"name":['Alice','Bob','Cindy','Eric','Helen','Grace '],"math":[90,89,99,78,97,93]}
test_dict
```




    {'id': [1, 2, 3, 4, 5, 6],
     'math': [90, 89, 99, 78, 97, 93],
     'name': ['Alice', 'Bob', 'Cindy', 'Eric', 'Helen', 'Grace ']}



基于字典对象构建数据框


```python
pd.DataFrame(test_dict)
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
      <th>id</th>
      <th>math</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>90</td>
      <td>Alice</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>89</td>
      <td>Bob</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>99</td>
      <td>Cindy</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>78</td>
      <td>Eric</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>97</td>
      <td>Helen</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>93</td>
      <td>Grace</td>
    </tr>
  </tbody>
</table>
</div>




```python
DoubanMovie = pd.DataFrame({'title': title, 'score': score, 'weblink': href}, pd.Index(range(len(score))))
DoubanMovie
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
      <th>score</th>
      <th>title</th>
      <th>weblink</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5.9</td>
      <td>囧妈</td>
      <td>https://movie.douban.com/subject/30306570/?tag...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7.5</td>
      <td>半个喜剧</td>
      <td>https://movie.douban.com/subject/30269016/?tag...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6.3</td>
      <td>葛蕾丝的坠落</td>
      <td>https://movie.douban.com/subject/34937473/?tag...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7.5</td>
      <td>正义的慈悲</td>
      <td>https://movie.douban.com/subject/26581206/?tag...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8.5</td>
      <td>1917</td>
      <td>https://movie.douban.com/subject/30252495/?tag...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>8.3</td>
      <td>小妇人</td>
      <td>https://movie.douban.com/subject/26348103/?tag...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>8.7</td>
      <td>小丑</td>
      <td>https://movie.douban.com/subject/27119724/?tag...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8.3</td>
      <td>阳光普照</td>
      <td>https://movie.douban.com/subject/30292777/?tag...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>6.4</td>
      <td>只有芸知道</td>
      <td>https://movie.douban.com/subject/33436135/?tag...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>7.0</td>
      <td>被光抓走的人</td>
      <td>https://movie.douban.com/subject/30394535/?tag...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>8.6</td>
      <td>82年生的金智英</td>
      <td>https://movie.douban.com/subject/30327842/?tag...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>8.9</td>
      <td>爱尔兰人</td>
      <td>https://movie.douban.com/subject/6981153/?tag=...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>6.0</td>
      <td>宠爱</td>
      <td>https://movie.douban.com/subject/33417046/?tag...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>8.6</td>
      <td>婚姻故事</td>
      <td>https://movie.douban.com/subject/27202818/?tag...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>8.7</td>
      <td>寄生虫</td>
      <td>https://movie.douban.com/subject/27010768/?tag...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>6.1</td>
      <td>两只老虎</td>
      <td>https://movie.douban.com/subject/30196805/?tag...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>8.3</td>
      <td>少年的你</td>
      <td>https://movie.douban.com/subject/30166972/?tag...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>8.2</td>
      <td>利刃出鞘</td>
      <td>https://movie.douban.com/subject/30318116/?tag...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>8.5</td>
      <td>极速车王</td>
      <td>https://movie.douban.com/subject/6538866/?tag=...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>8.4</td>
      <td>乔乔的异想世界</td>
      <td>https://movie.douban.com/subject/30170546/?tag...</td>
    </tr>
  </tbody>
</table>
</div>




```python
DoubanMovie.to_excel("C:\\my\\own\\file\\doubanmovie.xls")
```

待更新：爬取WTO SPS notifications
