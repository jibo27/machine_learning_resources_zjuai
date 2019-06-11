URL: arXiv:1904.11041

## TL;DR
即将开始做local attention相关的工作，来学习一下。这篇文章使用human mask来监督ReID人体attention的学习，且除了整个人体whole attention的学习，还有进一步地分出上躯干和下躯干两个部分来学习local part attention。

## Model
![1559534999949](C:\Users\shaon\AppData\Roaming\Typora\typora-user-images\1559534999949.png)
这个模型结构如上，在ResNet50 backbone上增加两个attention模块，第一个attention模块用来过滤背景，且存在一个average pooling进行down sample以和backbone的输出相匹配；第二个attention模块存在三个local attention，分别是整个人体、上躯干和下躯干的attention，且由于last stride为1，因此不需要averge pooling。attention和feature均采用element wise的乘积。 

![1559535032811](C:\Users\shaon\AppData\Roaming\Typora\typora-user-images\1559535032811.png)

Attention模块分为spatial attention和channel attention， spatial attention模块由三个conv 1x1组成，输出channel数为输入channel数的s分之一，最后输出的spatial attention大小为（h，w，1）。channel attention模块就是一个SE模块，输出大小为（1，1，c）。最后两个attention采用element wise的乘积得到一个（h，w，c）的attention map，再和backbone的feature map进行element wise乘积得到attention加权的feature map，可以让网络过滤背景干扰、更加关注人体。 

![1559535053108](C:\Users\shaon\AppData\Roaming\Typora\typora-user-images\1559535053108.png)

Loss上除了有几个模块的ID loss和Triplet Loss之外，还有一个mask guided attention loss，它是学习得到的attention map和gt的mask的RMSE。

## Thoughts
思路其实挺清晰的，也挺容易理解，和很多human parsing的方法差不多。不过我有一个直觉上不太同意的地方，就是最后的attention map是channel attention和spatial attention的element wise乘积，我觉得这单纯的乘积不太好，我直觉上感觉先乘spatial attention过一次非线性再乘channel attention过一次非线性，使得两个attention分离开而不是耦合在一起会具有更好的attention作用。SE模块的channel attention是已经证明涨点了的，spatial attention也至少应该能维持在加了SE的提点水平。