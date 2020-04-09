---
published: true
title: PyEcharts绘制玫瑰图
category: Data Visualization
tags: 
  - python
  - pyecharts
layout: post
excerpt_separator: <!--more-->
---
![img](/assets/images/20200409pyechart_pie/pie2_lev2_only.png){:width='60%'}
<!--more-->

## 数据读入及处理


```python
import pandas as pd
data = pd.read_excel("FileName.xlsx", sheet_name = "Faker")
data.head(10)# data["num"]与各扇区大小和半径有关，可以设置data["num"] = 1进行对比
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
      <th>lev1</th>
      <th>lev2</th>
      <th>lev3</th>
      <th>num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a11</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a12</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a13</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a14</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a15</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>5</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a16</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>6</th>
      <td>A大类</td>
      <td>A1</td>
      <td>a17</td>
      <td>0.68</td>
    </tr>
    <tr>
      <th>7</th>
      <td>A大类</td>
      <td>A2</td>
      <td>a21</td>
      <td>0.85</td>
    </tr>
    <tr>
      <th>8</th>
      <td>A大类</td>
      <td>A2</td>
      <td>a22</td>
      <td>0.85</td>
    </tr>
    <tr>
      <th>9</th>
      <td>A大类</td>
      <td>A2</td>
      <td>a23</td>
      <td>0.85</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 生成最内层数据
df_lev1 = data.pivot_table(index="lev1", values="num", aggfunc="sum").reset_index()
df_lev1
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
      <th>lev1</th>
      <th>num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A大类</td>
      <td>9.01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B大类</td>
      <td>9.60</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C大类</td>
      <td>13.60</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D大类</td>
      <td>16.85</td>
    </tr>
    <tr>
      <th>4</th>
      <td>E大类</td>
      <td>21.18</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 生成第二层数据
df_lev2 = data.pivot_table(index="lev2", values="num", aggfunc="sum").reset_index()
df_lev2["lev1"] = [item[0] for item in df_lev2.lev2]
df_lev2 = df_lev2.sort_values(by=["lev1", "num"])
df_lev2["lev2_ordered"] = [chr(97+idx) + "-" + item for idx,item in enumerate(df_lev2.lev2)]
df_lev2.head(10)
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
      <th>lev2</th>
      <th>num</th>
      <th>lev1</th>
      <th>lev2_ordered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A2</td>
      <td>4.25</td>
      <td>A</td>
      <td>a-A2</td>
    </tr>
    <tr>
      <th>0</th>
      <td>A1</td>
      <td>4.76</td>
      <td>A</td>
      <td>b-A1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>B1</td>
      <td>3.20</td>
      <td>B</td>
      <td>c-B1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>B2</td>
      <td>3.20</td>
      <td>B</td>
      <td>d-B2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B3</td>
      <td>3.20</td>
      <td>B</td>
      <td>e-B3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C1</td>
      <td>3.40</td>
      <td>C</td>
      <td>f-C1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>C2</td>
      <td>3.40</td>
      <td>C</td>
      <td>g-C2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>C3</td>
      <td>3.40</td>
      <td>C</td>
      <td>h-C3</td>
    </tr>
    <tr>
      <th>8</th>
      <td>C4</td>
      <td>3.40</td>
      <td>C</td>
      <td>i-C4</td>
    </tr>
    <tr>
      <th>11</th>
      <td>D3</td>
      <td>5.25</td>
      <td>D</td>
      <td>j-D3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 调整数据顺序
data =  pd.merge(data, df_lev2[["lev2", "lev2_ordered"]], on="lev2").drop(columns="lev2").rename(columns={"lev2_ordered":"lev2"})
data = data.sort_values(by=["lev2", "lev3"])

df_lev2["lev2"] = [item.split("-")[-1] for item in df_lev2.lev2]
del df_lev2["lev2_ordered"]
```

## 设置颜色


```python
# 颜色------------------------------------
df_pal = pd.DataFrame({
	"red":['#A9180F', '#DB5C5C', '#FD9B99'], 
	"yellow":['#E6A400', '#FFC229', '#FBE5B3'], 
	"green":['#166138', '#219355', '#B9D998'], 
	"blue":['#285171', '#5580A1', '#86A7C0'], 
	"purple":['#80506E', '#A46E90','#DEACAB']
	})
```

![img](/assets/images/20200409pyechart_pie/output_26_1.png){:width='50%'}


```python
# 第一层 上最深的红橙绿蓝紫色
df_lev1["color"] = df_pal.iloc[0].tolist()

# 第二层 上次深的颜色
pal_lev2 = pd.DataFrame({'lev1':['A', 'B', 'C', 'D', 'E'], 'color':df_pal.iloc[1].tolist()})
df_lev2 = pd.merge(df_lev2, pal_lev2, on="lev1")

# 第三层 上最浅的颜色
data["lev1"] = [item[0] for item in data.lev1]
pal_lev3 = pd.DataFrame({'lev1':['A', 'B', 'C', 'D', 'E'], 'color':df_pal.iloc[2].tolist()})
data = pd.merge(data, pal_lev3, on="lev1")

data.loc[data.lev3.isna(), "color"] = "#ffffff" # 第三层为空值的扇区上白色
```

## 绘图！

pyecharts全局参数：https://pyecharts.org/#/zh-cn/global_options  
系列参数：https://pyecharts.org/#/zh-cn/series_options  
饼图参数：https://pyecharts.org/#/zh-cn/basic_charts  


```python
from pyecharts.charts import Pie
from pyecharts import options as opts

# 初始化，设置长、宽、背景色
pie1 = Pie(init_opts=opts.InitOpts(width='350px', height='320px', bg_color='#ffffff')) 

# 全局参数，可设置标题、是否显示图例、是否显示工具箱等
pie1.set_global_opts(# title_opts=opts.TitleOpts(title='玫瑰图示例'),
                     legend_opts=opts.LegendOpts(is_show=False),
                     toolbox_opts=opts.ToolboxOpts( ),
                    )

# 设置颜色
pie1.set_colors(
    df_lev1.color.tolist() # 最深的红橙黄蓝紫色，['#A9180F', '#E6A400', '#166138', '#285171', '#80506E']
)

pie1.add("第一层", [list(z) for z in zip(df_lev1.lev1, df_lev1.num)],
        radius=["11%", "90%"], # 设置半径，第一层覆盖圆形半径为11%-90%的区域（即一个环形）
        label_opts=opts.LabelOpts( # 设置label（标签）相关属性
                                  is_show=True,  # 是否显示label
                                  font_family="STZhongSong", # label的字体，设置为华文中宋
                                  rotate="auto", #自动旋转，可设置rotate=0或90等参数进行比较
                                  position="inside", # label的位置，可以设置"top", "bottom"等进行比较
                                  color="#ffffff",font_size=19,# label字体颜色和字号
                                 ), 
        itemstyle_opts=opts.ItemStyleOpts( # 设置Item（图元，在饼图中是各个扇区）相关属性
                                  border_color="#ffffff", # 边界颜色为白色，若不加以设置，默认无边界
                                 ),
        )

# 保存为图片，需要import两个包，而且要设置好ChromeDriver的驱动，具体操作见另一篇博文【Selenium+BeautifulSoup爬虫-以豆瓣电影为例】
from pyecharts.render import make_snapshot
from snapshot_selenium import snapshot
make_snapshot(snapshot, pie1.render(), "./WhatEverPath/pie1_lev1_only.png")
# 也可以保存为html格式，更容易，不用import上面两个包
# pie1.render('./WhatEverPath/pie1.html')
```

![img](/assets/images/20200409pyechart_pie/pie1_lev1_only.png){:width='40%'}


rosetype: 是否展示成南丁格尔玫瑰图，通过半径区分数据大小，有'radius'和'area'两种模式。  
>    None: 扇区圆心角不同，半径相同（普通扇形图）  
    radius：扇区圆心角展现数据的百分比，半径展现数据的大小  
    area：所有扇区圆心角相同，仅通过半径展现数据大小  


```python
pie1 = Pie(init_opts=opts.InitOpts(width='1050px', height='320px', bg_color='#ffffff')) 

# 全局参数
pie1.set_global_opts(# title_opts=opts.TitleOpts(title='玫瑰图示例'),
                     legend_opts=opts.LegendOpts(is_show=False),
                     toolbox_opts=opts.ToolboxOpts( ),
                    )

# 系列参数
my_labelOpts = opts.LabelOpts(is_show=True, font_family="STZhongSong",rotate="auto", position="inside", color="#ffffff",font_size=19)
my_itemOpts = opts.ItemStyleOpts( border_color="#ffffff" )

# 设置颜色
pie1.set_colors( df_lev1.color.tolist() )

pie1.add("rosetype=None", [list(z) for z in zip(df_lev1.lev1, df_lev1.num)],
        radius=["11%", "90%"], 
        center=["16%", "50%"],
        label_opts=my_labelOpts, itemstyle_opts=my_itemOpts
        )

pie1.add("rosetype=area", [list(z) for z in zip(df_lev1.lev1, df_lev1.num)],
        radius=["11%", "90%"], 
        center=["50%", "50%"],
        rosetype="area",
        label_opts=my_labelOpts, itemstyle_opts=my_itemOpts
        )

pie1.add("rosetype=radius", [list(z) for z in zip(df_lev1.lev1, df_lev1.num)],
        radius=["11%", "90%"], 
        center=["84%", "50%"],
        rosetype="radius",
        label_opts=my_labelOpts, itemstyle_opts=my_itemOpts
        )

make_snapshot(snapshot, pie1.render(), "./assets/images/20200409pyechart_pie/pie1_rosetype_compare.png")
```


从左到右依次是None, area, radius  
![img](/assets/images/20200409pyechart_pie/pie1_rosetype_compare.png){:width="100%"}


```python
pie2 = Pie(init_opts=opts.InitOpts(width='800px', height='700px', bg_color='#ffffff'))

pie2.set_global_opts(legend_opts=opts.LegendOpts(is_show=False),
                     toolbox_opts=opts.ToolboxOpts( ),
                    )

myfont = "STZhongSong" # 华文中宋
isShow = True # 是否显示label

pie2.set_colors(
    ["#FFFFFF"]+df_lev1.color.tolist()+df_lev2.color.tolist()
)

pie2.add("第零层", [["大标题", 1]],
        radius=["0%", "21%"],
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont,
                                  position="center", color="#000000",font_size=22), #, font_weight="bold"
        )

pie2.add("第一层", [list(z) for z in zip(df_lev1.lev1, df_lev1.num)],
        radius=["21%", "50%"],
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont, 
                                  rotate="auto", 
                                  position="inside", color="#ffffff",font_size=19),
        itemstyle_opts=opts.ItemStyleOpts(border_color="#ffffff"),
        )

pie2.add("第二层", [list(z) for z in zip(df_lev2.lev2, df_lev2.num)], 
        radius=["50%", "90%"],
        rosetype="radius",
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont, 
                                  rotate="auto", 
                                  position="inside",color="#ffffff", font_size=17),
        itemstyle_opts=opts.ItemStyleOpts(border_color="#ffffff"),
        )

make_snapshot(snapshot, pie2.render(), "./WhatEverPath/pie2_lev2_only.png")
```


![img](/assets/images/20200409pyechart_pie/pie2_lev2_only.png){:width='60%'}




```python
pie3 = Pie(init_opts=opts.InitOpts(width='1350px', height='1100px', bg_color='#ffffff'))

pie3.set_global_opts(legend_opts=opts.LegendOpts(is_show=False),
                     toolbox_opts=opts.ToolboxOpts( ),
                    )

myfont = "STZhongSong" # 华文中宋
isShow = True # 是否显示label

pie3.set_colors(
    ["#FFFFFF"]+df_lev1.color.tolist()+df_lev2.color.tolist()+data.color.tolist()
)

pie3.add("第零层", [["大标题", 1]],
        radius=["0%", "11%"],
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont,
                                  position="center", color="#000000",font_size=22), #, font_weight="bold"
        )

pie3.add("第一层", [list(z) for z in zip(df_lev1.lev1, df_lev1.num)],
        radius=["11%", "30%"],
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont, 
                                  rotate="auto", 
                                  position="inside", color="#ffffff",font_size=19),
        itemstyle_opts=opts.ItemStyleOpts(border_color="#ffffff"),
        )

pie3.add("第二层", [list(z) for z in zip(df_lev2.lev2, df_lev2.num)], 
        radius=["30%", "60%"],
        rosetype="radius",
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont, 
                                  rotate="auto", 
                                  position="inside",color="#ffffff", font_size=17),
        itemstyle_opts=opts.ItemStyleOpts(border_color="#ffffff"),
        )

pie3.add("第三层", [list(z) for z in zip(data.lev3, data.num)], 
        radius=["60%", "93%"],
#         rosetype="radius",
        label_opts=opts.LabelOpts(is_show=isShow, font_family=myfont, 
                                  rotate="auto", 
                                  position="inside", color="#000000",font_size=16),
        itemstyle_opts=opts.ItemStyleOpts(border_color="#ffffff"),
        )

make_snapshot(snapshot, pie3.render(), "./WhatEverPath/pie3_full.png")
```



![img](/assets/images/20200409pyechart_pie/pie3_full.png){:width='80%'}
