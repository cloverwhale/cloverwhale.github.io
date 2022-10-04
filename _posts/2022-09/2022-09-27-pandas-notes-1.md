---
layout: post
title: "Homework: Space Mission Analysis"
date: 2022-09-27 21:02 +0800
description:
image:
category: [Notes]
tags: [python, pandas, jupyter notebook, plotly]
author: cloverwhale
---
紀錄100 Days of Code的作業: [分析Space Race](https://github.com/cloverwhale/py17_AnalyseSpaceRace)時遇到的一些問題 (還有常常忘記的method...)


> 注意data type. 例如在分析像Price這種欄位時, 即使使用了pandas.Series.str等method做了處理, 還要記得轉成對應的numeric type. 不然無法做正確的sorting或是繪圖. 
{: .prompt-tip }

> 要複寫原來的df要加上inplace=True
{: .prompt-tip }

## 常用的methods

### Column related

#### Drop column

```python
df_status_dt_year.drop(['Date'], axis=1, inplace=True)
```

#### Rename column

有寫到column的才會改
```python
df_status_success_yr_count.rename(columns={'ISO': 'Success'}, inplace=True)
```
### Index related

#### as_index=False

group by後不要當作index, 使用as_index=False
```python
df_status_dt = df_data.groupby(['Date','Mission_Status'], as_index=False).count()
```

#### Reset index

``` python
df_org_stat_yr.reset_index(level=[0,1], inplace=True)
```
level=[0,1] 表示index 0和1, 以下圖例子來說就是Date和Organisation這兩個index

Before reset index :

![before reset index](/assets/img/posts/2022-09/before_reset_index.png){: style="max-width: 50%" .normal}

After reset index :

![after reset index](/assets/img/posts/2022-09/after_reset_index.png){: style="max-width: 50%" .normal}

### Plotly

這個作業我只用了Plotly畫圖(真懶, 只想會一種 >"<)

Precondtion:
```python
import plotly.express as px
```

顯示圖片:
```python
fig.show()
```

#### Line

- [Plotly: Line Charts](https://plotly.com/python/line-charts/)

```python
fig = px.line(
    df_top_10_dt_year, 
    x=df_top_10_dt_year.index.array,  # x data來源column
    y='Location',  # y data來源column
    color='Organisation',  # 多個線條, 顏色的data來源column
    markers=True,  # 是否顯示marker
    title='Number of Launches over Time by the Top 10 Organisations',  # 圖表標題
    labels= {  # 標籤名稱設定
        'Location': 'Number of Launches',
        'x': 'Year'
    }
)
fig.update_layout(xaxis=dict(range=[1957,2020]))  # 設定x軸的上下限, y軸則是yaxis
```

#### Bar

- [Plotly: Bar Charts](https://plotly.com/python/bar-charts/)

```python
fig = px.bar(
    df_org,
    x=df_org.index.array,  # x data來源column
    y='Organisation',  # y data來源column
    title='Number of launches per Company',  # 圖表標題
    labels={  # 標籤名稱設定
        'y': 'Launches',
        'x': 'Organisation'
    }
)
```

#### Pie

- [Plotly: Pie Charts](https://plotly.com/python/pie-charts/)

```python
fig = px.pie(
    df_r_status,
    values='Rocket_Status',  # data來源column
    names=df_r_status.index.array,  # 標籤名稱設定
    title='Active versus Retired Rockets'  # 圖表標題
)
```
- hole = .3 # 可設定成甜甜圈圖

#### Histogram

- [Plotly: Histograms](https://plotly.com/python/histograms/)

```python
fig = px.histogram(
    df_p['Price_N'],
    nbins=50,  # bin number
    labels={  # 標籤名稱設定
        'value': 'millions USD'
    }
)
```

#### Choropleth Maps

- [Plotly: Choropleth Maps](https://plotly.com/python/choropleth-maps/)
- [Plotly: Built-in Continuous Color Scales in Python](https://plotly.com/python/builtin-colorscales/)

```python
fig = px.choropleth(
    df_country_counts,
    locations='ISO',  # 地圖位置data來源column
    color='Counts',  # 顏色data來源column
    hover_name='Country',  # 滑鼠移上去時顯示的名稱資料來源
    color_continuous_scale=px.colors.sequential.matter,  # 顏色組合
    title='Number of Launches by Country',  # 圖表標題
    labels={  # 標籤名稱設定
        'Counts': 'Number of Launches'
    }
)
```

#### Sunburst

- [Plotly: Sunburst Charts](https://plotly.com/python/sunburst-charts/)

```python
fig = px.sunburst(
    df_mission_status,
    path=['ISO', 'Mission_Status'],  # 圖表分佈的data來源columns(由中心向外)
    values='Mission_Status_Count'  # 數值來源
)
```

