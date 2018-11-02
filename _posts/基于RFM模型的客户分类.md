---
title: K-Means基于RFM模型的客户分类
date: 2018-11-02 09:39:57
categories: 数据分析
tag: 数据分析
---
![](/photos/RFM/RFM.jpg)


> 看到过基于LRFMC模型的航空客户分类，试试对我司的客户进行分类。
 如何通过RFM模型，为用户分群，实现精细化运营
 RFM模型是一个被广泛使用的客户关系分析模型，主要以用户行为来区分客户，RFM分别是：
　　
  * R = Recency 最近一次消费
　　
  * F = Frequency 消费频率
  * M = Monetary 消费金额 
  * 第一步：先挑出来近1个月的复购用户。
  * 第二步：近1个月内复购用户的平均实付金额做纵轴。
  * 第三步：近1个月内复购用户的购买次做横轴，生成表格。
  * 第四步，你需要自己在这个表格上划红线。横着的红线,平均消费金额，竖着的红线，平均消费次数。
  ![](/photos/RFM/frm.jpg)
   
   
#### 识别价值客户
* R（消费时间间隔，最近消费时间距离观测点的时间间隔）  
* M（当然是消费总额了）
* F（消费频次），
* L（关系存续时长）
* C（平均折扣），这个C我没有

#### 数据清洗 
* 使用pandas 的dropna()函数去掉na值
* drop()函数去掉件数为0的值
* groupby 用法
* drop_duplicates(column，keep=)去掉重复值，用到keep两个参数first和last

    * first 保留第一次出现的值，得到第一次拣货的值
    * last  保留最后一次出现的值，得到最后一次拣货的值
* pandas 的to_period 用法，去掉日期后面的时间值，
* Series.dt.days 获得天数

#### 数据归一化
* 使用常见的标准差归一化数据
    * (x-x.mean(axis=0)/x.std(axis=0)


#### K-Means 基本使用


```python
#原始数据长相
import pandas as pd
data=pd.read_excel('e:/蒙捷物流/门店拣货数量.xls',index_column=None)
```

    WARNING *** file size (53730112) not 512 + multiple of sector size (512)
    


```python
#只保留有用字段
data1=data[['生成时间','拣货门店','件数']]
data1.info()
```
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 54879 entries, 0 to 54878
    Data columns (total 3 columns):
    生成时间    54877 non-null datetime64[ns]
    拣货门店    54877 non-null object
    件数      54877 non-null object
    dtypes: datetime64[ns](1), object(2)
    memory usage: 1.3+ MB
    
```python
data1.head()
```
![](/photos/RFM/1.png)
```python
data1.tail()
```
![](/photos/RFM/2.png)
```python
#处理NA和0值
data1=data1.dropna()
data1=data1.drop(data1[data1.件数=='0'].index)
#设置索引
# data1=data1.set_index('拣货门店')
data1.head()
```
![](/photos/RFM/3.png)

```python
#修改件数字段为int64,用于统计数量
import re
f=lambda x:int(re.sub('\D','',x))
data1.件数=data1.件数.map(f)
data1.info()
```
    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 54257 entries, 6 to 54876
    Data columns (total 3 columns):
    生成时间    54257 non-null datetime64[ns]
    拣货门店    54257 non-null object
    件数      54257 non-null int64
    dtypes: datetime64[ns](1), int64(1), object(1)
    memory usage: 1.7+ MB
    
```python
#各个门店总件数
M=data1.groupby('拣货门店').sum()['件数']
#各门店总计拣货次数
Times=data1.groupby('拣货门店').count()['件数']
#第一次拣货时间
First_time=data1.drop_duplicates('拣货门店',keep='first')
#最后一次拣货时间
Last_time=data1.drop_duplicates('拣货门店',keep='last')
#设置index,
First_time=First_time.set_index('拣货门店')['生成时间']
Last_time=Last_time.set_index('拣货门店')['生成时间']
#拼接
d=pd.concat([M,Times,First_time,Last_time],axis=1)
d.columns=['M','F','First_time','Last_time']
#设置观测点时间为2018-09-20日
end_time=pd.to_datetime('2018-09-20')
#客户存续时长，取天数
d['L']=(d.Last_time-First_time).dt.days
#删除L异常值
d=d.drop(d[d['L']<=0].index)
#拣货间隔R
d['R']=(end_time-d.Last_time).dt.days
#拣货频率
d['ZF']=d['L']/d['F']
```

```python
d1=data1.set_index('生成时间')
```

```python
d=d[['M','L','R','ZF']]
d.head()
```

![](/photos/RFM/4.png)

```python
#归一化
d1=d.apply(lambda x: (x-x.mean(axis=0))/x.std(axis=0))
```

```python
d1.head()
```
![](/photos/RFM/5.png)

```python
from sklearn.cluster import KMeans
kmode=KMeans(n_clusters=4,n_jobs=4,max_iter=1000)
kmode.fit(d1)
```
    KMeans(algorithm='auto', copy_x=True, init='k-means++', max_iter=1000,
        n_clusters=4, n_init=10, n_jobs=4, precompute_distances='auto',
        random_state=None, tol=0.0001, verbose=0)

```python
#聚类中心
kmode.cluster_centers_
```
    array([[-0.20778844, -1.13408314,  1.30125341, -0.49642668],
           [-0.24994031, -0.22291813,  0.01485693,  1.47776371],
           [ 0.07186423,  0.61106574, -0.5932065 , -0.4025806 ],
           [ 9.00211981,  0.41519412, -0.69211313, -0.83961275]])


```python
kmode.labels_
```
    array([2, 2, 2, ..., 0, 2, 0])

```python
c1=pd.Series(kmode.labels_)
c1.value_counts()
```
##### 可以看到 每一类 具体数量
    2    545
    0    250
    1    237
    3      8
    dtype: int64


```python
d['labels']=kmode.labels_
```


```python
d[d['labels']==3]
```
![](/photos/RFM/6.png)
![](/photos/RFM/7.png)