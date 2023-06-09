---
layout: post
title: 'Coggle 30 Days of ML：图像检索与匹配'
subtitle: '2023.04'
date: 2023-04-12
categories: 计算机视觉
cover: 'https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.kismetceyiz.com%2F2021%2F02%2F08%2Fhistogram-matching-with-opencv-scikit-image-and-python%2F&psig=AOvVaw3oEdPLEKKuDkRNPqmX0B8W&ust=1681431233409000&source=images&cd=vfe&ved=0CBEQjRxqFwoTCMC63MzJpf4CFQAAAAAdAAAAABAZ'
tags: 图像检索与匹配 图像特征提取 深度学习 
---

# Coggle 30 Days of ML <span style="float:right;">-2023.04</span></p>
随着互联网上的图像数量不断增长，图像检索和匹配计数已成为许多视觉搜索引擎的核心技术。图像检索和匹配时计算机视觉领域的重要研究方向之一，主要目的是从大规模的图像库中快速准确地找到与查询图像相似或相同的图像。

## 任务1：图像匹配与检索

### 相关知识

* **图像相似度**
图像相似度是度量图像之间相似程度的指标。  
基于像素级别相似度的计算方法：均方误差、结构相似性指数。
基于特征匹配相似度的计算方法：提取图像的特征，如颜色、纹理、形状等。
* **颜色直方图**
统计一幅图像中每个像素值出现的频率，绘制相应的直方图。
* **图像检索流程**
输入一张图像，在图像数据库中搜索与该图像最相似的图像。其主要流程为：图像特征提取、计算相似度、排序。

### 代码实现

```python
import cv2
import glob
import os, sys
import pandas as pd
import numpy as np
from sklearn.preprocessing import normalize

# 计算dataset文件夹中所有图的直方图
dataset_feat = []
for path in glob.glob('./dataset/*.jpg'):
    img = cv2.imread(path, 0)
    feat = cv2.calcHist(np.array([img]), [0], None, [256], [0, 256]).flatten()
    dataset_feat.append(feat)

# 进行归一化
dataset_feat = np.array(dataset_feat)
dataset_feat = normalize(dataset_feat)

# 计算query文件夹中所有图像的直方图
query_feat = []
for path in glob.glob('./query/*.jpg'):
    img = cv2.imread(path, 0)
    feat = cv2.calcHist(np.array([img]), [0], None, [256], [0, 256]).flatten()
    query_feat.append(feat)

# 进行归一化
query_feat = np.array(query_feat)
query_feat = normalize(query_feat)

# 计算每张query图片与dataset图片的颜色直方图相似度
dis = np.dot(query_feat, dataset_feat.T)
dataset_path = np.array(glob.glob('./dataset/*.jpg'))

# 生成提交结果
pd.DataFrame({
    'source': [x.split('/')[-1] for x in dataset_path[dis.argmax(1)]],
    'query': [x.split('/')[-1] for x in glob.glob('./query/*.jpg')]
}).to_csv('submit.csv', index=None)
```

### 总结
以下为此次任务中遇到的一些问题，参考了ChatGPT生成的解释。
* **glob**
`在Python中，glob模块提供了一种在指定目录中查找符合某个模式的所有文件的方法。它可以用于检索与指定模式匹配的文件路径列表。以下为glob模块的简单用法：`
```python
import glob

# 搜索当前目录下所有以 .txt 结尾的文件
file_list = glob.glob('*.txt')

# 搜索指定目录下所有以 .jpg 结尾的文件
file_list = glob.glob('/path/to/directory/*.jpg')
```

