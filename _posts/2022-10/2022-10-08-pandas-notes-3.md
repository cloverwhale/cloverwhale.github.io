---
layout: post
title: "Homework: Determinants of Earnings"
date: 2022-10-08 20:45 +0800
description:
image:
category:
tags: [python, pandas, jupyter notebook, pyplot, seaborn, sklearn]
author: cloverwhale
---

紀錄100 Days of Code的作業: [Determinats of Earnings](https://github.com/cloverwhale/day100-Predict-Earnings)時遇到的一些問題和用到的methods.


### matplotlib.pyplot

`import matplotlib.pyplot as plt`

#### scatter plot

[matplotlib.pyplot.scatter — Matplotlib 3.5.1 documentation](https://matplotlib.org/3.5.1/api/_as_gen/matplotlib.pyplot.scatter.html)

```python
plt.scatter(x=y_train, y=predict_values_s, alpha=0.3)
plt.plot(y_train, y_train, color='C1')
plt.title(f'Actual vs Predicted Earnings: $y _i$ vs $\hat y_i$ (R-Squared {rsquared_s:.2})', fontsize=17)
plt.xlabel('Actual Earnings $y _i$', fontsize=14)
plt.ylabel('Prediced Earnings $\hat y _i$', fontsize=14)
plt.show()
```
- color: 定義圖的顏色
- alpha: 點的透明度
- `plt.plot` in ln2: 疊上另一線圖

#### display mathmetical expressions

[Writing mathematical expressions — Matplotlib 3.6.0 documentation](https://matplotlib.org/stable/tutorials/text/mathtext.html)

### Seaborn

`import seaborn as sns`

#### histplot

[seaborn.displot — seaborn 0.12.0 documentation](https://seaborn.pydata.org/generated/seaborn.displot.html)

```python
y_log = np.log(df_clean['EARNINGS'])
sns.displot(y_log, kde=True, color='C7')
plt.title(f'Log Earnings. Skew is {y_log.skew():.3}')
plt.show()
```

- `kde=True`: Add KDE curve

#### pairplot

[seaborn.pairplot — seaborn 0.12.0 documentation](https://seaborn.pydata.org/generated/seaborn.pairplot.html)

```python
sns.pairplot(df_clean, x_vars=["ASVABAR", "ASVABWK", "ASVABPC"], y_vars=["EARNINGS"],
             height=4, aspect=.8, kind="reg", plot_kws={'line_kws': {'color': 'C3'}, 'scatter_kws': {'alpha': 0.3}})
```

- 其實可以畫出多對多的關係圖, 但我只想並排每個變數對上earnings, 所以```y_vars```只有一個.
- `plot_kws`: 設定圖表的參數. 我目前只用到改顏色

### SKlearn - LinearRegression

[sklearn.linear_model.LinearRegression — scikit-learn 1.1.2 documentation](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html)

Imports:

```python
sklearn.linear_model import LinearRegression
sklearn.model_selection import train_test_split
```

#### Split data

Fit data前需要先split data. 使用一部分的data做training, 另一部分做testing.

```python
X_train, X_test, y_train, y_test = train_test_split(df_clean, df_clean['EARNINGS'], test_size=0.2, random_state=10)
```

- 前兩個args為data and target data. (我好像應該要把'EARNINGS'從第一個dataframe中拿掉?)
- `test_size`: 設定test data的比例0.0~1.0, default為0.25
- `random_state`: See [Glossary of Common Terms and API Elements — scikit-learn 1.1.2 documentation](https://scikit-learn.org/stable/glossary.html#term-random_state) 

#### .fit

> Fit linear model.

```python
reg_s = LinearRegression()
reg_s.fit(X_train_S, y_train)
```

- `fit(X_train_S, y_train)`: X_train_S為training data, y_train為target values
- 需要注意`len(X_train_S)`和`len(y_train)`需要相符
- Data如果不是2d array, 則需要reshape, for example: `X_train_S = np.array(X_train['S']).reshape(-1,1)`

#### attributes: intercept_ and coef_ 

Linear model的長相:
> ŷ(ω,x) = ω<sub>0</sub> + ω<sub>1</sub>x<sub>2</sub> + ... + ω<sub>p</sub>x<sub>p</sub>

- intercept_: ω<sub>0</sub>
- coef_: ω = (ω<sub>1</sub>, ..., ω<sub>p</sub>)

#### .score (R-Squared value)

> Return the coefficient of determination of the prediction. The best possible score is 1.0 and it could be negative.

```python
rsquared_s = reg_s.score(X_test_S, y_test)
```

#### .predict

用fit好的linear model預測數值

```python
predict_values_s = reg_s.predict(X_train_S)
```

接著求residuals. 用來分析linear model是否有bias.
`residuals_s = (y_train - predict_values_s)`

畫出residuals和predicted values的scatter plot. 越接近normal distribution越好.(可用distplot, skew and mean分析)
```python
plt.scatter(x=predict_values_s, y=residuals_s, c='C2', alpha=0.3)
plt.title('Residuals vs Fitted Values for Years of Schooling', fontsize=17)
plt.xlabel('Predicted Earnings $\hat y _i$', fontsize=14)
plt.ylabel('Residuals', fontsize=14)
plt.show()
```
