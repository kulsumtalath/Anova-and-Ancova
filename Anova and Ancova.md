```python
import pandas as pd
```


```python
dataset=pd.read_excel("ANCOVA1.xlsx",sheet_name=0)
```


```python
dataset.head()
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
      <th>Store Number</th>
      <th>Sales</th>
      <th>Promotion</th>
      <th>Coupon</th>
      <th>ClietelRatings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>10</td>
      <td>1</td>
      <td>1</td>
      <td>9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>9</td>
      <td>1</td>
      <td>1</td>
      <td>10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>10</td>
      <td>1</td>
      <td>1</td>
      <td>8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>9</td>
      <td>1</td>
      <td>1</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>




```python
#one way anova :one dependent and one ind var
#sales =dep vae,promotion -ind
```


```python
import statsmodels.api as sm
```


```python
from statsmodels.formula.api import ols
```


```python
model=ols('Sales~C(Promotion)',dataset).fit()#c categorical var
oneway=sm.stats.anova_lm(model,type=2)
print(oneway)
```

                    df      sum_sq    mean_sq          F    PR(>F)
    C(Promotion)   2.0  106.066667  53.033333  17.943609  0.000011
    Residual      27.0   79.800000   2.955556        NaN       NaN
    

result
btw variation =106.06
within variation = 79.8
degree of freedom = 2,27
calc val = 17.943
p>0.05 = promotion = ha accepted



```python
#two way anova , one dep , 2 ind var
model=ols('Sales~C(Promotion)+C(Coupon)',dataset).fit()#c categorical var
twoway=sm.stats.anova_lm(model,type=2)
print(twoway)
```

                    df      sum_sq    mean_sq          F        PR(>F)
    C(Promotion)   2.0  106.066667  53.033333  52.098237  8.032739e-10
    C(Coupon)      1.0   53.333333  53.333333  52.392947  1.095036e-07
    Residual      26.0   26.466667   1.017949        NaN           NaN
    


```python
#promotion and coupon are <0.05
```

# ANCOVA


```python
model=ols('Sales~C(Promotion)+C(Coupon)+C(ClietelRatings)',dataset).fit()#c categorical var
ancova=sm.stats.anova_lm(model,type=2)
print(ancova)
```

                         df      sum_sq    mean_sq          F        PR(>F)
    C(Promotion)        2.0  106.066667  53.033333  50.875000  5.605989e-09
    C(Coupon)           1.0   53.333333  53.333333  51.162791  3.592805e-07
    C(ClietelRatings)   4.0    3.533333   0.883333   0.847384  5.103673e-01
    Residual           22.0   22.933333   1.042424        NaN           NaN
    


```python
#func to find eta_sq
```


```python
def anova_table(oneway):
    oneway['eta_sq']=oneway[:-1]['sum_sq']/sum(oneway['sum_sq'])
    cols=['sum_sq','df','F','PR(>F)','eta_sq']
    oneway=oneway[cols]
    return oneway;
```


```python
anova_table(oneway)
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
      <th>sum_sq</th>
      <th>df</th>
      <th>F</th>
      <th>PR(&gt;F)</th>
      <th>eta_sq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>C(Promotion)</th>
      <td>106.066667</td>
      <td>2.0</td>
      <td>17.943609</td>
      <td>0.000011</td>
      <td>0.57066</td>
    </tr>
    <tr>
      <th>Residual</th>
      <td>79.800000</td>
      <td>27.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
anova_table(twoway)
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
      <th>sum_sq</th>
      <th>df</th>
      <th>F</th>
      <th>PR(&gt;F)</th>
      <th>eta_sq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>C(Promotion)</th>
      <td>106.066667</td>
      <td>2.0</td>
      <td>52.098237</td>
      <td>8.032739e-10</td>
      <td>0.570660</td>
    </tr>
    <tr>
      <th>C(Coupon)</th>
      <td>53.333333</td>
      <td>1.0</td>
      <td>52.392947</td>
      <td>1.095036e-07</td>
      <td>0.286944</td>
    </tr>
    <tr>
      <th>Residual</th>
      <td>26.466667</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
anova_table(ancova)
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
      <th>sum_sq</th>
      <th>df</th>
      <th>F</th>
      <th>PR(&gt;F)</th>
      <th>eta_sq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>C(Promotion)</th>
      <td>106.066667</td>
      <td>2.0</td>
      <td>50.875000</td>
      <td>5.605989e-09</td>
      <td>0.570660</td>
    </tr>
    <tr>
      <th>C(Coupon)</th>
      <td>53.333333</td>
      <td>1.0</td>
      <td>51.162791</td>
      <td>3.592805e-07</td>
      <td>0.286944</td>
    </tr>
    <tr>
      <th>C(ClietelRatings)</th>
      <td>3.533333</td>
      <td>4.0</td>
      <td>0.847384</td>
      <td>5.103673e-01</td>
      <td>0.019010</td>
    </tr>
    <tr>
      <th>Residual</th>
      <td>22.933333</td>
      <td>22.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
