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

![](/photos/JupyterToMarkdownTest/1.png)

这是一月份总体销售情况，来做个初步的统计描述,总销售金额，各个仓位的销售金额(包含了销售退单）
看看pandas的透视表函授怎么实现上述功能 pd.pivot_table

```python
sel_table=df1.pivot_table(index='仓位',values=['销售金额'],aggfunc=sum,margins_name='销售总计',margins=True)
sel_table
```

<div>
![](/photos/JupyterToMarkdownTest/2.png)


```python
sel_table=df1.pivot_table(index='商品名称',values=['销售数量','销售金额'],aggfunc=sum)
sel_table.sort_values(by='销售金额',ascending=False)[0:10]
```

![](/photos/JupyterToMarkdownTest/3.png)


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
![](/photos/JupyterToMarkdownTest/4.png)

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

![](/photos/JupyterToMarkdownTest/5.png)
```python
#每月各个仓库的销售汇总
sel_table.resample('m').sum()
```

![](/photos/JupyterToMarkdownTest/6.png)
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
