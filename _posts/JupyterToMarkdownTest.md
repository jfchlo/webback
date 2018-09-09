---
layout: 
title: JupyterToMarkdownTest
date: 2018-09-10 06:20:51
tags: jupyter markdown
categories: 数据分析

---
![](/photos/JupyterToMarkdownTest/output_14_0.png)
> 这是以前用jupyter，今日转换成md放到这里作为一个测试
  销售描述统计与 matplotlib

 - pandas 数据重采样
 - pandas 数据透视表
 - matplotlib 中文及符号显示
 - 基本画图

  以一月份份的销售为例：

```python
import os
import pandas as pd
import matplotlib.pyplot as plt
name=['单号','销售日期','商品编码','商品名称','车型','仓位','销售数量','销售单价','销售金额']
os.chdir('e:')
df1=pd.read_excel('./zyxiaoshou.xlsx',sheetname='Sheet5',header=0,names=name)

```

```python
df1.head()
```

<div>
<table border="1" class="dataframe"><thead><tr style="text-align: right;"><th></th><th>单号</th><th>销售日期</th><th>商品编码</th><th>商品名称</th><th>车型</th><th>仓位</th><th>销售数量</th><th>销售单价</th><th>销售金额</th></tr></thead><tbody><tr><th>0</th><td>SEL1605310001</td><td>2016-05-31</td><td>0501120615-003</td><td>车门铰链</td><td>NJP</td><td>AA</td><td>1</td><td>12.0</td><td>12.0</td></tr><tr><th>1</th><td>SEL1605310001</td><td>2016-05-31</td><td>0501120708-001</td><td>锁体</td><td>NJP</td><td>AA</td><td>1</td><td>16.0</td><td>16.0</td></tr><tr><th>2</th><td>SEL1605310002</td><td>2016-05-31</td><td>0303100406-001</td><td>后制动底板</td><td>FT</td><td>AB</td><td>1</td><td>90.0</td><td>90.0</td></tr><tr><th>3</th><td>SEL1605310003</td><td>2016-05-31</td><td>0601013409-001</td><td>排气制动阀</td><td>CDW</td><td>王牌库</td><td>1</td><td>87.0</td><td>87.0</td></tr><tr><th>4</th><td>SEL1605310003</td><td>2016-05-31</td><td>0601060304-014</td><td>后轮毂</td><td>CDW</td><td>王牌库</td><td>1</td><td>218.8</td><td>218.8</td></tr></tbody></table></div>

这是一月份总体销售情况，来做个初步的统计描述,总销售金额，各个仓位的销售金额(包含了销售退单）
看看pandas的透视表函授怎么实现上述功能 pd.pivot_table

```python
sel_table=df1.pivot_table(index='仓位',values=['销售金额'],aggfunc=sum,margins_name='销售总计',margins=True)
sel_table
```

<div>
<table border="1" class="dataframe"><thead><tr style="text-align: right;"><th></th><th>销售金额</th></tr><tr><th>仓位</th><th></th></tr></thead><tbody><tr><th>AA</th><td>119338.44</td></tr><tr><th>AB</th><td>59614.94</td></tr><tr><th>AF</th><td>-2224.00</td></tr>
<tr><th>王牌库</th><td>124863.98</td></tr><tr><th>福田库</th><td>168834.13</td></tr><tr><th>轮胎库</th><td>35870.00</td></tr><tr><th>销售总计</th><td>506297.49</td></tr></tbody></table></div>


```python
sel_table=df1.pivot_table(index='商品名称',values=['销售数量','销售金额'],aggfunc=sum)
sel_table.sort_values(by='销售金额',ascending=False)[0:10]
```

<div><table border="1" class="dataframe"><thead><tr style="text-align: right;"><th></th><th>销售数量</th><th>销售金额</th></tr><tr><th>商品名称</th><th></th><th></th></tr></thead><tbody><tr><th>发动机总成</th><td>5</td><td>51328.70</td></tr><tr><th>驾驶室空壳</th><td>5</td><td>44360.00</td></tr><tr><th>轮胎</th><td>203</td><td>35646.00</td></tr><tr><th>保险杠</th><td>87</td><td>15155.11</td></tr><tr><th>齿轮油</th><td>273</td><td>14013.00</td></tr><tr><th>驾驶室总成</th><td>1</td><td>13000.00</td></tr><tr><th>后视镜</th><td>777</td><td>12639.10</td></tr><tr><th>前大灯</th><td>105</td><td>12065.88</td></tr><tr><th>散热器总成</th><td>26</td><td>11505.91</td></tr><tr><th>柴油机油</th><td>143</td><td>10672.90</td></tr></tbody></table></div>


销售数据在一个工作薄中8个不同滴表中，看否能合并为一个

```python
import os
import pandas as pd
import matplotlib.pyplot as plt
name=['单号','销售日期','商品编码','商品名称','车型','仓位','销售数量','销售单价','销售金额']
os.chdir('e:')
df=[]
for i in range(1,13):
   df.append(pd.read_excel('./zyxiaoshou.xlsx',sheetname='Sheet'+str(i),header=0,names=name))
data=pd.concat(df)
data.index=[data['销售日期']]
```

```python
data.tail()
```
<div><table border="1" class="dataframe"><thead><tr style="text-align: right;"><th></th><th>单号</th><th>销售日期</th><th>商品编码</th><th>商品名称</th><th>车型</th><th>仓位</th><th>销售数量</th><th>销售单价</th></tr><tr><th>销售日期</th><th></th><th></th><th></th><th></th><th</th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><th>2016-12-01</th><td>SEL1612010026</td><td>2016-12-01</td><td>03021613-112</td><td>散热器总成</td><td>FT</td><td>福田库</td><td>1</td><td>575.09</td><td>575.09</td></tr><tr><th>2016-12-01</th><td>SEL1612010027</td><td>2016-12-01</td><td>0302121201-022</td><td>挡泥板</td><td>FT</td><td>福田库</td><td>2</td><td>90.00</td><td>180.00</td></tr><tr><th>2016-12-01</th><td>SEL1612010027</td><td>2016-12-01</td><td>0302121201-023</td><td>挡泥板</td><td>FT</td><td>福田库</td><td>1</td><td>90.00</td><td>90.00</td></tr><tr><th>2016-12-01</th><td>SEL1612010027</td><td>2016-12-01</td><td>0302121205-046</td><td>轮罩</td><td>FT</td><td>福田库</td><td>1</td><td>52.00</td><td>52.00</td></tr><tr><th>2016-12-01</th><td>SEL1612010027</td><td>2016-12-01</td><td>0302121205-047</td><td>轮罩</td><td>FT</td><td>AB</td><td>1</td><td>52.00</td><td>52.00</td></tr></tbody></table></div>


```python
len(data)
```

    33932

```python
from datetime import datetime
```

```python
sel_table=data.pivot_table(index='销售日期',columns='仓位',values='销售金额',aggfunc=sum,fill_value=0)
sel_table.head()
```

<div><table border="1" class="dataframe"><thead><tr style="text-align: right;"><th>仓位</th><th>AA</th><th>AB</th><th>AC</th><th>AF</th><th>BA</th><th>RR</th><th>Z</th><th>整车</th><th>王牌库</th><th>王牌旧件库</th><th>福田库</th><th>福田旧件库</th><th>轮胎库</th></tr><tr><th>销售日期</th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><th>2016-01-02</th><td>2665.5</td><td>2157.00</td><td>0</td><td>-25.0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1340.00</td><td>0</td><td>8472.89</td><td>0.0</td><td>0.0</td></tr><tr><th>2016-01-03</th><td>4155.0</td><td>2835.72</td><td>0</td><td>-259.5</td><td>0</td><td>0</td><td>0</td><td>0</td><td>7509.70</td><td>0</td><td>3874.55</td><td>0.0</td><td>0.0</td></tr><tr><th>2016-01-04</th><td>26055.0</td><td>2803.56</td><td>0</td><td>-80.0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3851.48</td><td>0</td><td>5751.85</td><td>0.0</td><td>0.0</td></tr><tr><th>2016-01-05</th><td>11263.0</td><td>6323.11</td><td>0</td><td>0.0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1978.50</td><td>0</td><td>3278.50</td><td>0.0</td><td>0.0</td></tr><tr><th>2016-01-06</th><td>1223.0</td><td>1529.00</td><td>0</td><td>0.0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1081.80</td><td>0</td><td>3100.43</td><td>0.0</td><td>0.0</td></tr></tbody></table></div>

```python
#每月各个仓库的销售汇总
sel_table.resample('m').sum()
```

<div><table border="1" class="dataframe"><thead><tr style="text-align: right;"><th>仓位</th><th>AA</th><th>AB</th><th>AC</th><th>AF</th><th>BA</th><th>RR</th><th>Z</th><th>整车</th><th>王牌库</th><th>王牌旧件库</th><th>福田库</th><th>福田旧件库</th><th>轮胎库</th></tr><tr><th>销售日期</th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><th>2016-01-31</th><td>127682.80</td><td>92014.75</td><td>0</td><td>-2761.30</td><td>0</td><td>0</td><td>-200</td><td>0</td><td>124957.82</td><td>-65</td><td>125930.35</td><td>0.00</td><td>0.00</td></tr><tr><th>2016-02-29</th><td>62677.00</td><td>36036.51</td><td>0</td><td>-741.50</td><td>0</td><td>0</td><td>0</td><td>0</td><td>51051.23</td><td>0</td><td>116428.23</td><td>0.00</td><td>0.00</td></tr><tr><th>2016-03-31</th><td>258884.70</td><td>127815.22</td><td>0</td><td>-4070.72</td><td>74</td><td>0</td><td>0</td><td>0</td><td>209483.66</td><td>0</td><td>301219.34</td><td>0.00</td><td>0.00</td></tr><tr><th>2016-04-30</th><td>136980.02</td><td>54074.05</td><td>0</td><td>-1972.50</td><td>0</td><td>0</td><td>0</td><td>0</td><td>182166.40</td><td>0</td><td>187852.54</td><td>0.00</td><td>27648.00</td></tr><tr><th>2016-05-31</th><td>119338.44</td><td>59614.94</td><td>0</td><td>-2224.00</td><td>0</td><td>0</td><td>0</td><td>0</td><td>124863.98</td><td>0</td><td>168834.13</td><td>0.00</td><td>35870.00</td></tr><tr><th>2016-06-30</th><td>94775.62</td><td>95951.11</td><td>0</td><td>-2370.54</td><td>0</td><td>4154000</td><td>0</td><td>91800</td><td>125914.73</td><td>0</td><td>189296.92</td><td>0.00</td><td>22858.00</td></tr><tr><th>2016-07-31</th><td>90293.42</td><td>66642.80</td><td>-70</td><td>-2625.00</td><td>0</td><td>1124660</td><td>0</td><td>0</td><td>90984.31</td><td>0</td><td>110750.86</td><td>-914.03</td><td>34125.00</td></tr><tr><th>2016-08-31</th><td>80870.88</td><td>43727.09</td><td>0</td><td>-1636.00</td><td>0</td><td>171048</td><td>0</td><td>255800</td><td>63678.54</td><td>0</td><td>92951.81</td><td>-345.00</td><td>41848.00</td></tr><tr><th>2016-09-30</th><td>114232.58</td><td>48921.98</td><td>0</td><td>-3032.00</td><td>0</td><td>0</td><td>0</td><td>264300</td><td>94730.09</td><td>0</td><td>105888.18</td><td>-16.00</td><td>36781.90</td></tr><tr><th>2016-10-31</th><td>96081.60</td><td>58120.44</td><td>0</td><td>-3359.50</td><td>0</td><td>0</td><td>0</td><td>180000</td><td>64260.25</td><td>0</td><td>88486.43</td><td>-285.08</td><td>25395.16</td></tr><tr><th>2016-11-30</th><td>99597.10</td><td>34259.78</td><td>0</td><td>-2770.50</td><td>0</td><td>0</td><td>0</td><td>268197</td><td>50649.67</td><td>0</td><td>99196.55</td><td>52674.37</td></tr><tr><th>2016-12-31</th><td>99297.45</td><td>53077.18</td><td>0</td><td>-534.50</td><td>0</td><td>0</td><td>98000</td><td>49910.29</td><td>0</td><td>113013.72</td><td>0.00</td><td>22431.87</td></tr></tbody></table></div>

```python
t1=sel_table.resample('m').sum().ix[:,['福田库','王牌库','AA','AB','整车','轮胎库']]

plt.rcParams['font.sans-serif']=['SimHei']
plt.rcParams['axes.unicode_minus']=False

t1.plot(figsize=(10,3),rot=30)
plt.legend()
plt.grid(True)
plt.xlabel('月份')
plt.ylabel('销售金额')
plt.title('2016年1-8月份分库销售统计')
plt.show()

```

![png](/photos/JupyterToMarkdownTest/output_12_0.png)



```python
t2=t1.resample('m').sum().sum(axis=1)
t2.plot(figsize=(10,3),rot=30)

plt.grid(True)
plt.ylabel('销售金额(万元)')
plt.xlabel('月份')
plt.show()

```


![](/photos/JupyterToMarkdownTest/output_13_0.png)



```python
t1.sum(axis=1).plot(figsize=(10,3),rot=45,style='bo-')
plt.grid(True)
plt.title('总体销售')
plt.ylabel('销售金额')
plt.xlabel('销售月份')
for i in range(len(t1)):
    plt.annotate(t1.sum(axis=1).values[i],
                 xy=(t1.index[i],t1.sum(axis=1)[i]),
                 xytext=(t1.index[i],t1.sum(axis=1)[i])
                 )
   
plt.show()
```

![](/photos/JupyterToMarkdownTest/output_14_0.png)

```python
import numpy as np
a=pd.Series([1,2,3])
a[1]
```

    2

```python
t1.plot(subplots=True,figsize=(6,10),sharey=True,grid=True)
plt.show()
```


![](/photos/JupyterToMarkdownTest/output_16_0.png)



```python
t1.plot(subplots=True,figsize=(14,6),layout=(2,3),sharey=True,sharex=False,grid=True)
plt.show()
```

![](/photos/JupyterToMarkdownTest/output_17_0.png)

```python
t1.sum(axis=0).plot.pie(figsize=(6,6),autopct='%.2f')
plt.show()
```
![png](/photos/JupyterToMarkdownTest/output_18_0.png)

```python
t1.plot.area(figsize=(12,6))
plt.show()
```
![](/photos/JupyterToMarkdownTest/output_19_0.png)

```python
import os
import pandas as pd
import matplotlib.pyplot as plt
name=['单号','销售日期','商品编码','商品名称','车型','仓位','销售数量','销售单价','销售金额']
os.chdir('e:')
df=[]
for i in range(1,12):
   df.append(pd.read_excel('./zyxiaoshou.xlsx',sheetname='Sheet'+str(i),header=0,names=name))
data=pd.concat(df)
data.to_excel('./zyqp.xls')
```
