---
layout: post
title: "Homework: Deaths Involving Police in US Analysis"
date: 2022-10-04 20:44 +0800
description:
image:
category: [Notes]
tags: [python, pandas, jupyter notebook, plotly, seaborn]
author: cloverwhale
---
紀錄100 Days of Code的作業: [美國警察造成死亡的案件分析](https://github.com/cloverwhale/day99-Deaths-Involving-Police-US)時遇到的一些問題和用到的methods.

### .apply 和 .astype

置換非數字的字串, 再轉numeric type
```python
df_pct_poverty['poverty_rate'] = df_pct_poverty['poverty_rate']
    .apply(lambda x: str(x).replace('-', '0'))
    .astype('float64')
```
- [pandas.DataFrame.apply](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html)
- [pandas.DataFrame.astype](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html)


### .resample

用日期重新取樣, 使用的column須為日期的type
```python
df_fatalities_time = df_fatalities.resample('M', on='date').count()
```
- [pandas.DataFrame.resample](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.resample.html)

### Plotly

#### subplots

需要import graph_objects和subplots:
```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots
```

繪圖:
```python
# 設定subplots的columns and rows
fig = make_subplots(rows=2, cols=1,
                    subplot_titles=(
                        'Fatalities of Race Percentage of Top 10 cities', 
                        'Racial Makeup of Top 10 cities'))
# 設定圖例的顏色
colors = {'Asian': px.colors.qualitative.Vivid[0],
          'Black': px.colors.qualitative.Vivid[1],
          'Hispanic': px.colors.qualitative.Vivid[2],
          'Native American': px.colors.qualitative.Vivid[3],
          'White': px.colors.qualitative.Vivid[4],
          'Others': px.colors.qualitative.Vivid[5]}

race_columns = ['Asian', 'Black', 'Hispanic',
                'Native American', 'Others', 'White']

# 把每個race的資料疊上去
for c in race_columns:
    fig.add_trace(
        go.Bar(x=df_fatalities_top_10_pivot['City'],
               y=df_fatalities_top_10_pivot[c],
               name=c, marker_color=colors[c]),
        row=1, col=1
    )

# 這邊拿掉Others是因為下面的dataFrame沒有這個欄位
race_columns.remove('Others')
for c in race_columns:
    fig.add_trace(
        go.Bar(x=df_share_race_top_10_city['City'],
               y=df_share_race_top_10_city[c],
               name=c, marker_color=colors[c], showlegend=False),
        row=2, col=1
    )

# 設定bar的樣式, 圖表大小和標題
fig.update_layout(barmode='stack')
fig.update_layout(height=800, width=800,
                  title_text="Death by Race")
fig.show()
```

畫出來的樣子:

![fig1](/assets/img/posts/2022-10/fig1.png){: style="max-width: 75%" .normal}


#### choropleth

- [locationmode](https://plotly.com/python/reference/#scattergeo-locationmode): 設定地圖組成的樣式, 美國使用'USA-states', default是'ISO-3'
- [scope](https://plotly.com/python/reference/#layout-geo-scope), 設定地圖範圍

```python
fig = px.choropleth(df_fatalities_state, locations='state',
                    color='killings',
                    hover_name='state',
                    locationmode="USA-states", scope="usa",
                    color_continuous_scale=px.colors.sequential.Cividis_r,
                    title='Number of Police Kilings by US State'
                    )
```

### Seaborn

```python
mport matplotlib.pyplot as plt
import seaborn as sns
```
- 使用plt更改圖表設定, 像是標題, x軸y軸的label.

#### joinplot

- [seaborn.jointplot](https://seaborn.pydata.org/generated/seaborn.jointplot.html)
> Draw a plot of two variables with [bivariate](https://en.wikipedia.org/wiki/Bivariate_analysis) and [univariate](https://en.wikipedia.org/wiki/Univariate) graphs.

- attribute 'kind'可設定圖表的類型, 例: 'kde', 'reg', 'hist'

```python
sns.jointplot(data=df_poverty_vs_hs, x="percent_completed_hs",
              y="poverty_rate", marginal_kws=dict(bins=20))
plt.xlabel("High School Graduation Rate")
plt.ylabel("Poverty Rate")
plt.show()
```

#### regplot

- [seaborn.regplot](https://seaborn.pydata.org/generated/seaborn.regplot.html)

```python
sns.regplot(data=df_poverty_vs_hs, x="percent_completed_hs",
              y="poverty_rate")
plt.xlabel("High School Graduation Rate")
plt.ylabel("Poverty Rate")
plt.show()
```

#### kdeplot

- [seaborn.kdeplot](https://seaborn.pydata.org/generated/seaborn.kdeplot.html)

```python
sns.kdeplot(data=df_fatalities, x="age")
plt.title("Age Distribution")
plt.xlabel("Age")
plt.show()
```

#### FacetGrid

- [seaborn.FacetGrid](https://seaborn.pydata.org/generated/seaborn.FacetGrid.html)

```python
races = df_fatalities.race.value_counts().index
# row: 每列圖表的資料分類根據
# col: 每行圖表的資料分類根據
figs = sns.FacetGrid(df_fatalities, row="race", row_order=races,
                     height=2, aspect=3)
figs.map(sns.kdeplot, "age")
plt.xlabel("Age")
plt.xlim(0,100)
plt.show()
```

畫出來的樣子:

![fig1](/assets/img/posts/2022-10/fig2.png){: style="max-width: 50%" .normal}