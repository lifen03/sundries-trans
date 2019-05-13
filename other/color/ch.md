# [颜色：从十六进制编码到眼球](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0)

[原文地址](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0)

作者：[Jamie Wong](http://jamie-wong.com/)@创作时间：2018-04-03  

翻译&校验：freedom

![Performance](http://jamie-wong.com/images/color/Hero.png)

这篇文章也有俄语版本：[Цвет：отшестнадцатеричныхкодовдоглаза](https://habr.com/post/353582/)，和日语：色：[ヘキサコードから眼球まで](https://postd.cc/color/)。

为什么我们能感觉到```background-color: #9B51E0```是特定的紫色？

![Performance](http://jamie-wong.com/images/color/Purple.png)

这是很早我认为我已经知道答案的问题之一，但是在我审视我对这个问题的认知时，我意识到这里面存在相当大的误解。

通过对电磁辐射，光学生物学，比色法和显示硬件的探索，我希望开始填补其中的一些空白。 如果你想跳过，这里是我们将要涉及到的领域：

+ [电磁辐射](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#electromagnetic-radiation)
+ [可见光](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#visible-light)
+ [人类感知亮度](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#human-perceived-brightness)
+ [量化颜色](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#quantifying-color)
+ [光学生物学](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#optical-biology)
+ [色彩空间](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#color-spaces)
+ [Wright＆Guild的配色实验](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#wright-guild-s-color-matching-experiments)
+ [可视化色彩空间和色度](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#visualizing-color-spaces-chromaticity)
+ [色域和光谱轨迹](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#gamuts-and-the-spectral-locus)
+ [CIE XYZ色彩空间](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#cie-xyz-color-space)
+ [屏幕子像素](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#screen-subpixels)
+ [sRGB](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#srgb)
+ [sRGB十六进制码](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#srgb-hexcodes)
+ [伽玛校正](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#gamma-correction)
+ [从十六进制编码到眼球](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#from-hexcodes-to-eyeballs)
+ [关于亮度设置的简要说明](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#a-brief-note-about-brightness-setting)
+ [补充](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#stuff-i-left-out)
+ [参考](http://jamie-wong.com/post/color/?from=groupmessage&isappinstalled=0#references)

现在，让我们从物理学开始吧。

## 电磁辐射

无线电波，微波，红外线，可见光，紫外线，X射线和伽马射线都是电磁辐射的各种形式。 虽然这些东西都有不同的名称，但这些名称实际上只标记了电磁波谱内的不同波长范围。

![Performance](http://jamie-wong.com/images/color/electromagneticSpectrum.png)

*电磁波谱*

最小的电磁辐射单位是光子。 光子中包含的能量与其相应波的频率成比例，高能光子对应于高频波。

要真正理解颜色，我们需要先了解辐射。 让我们仔细看看白炽灯泡的辐射。

![Performance](http://jamie-wong.com/images/color/incandescent.png)

*照片来自[Alex Iby](https://unsplash.com/photos/HfR0W6HW_Cw)*

如果我们想知道每个波长有多少能量，我们可以看一下**光谱通量**。光谱通量


对象是每秒发射的总能量，以瓦特为单位测量。 100W白炽灯泡的辐射通量约为80W，其余20W直接转换为非辐射的热量。

如果我们将白炽灯泡的光谱通量绘制为波长的函数，它可能看起来像这样：

![Performance](http://jamie-wong.com/images/color/SpectralFlux1.png)

The area under this curve will give the radiant flux. As an equation, 

. In this case, the area under the curve will be about 80W.