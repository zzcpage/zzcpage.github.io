---
title: TensorFlow2笔记-NN(全连接)
categories: 
    - 深度学习
    - TensorFlow2
tags: 
    - TensorFlow2
    - CNN
---
### 介绍

全连接NN：每个神经元与前后相邻层的每一个神经元都有连接关系，输入是特征，输出为预测的结果。
（可以类比于满射，前后层神经元之间都有联系）

>参数个数

参数个数=$\displaystyle \sum^{}_{各层}{前层*后层(w)+后层(b)}$

