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
函数声明： void morphologyEx(InputArray **src**, OutputArray **dst**, int **op**, InputArray **kernel**, Point **anchor**=Point(-1,-1), int **iterations**=1, int **borderType**=BORDER_CONSTANT, const Scalar& **borderValue**=morphologyDefaultBorderValue() )
***
参数：
* **src** - 输入图像。图像的通道数随意，但是图像深度必须是 CV_8U, CV_16U, CV_16S, CV_32F, CV64F中的一个。
* **dst** - 目标图像。其尺寸与类型和源图像相同。
* **element** - （形态学）结构元素
* **op** - 形态学变换的类型。可以使以下的几种之一。<br>
    + MORPH_OPEN - 开运算（先腐蚀再膨胀）
    + MORPH_CLOSE - 闭运算（先膨胀再腐蚀）
    + MORPH_GRADIENT - 形态梯度（膨胀图和腐蚀图之差） 
    + MORPH_TOPHAT - 顶帽（原图和开运算图之差）
    + MORPH_BLACKHAT - 黑帽（闭运算和原图之差）
    + MORPH_HITMISS - 击中击不中运算（？）
* **iterations** - 腐蚀或膨胀的次数
* **borderType** - 像素外差值的方法
* **borderValue** - 在borderType = BORDER_CONSTANT的情况下border的值。
***
该函数利用腐蚀和膨胀作为基本运算来进行形态学运算。<br>
开运算（公式）：<br>
![](http://docs.opencv.org/2.4/_images/math/28abab30a249515f8d3adc12827c17dbabc8954b.png)<br>
闭运算：<br>
![](http://docs.opencv.org/2.4/_images/math/2d859333d4b0bdfe8ef8b2655b1212ffcbf37be2.png)<br>
形态梯度：<br>
![](http://docs.opencv.org/2.4/_images/math/ac94d7b594ef0528571597b0bf1f9f3c401b994b.png)<br>
顶帽：<br>
![](http://docs.opencv.org/2.4/_images/math/d269e3587e4d6dfc70fcedab8c32c24144fbf037.png)<br>
黑帽：<br>
![](http://docs.opencv.org/2.4/_images/math/dbe528ca1d36b71bac05626f7a53f6acae725323.png)
<br>

任何运算都可就地处理(in-place)。在多通道的情况下，每个通道都会被独立地处理。
***
***
## floodFill ##
用指定颜色填充一个连通域。
***
函数声明: int floodFill(InputOutputArray **image**, Point **seedPoint**, Scalar **newVal**, Rect* **rect**=0, Scalar **loDiff**=Scalar(), Scalar **upDiff**=Scalar(), int **flags**=4 )
***
参数：<br>
* **image** - 输入/输出一个单或三通道，八位或浮点图像。除非设立了FLOODFILL_MASK_ONLY标志位，否则该图像就会被修改。更多细节请往下看。
* **mask** - 掩码，它应该是一个比源图像宽与高都多两个像素的单通道八位图像。考虑到这是一个输入输出参数，使用者有责任初始化它。Flood-filling不能连通在mask中非零的像素。举个例子，边缘检测器的输出可以用来作为一个mask，以阻止填充边缘。当mask被输出时，mask中的与源图像被填充的像素相关的像素会被设为1，或者是参数flags制定的值。因此可以使用同一个mask多次调用这个函数来保证填充的区域没有重叠。
> **注意：** 因为mask比源图像大，所以在源图像中(x,y)对应于mask中的(x+1,y+1)
* **seedPoint** - 起始点。
* **newVal** - 连通域新的像素值。
* **loDiff** - 连通域中某个像素比其相邻像素（或者种子像素）最大高多少。
* **upDiff** - 连通域中某个像素比其相邻像素（或者种子像素）最大低多少。
* **rect** - 可选输出参数，是连通区域的最小包围矩形。
* **flags** - 操作标志。前八位包含一个连通性值。默认值4表示只有一个像素的四个最相近的像素会被使用。将其设为8代表一个像素的八个最相近的像素会被使用。后八位代表一个在0～255的像素值以用来填充mask，其默认值为1。举个例子，4 | ( 255 << 8 )表示使用一个像素的四个最相近的像素（来进行计算）并且用255来填充mask。以下的选项填充高八位所以也许会歌连通性值或者mask的填充值连用。<br>
    + **FLOODFILL_FIXED_RANGE** 如果使用了这个flag，那么会使用当前像素与种子像素的值之差，否则会使用当前与邻近像素之间的差值。
    + **FLOODFILL_MASK_ONLY** 如果立起了这个flag，这个函数就不会修改输入图像，并且只会用高八位的值来填充mask。只有当参数mask不为空时，这个flag才有意义。
***
函数floodFill用指定的颜色填充从seedPoint开始的连通域。连通性由某个像素周围的像素的颜色或亮度决定。如果满足以下条件，在(x,y)处的像素就属于会被重新绘制的区域:<br>
* ![](http://docs.opencv.org/2.4/_images/math/f5bc06ed04d2da7eb517bfb4e41409995cb93532.png)<br>
（灰度图像或者浮点图像）
* ![](http://docs.opencv.org/2.4/_images/math/f67280089e076019034831de12f606456569a28b.png)<br>
(灰度图像并且设立了FIX_RANGEflag)
* ![](http://docs.opencv.org/2.4/_images/math/7ee7c537a16acfb90ecb81c1959f59d8fe826407.png)<br> 
![](http://docs.opencv.org/2.4/_images/math/5fe3cd71145724afb461254d10fb82971a8e7f4d.png)<br>
![](http://docs.opencv.org/2.4/_images/math/595c0f6c89dc4a7ea836bc4fc55dfd74e8297300.png)<br>
(彩色图像并且立起了FiX_RANGE的flag)

这些公式中![](http://docs.opencv.org/2.4/_images/math/0d90013f209ff624c3808428cbc40422e6436f4d.png)是邻近像素值中的一个。也就是说，一个像素要想是在连通域里，该像素的值就应该足够接近：<br>
* 它已经在连通域中的邻近像素的值。
* 种子像素的值。
这个函数主要用来使用特定的颜色来标志连通域，或者建立一个mask并且扩展它的轮廓，或者拷贝一个区域到另一个图像里等等。
