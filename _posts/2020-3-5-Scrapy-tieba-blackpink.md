---
published: true
title: Scrapy爬取BLACKPINK贴吧
category: Web Scraping
tags: 
  - python
  - scrapy
layout: post
excerpt_separator: <!--more-->
---

![png](/assets/images/20200305tieba_bp/tieba_bp.png)

# 新建项目

在命令行中，先访问想要创建项目的位置，例如E盘根目录，然后输入  

    scrapy startproject tieba_spider # tieba_spider是项目名字，可自定义
    cd tieba_spider
    scrapy genspider tieba_bp https://tieba.baidu.com/f?kw=blackpink # tieba_bp是爬虫文件的名字，其后是爬虫的目标网址

此时在创建项目的位置（例如E:\）会出现名为tieba_spider的文件夹
<!--more-->  
其中名为spiders的文件夹下有tieba_bp.py，已经生成了这样的语句


```python
# tieba_bp.py
class TiebaBpSpider(scrapy.Spider):
    name = 'tieba_bp'
    allowed_domains = ['https://tieba.baidu.com/f?kw=blackpink']
    start_urls = ['http://https://tieba.baidu.com/f?kw=blackpink/']
```

将其修改成


```python
# tieba_bp.py
class TiebaBpSpider(scrapy.Spider):
    name = 'tieba_bp'
    allowed_domains = ['https://tieba.baidu.com']
    start_urls = ['https://tieba.baidu.com/f?kw=blackpink'] # 把多余的http://和最后的斜杠删掉，否则会访问错误的吧名blackpink/
```

在items.py, pipelines.py等文件所在的tieba_spider目录下，新建文件main.py，方便运行爬虫和debug


```python
# main.py
__author__ = "作者的名字" # 自定义
__date__ = "创建的日期" # 自定义

from scrapy.cmdline import execute
import sys
import os

sys.path.append(os.path.dirname(os.path.abspath(__file__)))
execute(["scrapy", "crawl", "tieba_bp"]) # 执行命令行语句
# 注意execute的参数类型为一个列表,上述语句在命令行中对应scrapy crawl tieba_bp  
# 这一句的执行后会进入tieba_bp.py开始爬虫  
# 其中tieba_bp处应填入爬虫文件tieba_bp.py中的name
```

建立了main.py文件后，可以在pycharm中通过右键->Run main来执行爬虫，也可以右键->Debug main来debug  
断点可以设置在tieba_bp.py(爬虫文件)和pipelines.py等文件中，但注意Run和Debug的对象是main.py

# 爬取规则

爬取规则在tieba_bp.py中编写，只要在自动生成的代码基础上修改即可，先看代码全貌  


```python
# -*- coding: utf-8 -*-
import scrapy
from urllib import parse
from tieba_spider.items import TiebaSpiderItem # 在数据存储部分解释

class TiebaBpSpider(scrapy.Spider):
    name = 'tieba_bp'
    allowed_domains = ['tieba.baidu.com']
    start_urls = ['https://tieba.baidu.com/f?kw=blackpink']

    def parse(self, response):
        cur_page = int(response.css(".pagination-current::text").extract()[0]) # 获取当前页码

        # 数据爬取规则 ---------------
        title = response.css('div.content div.threadlist_title a::attr("title")').extract()
        # 也可以response.css('div.content div.threadlist_title a::text').extract()
        url = response.css('div.content div.threadlist_title a::attr("href")').extract()
        url = [parse.urljoin(response.url, iurl) for iurl in url]
        author = response.css('div.content div.threadlist_author span.tb_icon_author::attr("title")').extract() # 作者
        author = [iauthor.split("主题作者: ")[1] for iauthor in author]
        last_post_time = response.css('div.content div.threadlist_author span.pull-right::text').extract() # 发布时间

        # 存储当页数据 ---------------
        for i in range(len(author)):
            tieba_item = TiebaSpiderItem() # 生成类TiebaSpiderItem
            tieba_item["title"] = title[i]
            tieba_item["author"] = author[i]
            tieba_item["url"] = url[i]
            tieba_item["last_post_time"] = last_post_time[i]
            yield tieba_item # 返回tieba_item

        # 翻页 ---------------
        if cur_page<5: # 只爬取前5页，也可以不加这个条件，会爬取该贴吧所有页面
            next_url = parse.urljoin(response.url, response.css(".next.pagination-item::attr('href')").extract()[0])
            # parse.urljoin: 拼接，比简单的字符串拼接(+)更智能，会删去重复的元素、多余的斜杠之类的
            if next_url:
                yield scrapy.Request(next_url, callback=self.parse) # callback函数不能加括号，要传的参数是函数名
                # 这一句的作用是将scrapy.Request(下一页链接)返回的结果(response)作为参数传入parse函数进行处理
                # 注意是request不是requests!!!

```

只看数据爬取规则部分，获得了四个变量：title, url, author, last_post_time


```python
# 数据爬取规则 ---------------
title = response.css('div.content div.threadlist_title a::attr("title")').extract()
# 也可以response.css('div.content div.threadlist_title a::text').extract()
url = response.css('div.content div.threadlist_title a::attr("href")').extract()
url = [parse.urljoin(response.url, iurl) for iurl in url]
author = response.css('div.content div.threadlist_author span.tb_icon_author::attr("title")').extract() # 作者
author = [iauthor.split("主题作者: ")[1] for iauthor in author]
last_post_time = response.css('div.content div.threadlist_author span.pull-right::text').extract() # 发布时间
```

可以通过**scrapy自带的调试功能**来确定css/xpath规则。  
使用方法（以下命令均在命令行中输入）：  

进入调试：scrapy shell "https://tieba.baidu.com/f?kw=blackpink"  
待执行完命令后，可以输入resposne.text来查看返回的源码  
也可以输入resposne.css(""), response.xpath("")等来访问具体位置的源码，注意要加上.extract()才能顺利定位目标元素  

退出调试：exit()

也可以在tieba_bp.py中设置进一步爬取详情页面的规则，我这次没有爬，大致方法如下：


```python
class TiebaBpSpider(scrapy.Spider):
    name = 'tieba_bp'
    allowed_domains = ['tieba.baidu.com']
    start_urls = ['https://tieba.baidu.com/f?kw=blackpink']

    def parse(self, response):
        cur_page = int(response.css(".pagination-current::text").extract()[0])
        
        # 数据爬取规则 ---------------
        url = response.css('div.content div.threadlist_title a::attr("href")').extract()
        url = [parse.urljoin(response.url, iurl) for iurl in url]
        
        # 爬取详情页面 ---------------
        for iurl in url:
            yield scrapy.Request(parse.urljoin(response.url, iurl), callback=self.parse_detail)
            # 这一句的作用是将scrapy.Request(帖子url)返回的结果(response)作为参数传入parse_detail函数（见下）进行处理
            # callback函数(self.parse_detail)不能加括号

        # 翻页 ---------------
        if cur_page<5:
            next_url = parse.urljoin(response.url, response.css(".next.pagination-item::attr('href')").extract()[0]) # 下一页
            if next_url:
                yield scrapy.Request(next_url, callback=self.parse) # callback函数不能加括号，要传的参数是函数名

    # 解析帖子详情页面
    def parse_detail(self, response):
        # post_content =  response.css("div.d_post_content::text").extract()
        # post_author = response.css("li.d_name a::text").extract()
        # 还可以写详情页内翻页等
        pass
```

# 数据存储


```python
# tieba_bp.py
# 存储当页数据 ---------------
for i in range(len(author)): # 逐行（观测）存储数据
    # 生成类TiebaSpiderItem
    tieba_item = TiebaSpiderItem() 
    
    # 将爬取的数据存储到tieba_item中
    tieba_item["title"] = title[i] # 此前已经生成了四个列表title, author, url, last_post_time
    tieba_item["author"] = author[i]
    tieba_item["url"] = url[i]
    tieba_item["last_post_time"] = last_post_time[i]
    
    # 返回tieba_item，通过pipelines.py中设置的管道进行数据存储
    yield tieba_item 
```

这里的TiebaSpiderItem是一个数据类型，是在tieba_bp.py文件最开始引入的（from tieba_spider.items import TiebaSpiderItem）  
显然，引入的位置是tieba_spider项目目录下的items.py


```python
# items.py
import scrapy

class TiebaSpiderItem(scrapy.Item):
    title = scrapy.Field()
    author = scrapy.Field()
    url = scrapy.Field()
    last_post_time = scrapy.Field()
    # 这里的title, author, url, last_post_item和tieba_bp.py中的变量tieba_item的属性对应
```

上面提到，yield tieba_item一句执行后，会将tieba_item中的数据通过pipelines.py中的管道进行存储  
至于选择哪个管道，是在settings.py中设置的


```python
# settings.py中有这样一部份，原本是被注释掉的
# Configure item pipelines
# See https://doc.scrapy.org/en/latest/topics/item-pipeline.html
#ITEM_PIPELINES = {
#    'tieba_spider.pipelines.TiebaSpiderPipeline': 300,
#}
```

将其编辑为


```python
# Configure item pipelines
ITEM_PIPELINES = {
    'tieba_spider.pipelines.XlsTwistedPipeline': 100
}
```

可以有多个管道，默认按ITEM_PIPELINES中的values由小到大优先运行，即values值越小越优先运行


```python
ITEM_PIPELINES = {
    # 'tieba_spider.pipelines.TiebaSpiderPipeline': 300,
    # 'tieba_spider.pipelines.MysqlTwistedPipeline' : 200,
    'tieba_spider.pipelines.XlsTwistedPipeline': 100
}
```

通过settings.py选择了管道之后，再进入pipelines.py设置具体的存储方式


```python
# pipelines.py
import xlwt
import xlrd
from xlutils.copy import copy

class XlsTwistedPipeline(object):
    def process_item(self, item, spider):
        path = "某个已经存在的excel文件路径.xls"
        # 用xlsx也可以顺利写入存储，但可能会无法打开文件，只要把后缀改为xls就可以打开了
        wb = xlrd.open_workbook(path)
        newb = copy(wb)
        cursheet = newb.get_sheet("Sheet1")
        k = len(cursheet.rows)
        cursheet.write(k, 0, item["title"]) # 注意这里的item就是从tieba_bp.py中传入的tieba_item，故四个属性的名字要一一对应
        cursheet.write(k, 1, item["author"])
        cursheet.write(k, 2, item["url"])
        cursheet.write(k, 3, item["last_post_time"])
        newb.save(path)
        return item
```

这里选择了excel文件来进行存储，也可以用数据库等  
到这里就可以通过Run main来执行爬虫了，爬到的数据如下：

<div style="overflow-x:auto;">

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>【警醒】BLACKPINK贴吧官博才是本吧唯一微博</td>
      <td>athenamoney</td>
      <td>https://tieba.baidu.com/p/5219405583</td>
      <td>2017-07</td>
    </tr>
    <tr>
      <th>1</th>
      <td>【yg粉墨团★191119★公告】征换新任吧主团队</td>
      <td>athenamoney</td>
      <td>https://tieba.baidu.com/p/6348004699</td>
      <td>2019-11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>【yg粉墨团★200121★讨论】私生饭爆料YG公司各团回归顺序</td>
      <td>宋茜TOP</td>
      <td>https://tieba.baidu.com/p/6454442885</td>
      <td>1-21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>【yg粉墨团★200225★图片】blackpink头像拿图</td>
      <td>niani⛄</td>
      <td>https://tieba.baidu.com/p/6511165761</td>
      <td>2-25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>【yg粉墨团★200301★讨论】高质画绘BLACKPINK</td>
      <td>宋茜TOP</td>
      <td>https://tieba.baidu.com/p/6521472599</td>
      <td>3-1</td>
    </tr>
  </tbody>
</table>
</div>


```python
# 与爬虫项目无关，记录一下关于xlrd的一些用法
ws = wb.sheet_by_name("Sheet1")
ws.col_values(2) # 获取第三列的值，type=list
# ref: https://www.cnblogs.com/chen0307/p/9598525.html
```

# Final review

各文件的功能  
>main.py: 运行文件，运行tieba_bp.py  
items.py：定义爬取的数据类型（TiebaSpiderItem），用于存储数据  
tieba_bp.py: 定义爬取规则，爬取的每一行数据（多列）生成TiebaSpiderItem这个类，yield tieba_item  
settings.py: 然后由settings中的ITEM_PIPELINES检测到tieba_item,并用pipelines中的MysqlTwistedPipeline来处理之  
pipelines.py: MysqlTwistedPipeline连接mysql数据库，执行insert into语句（存储数据）
