---
title: Pagerank 实现文本摘要
date: 2019-03-21 22:39:57
categories: 数据分析
tag: 数据分析
---
![](/photos/analysis/th.jpg)
## pagerank 实现文本自动摘要

### 一 分句

* 使用正则将文档按照标点符号或其它符号进行分句，成为列表形式。
    
### 二 分词，去掉停用词
* 使用jieba分词将列表中的每个句子分词，并去掉停用词。这一步，还有词的<B>向量化</B> 可使用sklearn中的CountVectorizer函数一并实现。
 
### 词的向量化和tf-idf

* TFIDF 是个什么鬼

>  TF-IDF是一种统计方法，用以评估一字词对于一个文件集或一个语料库中的其中一份文件的重要程度。字词的重要性随着它在文件中出现的次数成正比增加，但同时会随着它在语料库中出现的频率成反比下降。

>  TFIDF的主要思想是：如果某个词或短语在一篇文章中出现的频率TF高，并且在其他文章中很少出现，则认为此词或者短语具有很好的类别区分能力，适合用来分类。TFIDF实际上是：TF * IDF，TF词频(Term Frequency)，IDF逆向文件频率(Inverse Document Frequency)。

### pagerank
* 这个pagerank 可以由networkx包实现

> PageRank，网页排名，又称网页级别、Google左侧排名或佩奇排名，是一种由 根据网页之间相互的超链接计算的技术，而作为网页排名的要素之一，以Google公司创办人拉里·佩奇（Larry Page）之姓来命名

>Google把从A页面到B页面的链接解释为A页面给B页面投票，Google根据投票来源（甚至来源的来源，即链接到A页面的页面）和投票目标的等级来决定新的等级。简单的说，一个高等级的页面可以使其他低等级页面的等级提升。
    

### 实现代码


```python
import re
# import numpy as np
import networkx as nx
import jieba
from sklearn.feature_extraction.text import TfidfTransformer,CountVectorizer
txt=''
with open('./1.txt',encoding='utf-8') as f:
    for line in iter(f):
        txt+=''.join(line)
stop_word=['的','是','了','我','之','以','，','、','《','》','“','”']
def get_sentences(text):
    #分句
    s=re.split(r'[。,?,!,\n\n ,:]', text)
    sentences=[w for w in s if w!='' and len(w)>0 ]
    return  sentences

#得分topn的句子作为摘要
def textrank(sentences,n):
    sorted_sentences=[]
    
    #词向量化,使用jieba的cut方法
    bow_matrix=CountVectorizer(tokenizer=jieba.cut,stop_words=stop_word).fit_transform(sentences)
    normalized=TfidfTransformer().fit_transform(bow_matrix)

    sim_graph=normalized*normalized.T
    # print(sim_graph)
    
    #将稀疏矩阵转化化为图（networkx）
    nx_graph=nx.from_scipy_sparse_matrix(sim_graph)

    scores=nx.pagerank(nx_graph)
    # print(scores)
    
    #字典排序
    sorted_scores=sorted(scores.items(),key = lambda item: item[1], reverse=True)
    #print(sorted_scores)
    for index,score in sorted_scores:
        item={
            'index':index,
            'sentence':sentences[index],
            'weight':score
        }
        sorted_sentences.append(item)
    return sorted_sentences[:n]
```


```python
t=get_sentences(txt)
#print(textrank(t,5))
for i in textrank(t,5):
    print(i)
    print('\n')

```
<font size=2>
    {'index': 16, 'sentence': '本研究使用总和生育率、递进生育率（控制了年龄、孩次后的总和生育率）、内在生育率（控制了年龄、孩次、生育间隔后的总和生育率）和队列生育率（一个出生队列的女性，到某一年龄为止的累计生育率或平均生育子女数）这四种生育率指标，来估计和对比分析中国近年来的生育水平与变化趋势', 'weight': 0.020681738055241223}

    {'index': 58, 'sentence': '总之，类似于二孩递进总和生育率，二孩内在总和生育率的变化进一步表明近年来二孩总和生育率的大幅度上升是生育堆积效应的反映，但其对实际二孩生育水平的估计要高于二孩递进总和生育率', 'weight': 0.018873167326076482}

    {'index': 55, 'sentence': '通过对比二孩总和生育率、二孩递进总和生育率和二孩内在总和生育率，可以看出因近10年来生育间隔的变化，二孩递进总和生育率对二孩生育水平存在一定程度低估，而二孩总和生育率在2012年前低估二孩生育水平，之后又高估二孩生育水平', 'weight': 0.018784835700268225}
      
    {'index': 72, 'sentence': '综上，近年来一孩总和生育率的大幅度下降并不表明一孩生育水平的真实的有如此明显下降，而主要是反映了妇女婚育年龄推迟的进度效应，一孩总和生育率降到了0.6左右，而一孩内在总和生育率仍然高达0.9以上', 'weight': 0.018205850700340118}
    
    {'index': 33, 'sentence': '在2016和2017年一孩生育水平较低的情况下总和生育率仍有较大幅度回升，原因就在于二孩总和生育率提高产生的对总和生育率的提升效应，这也表明了全面两孩政策效应非常显著', 'weight': 0.018115375202097012}
    </font>
    
    
    

摘要原文档地址:  [中国家庭︱中国近10年的生育水平与趋势](https://pit.ifeng.com/c/7lDbdcv3qc4)
