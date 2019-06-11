## 论文题目
Accurate Face Detection for High Performance [[PDF]](https://arxiv.org/pdf/1905.01585.pdf)


## 简介

&emsp;&emsp;本文以RetinaNet为backbone提出了一种Face Detection的新框架，并在其中使用了许多Tricks，和SOTA模块，在WIDER FACE 数据集上取的很好的效果。

## 主要贡献

1. 将IoU Regression Loss 集成至回归分支，更好的指导优化参数。
2. 将STN中 selective Two-step classification &  selective Two-step regression 加入网络
3. Data Anchor Sampling
4. Max-Out Label
5. Multi-Scale Testing



## 总结
&emsp;&emsp;本文基本没有什么创新，就是把各种其他网络SOTA技巧集成到了一起，获得了SOTA结果。
