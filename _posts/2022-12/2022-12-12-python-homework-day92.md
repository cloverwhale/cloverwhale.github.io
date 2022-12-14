---
layout: post
title: "Homework: Custom Web Scrapper"
date: 2022-12-12 22:57 +0800
description:
image:
category: [Notes]
tags: [python, beautifulsoup, argparse, webscrapping]
author: cloverwhale
---

紀錄100 Days of Code的作業: [Custom Web Scrapper](https://github.com/cloverwhale/day92-Custom-Web-Scrapper)時遇到的一些問題和用到的methods.

## BeautifulSoup

### Import and Create Object

Create BeautifulSoup object

```python
from bs4 import BeautifulSoup as bs

soup = bs(search_result.text, "html.parser")
```

- [其他的parser資訊](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-a-parser)


### Get tags

- [find_all()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find-all): 回傳相符的tag的list, 下面的例子放tag name, 和css class, 所以是要找所有符合的`<a class="mntl-card-list-items"...>`; 若要只回傳相符的tag則使用[find()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find)

```python
soup.find_all("a", class_="mntl-card-list-items")
```

- [select()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#css-selectors): 使用css selector

```python
soup.select("p#article-subheading_2-0")
```


### Get attribute value from a tag

例如要拿`<a>`裡面的url

```python
link = soup.find("a")
link.get("href")
```

### Remove tags

- [Tag.decompose()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#decompose): 移除原來物件中的某個tag

```python
button = soup.select("button")
button.decompose()
```

- [PageElement.extrac()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#extract): 移除tree中的tag或String, 並回傳移除的值

```python
soup.a.extract() # remove tag <a>
```


## Argparse

[Argparse Tutorial](https://docs.python.org/zh-tw/3/howto/argparse.html)

### Import and Create Object

```python
import argparse

parser = argparse.ArgumentParser(description="Search recipes by given a keyword")
```

### 設定可用的參數

```python
parser.add_argument("-s", "--search", type=str, help="search key word", default=DEFAULT_SEARCH_KEYWORD)
parser.add_argument("-n", "--number", type=int, help="number of recipes to save", default=DATA_COUNT)
```

- type, 指定data type, 若輸入不符合的類型會報錯
- help, 使用-h, -help時會出現的說明文字
- default, 沒有輸入時的預設值

### Get arugment values

```python
args = parser.parse_args() # Get all argument values

search_phrase=args.search # get -s/-search value
number=args.number
```

- 拿argument value時是指定`search`而不是`s`
- 作業中我只使用optional arguments, 如果是postional argument: `parser.add_argument("square", help="help text", type=int)`



## Etc

- 每次網站改版就要重寫一遍? (作業已經重寫一次T_T)
- 如何快速的完成改版?
