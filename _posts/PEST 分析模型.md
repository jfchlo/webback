---
title: PEST分析方法（一）
date: 2018-09-19
categories: 数据分析
tag: 数据分析

---
![](/photos/pest/bj.jpg)
# PEST 分析模型  

   从事同城配送行业，感觉现在是王小二过年，心理慌啊，我还欠老马不少银子啊
   看看这线条，跌宕起伏，犹如过山车叫人心惊肉跳。
![心惊肉跳图](/photos/pest/2017-2018运输数据.png)

耳闻PEST是卜卦大湿，据传卜过的人都说好。

一日登门请教！

我：“大湿啊，你看看我这，未来可好？”
大湿低头端详片刻，悠悠道：“你这是季节性情感障碍综合征。”
我：“情感障碍？”
大湿：“对，你看啊，节假日，特别是大的传统节日，你心情好点，而且越来越差，如今即便是传统节日，你的心情也大不如以前了。”

我：“大湿啊，你看看《武汉现代物流十三五规划纲要》，里头好多关于发展物流的热词，我还统计了一下，提到物流205个，还有这个配送。。。。。。”
大湿抬头，轻蔑扫了我一眼，我立马收住激情。
“一个物流规划文件，它不关注物流，关注什么，难道是某宝离婚，东哥性侵。” 大湿厉声道。

我默然！
![大湿打脸图1](/photos/pest/物流规划热点词汇词云.png)
```
import jieba
import jieba.analyse 
from pyecharts import WordCloud
from collections import Counter
import string
txt=''
with open('e:/file/1.txt',mode='r',encoding='utf-8') as f:
    line=f.read()
    line=line.translate(string.punctuation)#删除标点及特殊字符
    line=line.splitlines()
    txt=''.join(line)
keywords=jieba.cut(txt,cut_all=False)
word=[]
for key in keywords:
    if len(key)>1:
        word.append(key)

count=Counter(word)
#print(count.most_common(50))
name,value=WordCloud.cast(count)
wordcloud = WordCloud(width=1000, height=400)
wordcloud.add("", name, value,word_size_range=[20, 100])
wordcloud
```

![大湿打脸图2](/photos/pest/热点词汇柱状图.png)
```
from pyecharts import Bar
name=count.most_common(30)
m,n=Bar.cast(name)
bar=Bar('热点词汇')
bar.add('武汉市现代物流十三五规划',m,n)
bar
```

```
import pandas as pd
from pyecharts import Line
import re
files='e:/file/门店拣货数量.xls'
df=pd.read_excel(files,index_col=None)
df1=df.loc[:,['生成时间','拣货门店','件数']]
df1=df1.dropna()

f=lambda x: int(re.sub('\D','',x))
df1['件数']=df1['件数'].apply(f)

p=df1.pivot_table(index='生成时间',values='件数',aggfunc=sum)
t=p.resample('M').sum()
t.values.T
line=Line()
line.add("2017年至今运输数量", t.index, t.values.T[0], is_smooth=True, mark_line=["max", "average"])
line
```
> PEST 为一种企业所处宏观环境分析模型，所谓PEST，即P是政治（Politics），E是经济（Economy），S是社会（Society），T是技术（Technology）. 这些是企业的外部环境，一般不受企业掌握，这些因素也被戏称为“pest（有害物）”PEST要求高级管理层具备相关的能力及素养。



+ 政治环境
	+ 政府政策
	+ 法律环境

+ 经济环境
	+ 社会经济结构
	+ 经济体制
	+ 宏观经济政策
	+ 当前经济状况
	+ 其他一般经济条件
	可以参考以下几点
	1. 利率
	2. 通货膨胀与人均就业
	3. 人均GDP的长远预期

+ 社会环境
	+ 人口社会流动性
	+ 消费心理
	+ 生活方式变化
	+ 文化传统
	+ 价值观
	
+ 技术环境