## 论文题目
Selective Refinement Network for High Performance Face Detection [[PDF]](https://arxiv.org/pdf/1809.02693.pdf)


## 简介

&emsp;&emsp;本文为了解决在人脸检测过程中，小人脸难以检测和Recall上升后precision降低的问题，提出了一种新型网络Selective refinement Network，类似于RefineDet，通过two-step classification and regression方式，来提高精度。

## 网络结构

![](https://pic3.zhimg.com/80/v2-92a7200c43ea2f98bbdbf84b489b534e_hd.jpg)

SRN在anchor-based人脸检测器中引入了two-step的cls + reg任务，有效地降低了人脸检测中的false positives，提升了人脸定位的精准度，并有利于小尺度人脸的检出；

## Selective Two-step Classification

1st-step通过预定义的objectness阈值过滤大部分易分负样本，减少2nd-step分类器所需处理的样本量； 使用这个操作的出发点为：anchor-based检测器为检出小尺度人脸，都会在特征金字塔的浅层feature map上做anchor的密集采样，小尺度人脸是保持高召回率了，却带来了海量false positives，进一步导致了正负样本数量不均衡；从这点上看，STC就是作用于浅层feature map上；

但作者也提到，没必要在所有检测分支上都使用2-step分类操作，因为P5、P6、P7等高层feature map上anchor的占比比较小（占总anchor数量的11.1%），正负样本数量不均衡的问题会比较轻，且这些层上的特征更利于检测大尺度人脸；但P2、P3、P4等浅层feature map不同，anchor数量特别多（占总anchor数量的88.9%），且特征的表达能力本身又比较弱，context信息也不够，语义信息又缺乏，因此在这些浅层feature map上使用2-step的分类操作，就会有很好的效果。

此外，SRN在两阶段中都引入了focal loss操作，可以更全面地利用所有样本，且STC中，两阶段的分类器共享了大部分参数（仅预测的分类分支参数不一样），因为二者任务相同：都是从 bg 中判别出前景的人脸区域；最终实验结果如SRN论文中的table 2，可知STC应用于浅层feature map上的性能优于应用于高层feature map上；

## Selective Two-step Regression

1-stage的目标检测算法一般仅对bbox做一次边框调整，作者认为不够，但cascade rcnn里又说了，盲目地迭代做multi-step的bbox reg，并不能进一步提升bbox的定位精准度，SRN论文中的table 4中证实了，在浅层feature map上操作两次bbox reg，精度竟然还下降了，作者认为原因有二：

1 三个浅层featue map本职工作是通过密集采样的anchor检测小尺度人脸，这些feature map上特征本身的表达能力也不够，因此要其进一步完成bbox回归，精准度就显得不够了；

2 训练阶段，若让浅层feature map也参与其不擅长的bbox回归任务中，有点没发挥其特长，导致模型过于关注其产生的reg loss，忽略了其本职工作的cls loss；

基于以上分析，SRN选择仅在高层P5、P6、P7上使用STR操作，出发点很简单：低层feature map专注于分类任务，高层feature map充分利用大尺度人脸的高语义信息，训练出更适于bbox精准定位的分支；这种divide-and-conquer策略让整个模型的效率非常之高；---- 那么整体上，STC、STR就是RefineDet中ARM、ODM的进一步深入探索，非常有利于小尺度人脸的检测，并过滤海量负样本anchors。

## Receptive Field Enhancement

现有人脸检测算法都使用正方形的卷积核尺度，如conv 3 x 3、1 x 1等，因而也会生成正方形的感受野区域，如果目标尺度长宽比并不是接近1：1，那么此类正方形感受野可能并不会带来最佳检测性能；

SRN中提出了RFE，为模型引入多样化的感受野信息，有助于SRN检出极端尺度、姿态下的人脸，RFE结构如图2，包含了Inception、ResNet思路，共4个分支 + 1个shotcut，有点类似上篇文章中的RFB。

![](https://pic1.zhimg.com/80/v2-e0dad9d2ec6f0c9847226d10faf85710_hd.jpg)


