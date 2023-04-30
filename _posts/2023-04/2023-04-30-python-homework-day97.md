---
layout: post
title: "Homework: Custom Automation"
date: 2023-04-30 12:47 +0800
description:
image:
category: [Notes]
tags: [python, pandas, jupyter notebook]
author: cloverwhale
---

100 Days of Code的作業: [Custom Automation](https://github.com/cloverwhale/day97-Custom-Automation) 的紀錄.



## Pandas drop multiple columns

> 使用下面的方法在column names有重複的時候會無法正確運作, 必須重新命名.
{: .prompt-tip }

```python
cols = [2, 3, 5, 6, 7, 8, 9, 12, 13, 14, 15, 16, 17]
df.drop(df.columns[cols], axis=1, inplace=True)
```


## 在Jupyter Notebook中並排顯示表格

- [Table Visualization — pandas 2.0.1 documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/style.html)
- ```df_foreign.style```為```<class 'pandas.io.formats.style.Styler'>```
- ```.set_table_attributes("style='display:inline'")```設定table style

```python
df_top_styler = df_foreign.head(20).style \
    .hide(axis='index') \
    .set_table_attributes("style='display:inline'") \
    .set_caption('買超排行')
df_bottom_styler = df_foreign.tail(20).sort_values(by='外陸資買賣超股數(不含外資自營商)').style \
    .hide(axis='index') \
    .set_table_attributes("style='display:inline'") \
    .set_caption('賣超排行')
```

- 輸出html後利用IPythond的display_html顯示

```python
from IPython.display import display_html
display_html(df_top_styler._repr_html_() + df_bottom_styler._repr_html_(), raw=True)
```

## 在Jupyter Notebook markdown中顯示變數

- 寫在code block中, 執行時會產生markdown

```python
from IPython.display import Markdown as md

#...
md("# {} 三大法人買賣超排行".format(date_string_f))
```

### 其他參考頁面
- [Jupyter notebook display two pandas tables side by side](https://stackoverflow.com/questions/38783027/jupyter-notebook-display-two-pandas-tables-side-by-side/44923103)
- [Print Variable In Jupyter Notebook Markdown Cell Python](https://stackoverflow.com/questions/52812231/print-variable-in-jupyter-notebook-markdown-cell-python)
