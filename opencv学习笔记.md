# 学习中的小知识点 #
### 仿射变换（affine transformation）：###
	 刚体变换 /1.平移变换 将每一点移动到(x+tx, y+ty) 
	         \2.旋转变换
	          3.缩放变换
	          4.剪切变换

### 透视变换（perspective transformation）： ###
    透视原理

### 汉明码（海明码/Hamming Code) ###
    相当于一个数各个位上的多次奇偶校验，使得根据校验位便能得出数据出错的位数
    并加以纠正。数据位数D与汉明码位数P的数学关系为
                2^P >= P+D+1

### 汉明距离 ###
> 汉明距离是使用在数据传输差错控制编码里面的，汉明距离是一个概念，它表示两个（相同长度）字对应位不同的数量，我们以d（x,y）表示两个字x,y之间的汉明距离。对两个字符串进行异或运算，并统计结果为1的个数，那么这个数就是汉明距离。

### 特殊的 threshold type ###
    CV_THRESH_OSTU: ostu（大津算法或最大类间方差法）
> 利用阈值将原图像分成前景，背景两个图象。

> 前景：用n1,csum,m1来表示在当前阈值下的前景的点数，质量矩，平均灰度

> 后景：用n2, sum-csum,m2来表示在当前阈值下的背景的点数，质量矩，平均灰度

> 当取最佳阈值时，背景应该与前景差别最大，关键在于如何选择衡量差别的标准，而在otsu算法中这个衡量差别的标准就是最大类间方差（英文简称otsu，这也就是这个算法名字的来源），在本程序中类间方差用sb表示，最大类间方差用fmax

<br>
<br>
<br>

# 部分 API 中文自翻 #
## cornerSubPix ##
函数声明：void cornerSubPix(InputArray **image**, InputOutputArray **corners**, 
                                 Size **winSize**, Size **zeroZone**, TermCriteria **criteria**)
***
参数： 
* **image** - 输入图像
* **corners** - 初始的输入的角点的坐标以及即将输出的精确的角点坐标
* **winSize** - 搜索窗口的边长的一半。比如，如果winSize = Size(5, 5)，则使用 (5 * 2 + 1)*(5 * 2 + 1) = 11 * 11的搜索窗口
* **zeroZone** - 用来防止可能出现的自相关矩阵的奇异性（？）。将其设为(-1, -1)则表明不考虑这种情况。
* **criteria** - 角点精确化这一迭代过程的结束标准。也就是说，角点精确化的过程在迭代次数达到criteria.Count后 或者 在某一迭代中，角点的位置变化小于 criteria.epsilon后结束。
***
该函数通过迭代来找到角点或者辐射状马鞍点的亚像素精度的位置，如下图所示： <br> <br>
![Theory Picture](http://docs.opencv.org/2.4/_images/cornersubpix.png) <br> <br>
亚像素精度角点的位置基于这样一个定律：每个从中点q指向q的相邻点p的向量，总是垂直于图像在p点处的梯度。参考以下表达式：<br><br>

![Expression](http://docs.opencv.org/2.4/_images/math/99868eeda69186014cffde8171ef01ad86bc4ff1.png) <br> <br>

其中![Expression](http://docs.opencv.org/2.4/_images/math/a45e037d69d59bd0f1e7202894937f170948e1bc.png)是一幅图像在q点的邻近点pi处的梯度。q的值是使![Expression](http://docs.opencv.org/2.4/_images/math/6033306b5e4cd36f340e0542a0b05b9856dd714c.png)最小的时候所取的值。将![Expression](http://docs.opencv.org/2.4/_images/math/6033306b5e4cd36f340e0542a0b05b9856dd714c.png)设为0，便可得到一个方程组:<br><br>
![Expression](http://docs.opencv.org/2.4/_images/math/1cff760afbc0b01d8b0e4edf2d70c97de971d8a5.png)<br><br>
在这个表达式中，q附近的点的梯度值累加。令第一个多项式为G,第二个多项式为b,则有<br> <br>
![Expression](http://docs.opencv.org/2.4/_images/math/2d3e33b08cd6e9bd54495679bed2ca7470af91d3.png)<br><br>

然后该算法将邻近窗口（搜索窗口）的中点设为新的中点q点的位置并继续迭代直到该点稳定在给定的阈值中。
***
***
## morphologyEx ##
对图像进行形态学变换。
***
函数声明： C++: void morphologyEx(InputArray **src**, OutputArray **dst**, int **op**, InputArray **kernel**, Point **anchor**=Point(-1,-1), int **iterations**=1, int **borderType**=BORDER_CONSTANT, const Scalar& **borderValue**=morphologyDefaultBorderValue() )
***
参数：
* **src** - 输入图像。图像的通道数随意，但是图像深度必须是 CV_8U, CV_16U, CV_16S, CV_32F, CV64F中的一个。
* **dst** - 目标图像。其尺寸与类型和源图像相同。
* **element** - （形态学）结构元素
* **op** - 形态学变换的类型。可以使以下的几种之一。<br>
    + MORPH_OPEN - 开运算（先腐蚀再膨胀）
    + MORPH_CLOSE - 闭运算（先膨胀再腐蚀）
    + MORPH_GRADIENT - 形态梯度（膨胀图和腐蚀图之差） 
    + MORPH_TOPHAT - 顶冒（原图和开运算图之差）
    + MORPH_BLACKHAT - 黑帽（闭运算和原图之差）
    + MORPH_HITMISS - 击中击不中运算（？）
* **iterations** - 腐蚀或膨胀的次数
* **borderType** - 像素外差值的方法
* **borderValue** - 在borderType = BORDER_CONSTANT的情况下border的值。