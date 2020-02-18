---
published: true
title: 韩综族谱
category: Neo4j
tags: 
  - python
  - selenium
  - beautifulsoup
  - neo4j
  - py2neo
layout: post
excerpt_separator: <!--more-->
---

![png](/assets/images/20200218neo4j/graph_0_naPD.png)

## 爬虫获取综艺节目数据  
yay，又是豆瓣

<!--more-->


```python
from selenium import webdriver
from bs4 import BeautifulSoup
import pandas as pd
import time
import eventlet
ts1 = lambda : time.sleep(1)
ts3 = lambda : time.sleep(3)
import pandas as pd
```


```python
url = "https://movie.douban.com/tv/#!type=tv&tag=%E7%BB%BC%E8%89%BA&sort=recommend"
driver = webdriver.Chrome()
driver.get(url)
for i in range(10):
    time.sleep(2)
    driver.find_element_by_xpath("//*/a[@class='more']").click()

ts3()
soup = BeautifulSoup(driver.page_source, 'html5lib')
items = soup.select("div.list a.item")
link = [item["href"] for item in items]
```


```python
dfshow = pd.DataFrame(columns=["name", "year", "country", "director", "actor", "type", "lang", "date", "episodes"])
driver=webdriver.Chrome()
```


```python
for i, url in enumerate(link):
    eventlet.monkey_patch()
    with eventlet.Timeout(7, False):
        driver.get(url)
    ts1()
    response=BeautifulSoup(driver.page_source, "html5lib")
    soup=response.select("div.subject.clearfix div[id=info]")[0]
    alltext = soup.text

    icoun = alltext.split("制片国家/地区: ")[1].split()[0]
    iname = response.select("h1 span")[0].text
    iyear = int(response.select("h1 span")[1].text.split('(')[1].split(')')[0])
    try:
        ipd = alltext.split("导演: ")[1].split()[0]
    except IndexError:
        ipd = None
    try:
        iactor = soup.select("span.actor")[0].text.split("主演: ")[1].split("更多")[0]
    except IndexError:
        iactor = None
    itype = alltext.split("类型: ")[1].split()[0]
    ilang = alltext.split("语言: ")[1].split()[0]
    idate = alltext.split("首播: ")[1].split()[0]
    try:
        iepi = alltext.split("单集片长: ")[1].split()[0]
    except IndexError:
        iepi = None

    dfshow = dfshow.append({"name":iname, "year":iyear, "country":icoun, "director":ipd,"actor":iactor, "type":itype, "lang":ilang, "date":idate, "episodes":iepi}, ignore_index=True)

print(f"Got {dfshow.shape[0]} entertainment shows. ")
```


```python
dfshow["link"] = link
dfshow.head()
# pd.pivot_table(dfshow, index="country", values="name", aggfunc="count")
# dfshow.to_excel("korEntShow2.xlsx")
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
      <th>actor</th>
      <th>country</th>
      <th>date</th>
      <th>director</th>
      <th>episodes</th>
      <th>lang</th>
      <th>name</th>
      <th>type</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>姜虎东 / 李寿根 / 殷志源 / 安宰贤 / 宋旻浩 / 表志勋</td>
      <td>韩国</td>
      <td>2019-05-31(韩国)</td>
      <td>罗英石</td>
      <td>100分钟</td>
      <td>韩语</td>
      <td>姜食堂 第二季 강식당  시즌2</td>
      <td>真人秀</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>1</th>
      <td>姜虎东 / 李寿根 / 殷志源 / 安宰贤 / 曹圭贤 / 宋旻浩 / 表志勋</td>
      <td>韩国</td>
      <td>2019-07-05(韩国)</td>
      <td>罗英石</td>
      <td>100分钟</td>
      <td>韩语</td>
      <td>姜食堂 第三季 강식당 시즌3</td>
      <td>真人秀</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>2</th>
      <td>大左 / 杨迪 / 贝乐泰 / 功必扬 / 华波波 / 浅井悠佑 / 田原皓 / 陈铭 / 陈超</td>
      <td>中国大陆</td>
      <td>2016-12-09(中国大陆)</td>
      <td>NaN</td>
      <td>80分钟</td>
      <td>汉语普通话</td>
      <td>非正式会谈 第三季</td>
      <td>真人秀</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>3</th>
      <td>谢娜 / 李娜 / 杨千嬅 / 霍思燕 / 唐一菲 / 魏大勋 / 丁子高 / 杜江 / 凌...</td>
      <td>中国大陆</td>
      <td>2019-11-06(中国大陆)</td>
      <td>蔡欢</td>
      <td>99分钟</td>
      <td>汉语普通话</td>
      <td>妻子的浪漫旅行 第三季</td>
      <td>真人秀</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>4</th>
      <td>姜虎东 / 金钟民 / 金东炫 / 申东熙 / 柳炳宰 / 表志勋</td>
      <td>韩国</td>
      <td>2019-03-17(韩国)</td>
      <td>郑中渊</td>
      <td>75分钟</td>
      <td>韩语</td>
      <td>大逃脱 第二季 대탈출 시즌2</td>
      <td>真人秀</td>
      <td>2019</td>
    </tr>
  </tbody>
</table>
</div>


## Neo4j


```python
import pandas as pd
from py2neo import Graph, Node, Relationship, NodeMatcher
import numpy as np
# import py2neo
# print(py2neo.__version__)
```


```python
dfshow = pd.read_excel("korEntShow2.xlsx")
dfshow = dfshow[dfshow["country"]=="韩国"]
del dfshow["Unnamed: 0"]
dfshow.shape
dfshow.columns
```




    (31, 10)
    Index(['actor', 'country', 'date', 'director', 'episodes', 'lang', 'link',
           'name', 'type', 'year'],
          dtype='object')



**Graph()连接Neo4j**  
注意需要现在命令行开启Neo4j service  
    neo4j install-service  
    neo4j start


```python
graph = Graph(host="localhost", auth=("neo4j", "123456")) # 不用localhost:7687或者localhost:7474，自动加上了7687
```

<b>Step 1: 既创建show结点，也创建演员集合actorset</b>


```python
actorset = set()
for i in range(dfshow.shape[0]):
    # 创建show结点 - graph.create( Node(...) )
    # 不知道为什么属性值不能是整型(year = dfshow.iloc[i]["year"]会报错），必须转成字符串
    node = Node("show", name=dfshow.iloc[i]["name"], year=str(dfshow.iloc[i]["year"]),type=dfshow.iloc[i]["type"], date=dfshow.iloc[i]["date"], episodes=dfshow.iloc[i]["episodes"])
    graph.create(node)
    # print(f'processing node no.{i+1} ')
    # 创建演员集合
    actorlist = dfshow.iloc[i]["actor"].split(" / ")
    actorset = actorset.union(actorlist) # set对象.union()括号中可以是列表
print('Created show nodes successfully!')
```

    Created show nodes successfully!
    

![img](/assets/images/20200218neo4j/graph_1_allshow.png){:width=500}

<b>Step 2: 创建演员结点</b>  


```python
for actor in actorset:
    node = Node("actor", name=actor)
    graph.create(node)
print(f'create {len(actorset)} actor nodes successfully!')
```

    create 198 actor nodes successfully!
    

![img](/assets/images/20200218neo4j/graph_2_allactor.png){:width=500}

<b>Step 3: 创建演员->综艺节目的关系</b>  
需要先MATCH到actor/show结点  
而MATCH结点可以有下面两种做法

法1：graph.run("Cypher语句")


```python
cypher2 = "MATCH (b:actor{name:\"" + actor + "\"})"; cypher2
```




    'MATCH (b:actor{name:"Paul kim"})'




```python
showname = dfshow.iloc[0]["name"]
actor = list(actorset)[0]

cypher1 = "MATCH (a:show{name:\""+showname+"\"}) RETURN a"
print("Running>>>>"+cypher1)
m1 = graph.run(cypher1)
m1.to_data_frame()

cypher2 = "MATCH (b:actor{name:\"" + actor + "\"}) RETURN b"
print("Running>>>>"+cypher2)
m2 = graph.run(cypher2)
m2.to_data_frame()
```

    Running>>>>MATCH (a:show{name:"姜食堂 第二季 강식당  시즌2"}) RETURN a
    




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
<table border="1" class="dataframe" width="300">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{'date': '2019-05-31(韩国)', 'name': '姜食堂 第二季 강식...</td>
    </tr>
  </tbody>
</table>
</div>



    Running>>>>MATCH (b:actor{name:"Paul kim"}) RETURN b
    




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
<table border="1" class="dataframe" width="300">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{'name': 'Paul kim'}</td>
    </tr>
  </tbody>
</table>
</div>



法2：用NodeMatcher()


```python
matcher = NodeMatcher(graph)

# 下面这两个用法都会报错old parameter '{param}' no longer supported，应该是对Neo4j 4.0+的版本不适用
# matcher.match("pd", name="罗英石").first()
# matcher.match("pd", name="罗英石") 

# 这个方法是可以用的
matcher.match("actor").where("_.name='殷志源'")
list(matcher.match("actor").where("_.name='殷志源'"))
```




    <py2neo.matching.NodeMatch at 0x1c71cfe6b00>
   [(_317:actor {name: '\u6bb7\u5fd7\u6e90'})]



仍然是法2：我们需要找到唯一的结点，而不是结点列表（哪怕长度为1)  
一般来讲需要用matcher.match("show", name=showname).first()的语句  
still a little bit tricky here


```python
showname = dfshow.iloc[0]["name"]
# matcher.match("show").where(f"_.name=~'{showname}'").fisrt() # 直接这样.first不行
item = matcher.match("show").where(f"_.name=~'{showname}'")
item.first()
```




    (_229:show {date: '2019-05-31(\u97e9\u56fd)', episodes: '100\u5206\u949f', name: '\u59dc\u98df\u5802 \u7b2c\u4e8c\u5b63 \uac15\uc2dd\ub2f9\u00a0 \uc2dc\uc98c2', type: '\u771f\u4eba\u79c0', year: '2019'})



介绍完MATCH结点的方法，开始CREATE RELATIONSHIP


```python
# 法1：graph.run()
for i in range(dfshow.shape[0]):
    showname = dfshow.iloc[i]["name"]
    actorlist = dfshow.iloc[i]["actor"].split(" / ")
    for actor in actorlist:
        graph.run("MATCH (a:show{name:\""+showname+"\"})" + ",(b:actor{name:\"" + actor + "\"})" +\
                 "CREATE (a)-[:ACT_IN]->(b)")
    # print(f"processing relationship for show no. {i+1}")
print('create relationships successfully!')
```

    create relationships successfully!
    

![img](/assets/images/20200218neo4j/graph_3_actorshow.png){:width=500}  
Produce 101第二季...This is crazy!


```python
# 法2：NodeMatcher
for i in range(dfshow.shape[0]):
    showname = dfshow.iloc[i]["name"]
    shownode = matcher.match("show").where(f"_.name='{showname}'")
    actorlist = dfshow.iloc[i]["actor"].split(" / ")
    matcher=NodeMatcher(graph)
    for actor in actorlist:
        actornode = matcher.match("actor").where(f"_.name='{actor}'")
        relation = Relationship(actornode.first(), 'ACT_IN', shownode.first())
        graph.create(relation)
    # print(f"processing relationship for show no. {i+1}")
print('create relationships successfully!')
```

    create relationships successfully!
    

![img](/assets/images/20200218neo4j/graph_4_actorshow.png){:width=500}  
姜食堂3新入成员曹圭贤！

Step 3: 把PD们也加上，思路和前两步差不多  
要注意的是director列表里有空值  
还不是None，是NaN，判断比较tricky


```python
dfshow["director"][0:5]
```




    0    罗英石
    1    罗英石
    4    郑中渊
    6    NaN
    7    罗英石
    Name: director, dtype: object




```python
pdlist = set()
for i in range(dfshow.shape[0]):
    ipd = dfshow.iloc[i]["director"]
    
    # 判断是否NaN：if np.isnan(ipd): # 对于ipd是nan时好使，对ipd是str时不好使
    if isinstance(ipd, float): # 幸好NaN是float，其他PD们是string
        continue
        
    if ipd not in pdlist: # 防止重复新建结点。像罗PD这样高产的很有可能建出几十个点来。也可以像actorset那样先遍历一遍建set，再遍历set建结点
        node = Node("pd", name=ipd)
        graph.create(node)
    
    showname = dfshow.iloc[i]["name"]
    # 仍然是两种思路，法1：
    # graph.run("MATCH (s: show{name:\""+ showname + "\"})" + ", (p: pd{name:\""+ ipd +"\"})" + " CREATE (p)-[:DIRECT]->(s)")
    # 法2：
    matcher = NodeMatcher(graph)
    shownode = matcher.match("show").where(f"_.name='{showname}'")
    pdnode = matcher.match("pd").where(f"_.name='{ipd}'")
    relation = Relationship(pdnode.first(), 'DIRECT', shownode.first())
    graph.create(relation)
    
    pdlist.add(ipd)
    
print('create relationships successfully!')
```

    create relationships successfully!
    

![img](/assets/images/20200218neo4j/graph_5_pdshow.png){:width=500}  
罗导真是高产似..
