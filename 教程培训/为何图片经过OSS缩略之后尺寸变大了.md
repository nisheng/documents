#             为何图片经过OSS缩略之后尺寸变大了？——影响不同格式图片文件大小的一些因素和实际示例

​                                                        

​    *摘要：*    OSS提供了基本的图片处理功能和图片格式之间的转换功能，在实际使用过程中，很多用户使用OSS将原图缩略之后输出，在这个过程中也出现了很多用户询问为何缩略之后图片尺寸变大，本文主要通过一些示例解释了这种现象出现的原因和排查方法。

# 简介

OSS提供了基本的图片处理功能和图片格式之间的转换功能，在实际使用过程中，很多用户使用OSS将原图缩略之后输出，在这个过程中也出现了很多用户询问为何缩略之后图片尺寸变大，如这个例子：

原图：[http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png](http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png?x-oss-process=image/resize,w_600)

处理之后：<http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png?x-oss-process=image/resize,w_600>

原图经过OSS从640x427 缩放到 600x400之后size反而变大了很多，这是为什么呢？

下面我们结合不同图片格式的一些特点分析一下为什么会出现这样的情况。

# 格式对比

图片目前是一种多种编码格式并存的情况。目前在网络上广泛使用的主流格式有JPEG、PNG和WEBP三种，其他的类似BMP、GIF、TIFF等使用较少，这里暂时不讨论。

JPEG作为一种最常见的图片格式，出现的最早，几乎已经成为网络上的标准格式。但是在实际使用上有一个最大的问题，那就是不支持透明通道。

PNG是一种无损压缩的图片格式，最大的优势是支持模式多，可以支持RGB、灰度、索引等各种格式，同时可以自由选择叠加透明通道或者指定透明背景颜色，因此在图片质量要求较高的时候或者需要支持透明通道的时候PNG是最好的选择。

WEBP是Google大力推广的一种图片格式。WEBP作为一种现代图片格式，不仅运用了很多新的压缩方式，达到在同等效果下比JPEG减小很多尺寸，而且支持透明通道、无损压缩等之前只有PNG才支持的一些特性，因此在很多方面也得到了广泛的运用。

三种格式的功能对比如下：

![image](https://yqfile.alicdn.com/ccb4f4db89628e141ffb1ee67a342f2c1c7054d0.png)

# 尺寸影响因素

对于同一张片来说，使用JPEG和WEBP，尺寸影响因素除了图片长宽之外，最主要就是压缩的质量参数。但是对于PNG来说，影响的因素非常多。

PNG的是无损压缩，基本原理是首先对图片使用差分编码，然后对编码之后的数据使用LZ77+Huffman编码进行压缩。因此PNG图片的大小受到以下这些因素的影响：

- 图片内容：因为重复性内容多的PNG图片会导致无法充分的压缩，因此文件大小会很大。
- 位深：PNG支持16bit和8bit的类型。
- 色彩空间：PNG支持24bit的真彩色、8bit的灰度、8bit的索引图像等类型，因此视觉效果相同的图片文件大小会非常悬殊。
- 透明通道：带透明通道的PNG图片会比不带的多出很多像素，另外，如果仅仅指定透明背景色对图片文件大小几乎无影响。

下面会用结合实际遇到的一些例子来说明这些因素如何影响图片文件大小。

## 例子1

首先我们分析最开头遇到的这个例子。

原图：[http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png](http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png?x-oss-process=image/resize,w_600)

处理之后：<http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png?x-oss-process=image/resize,w_600>

可见原图大小为127K，缩放之后为335K。

这种问题调查首先要确定原图和缩放之后的一些详细信息，这里我们使用ImageMagicK这个工具集中的identify命令来查看对应的信息。工具官网见<https://www.imagemagick.org/script/index.php>。

这里使用的命令是identify -verbose xxx.png。

原图的信息为：

```
 Format: PNG (Portable Network Graphics)
  Mime type: image/png
  Class: PseudoClass
  Geometry: 640x427+0+0
  Units: Undefined
  Type: Palette
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
```

再看处理之后的图片信息：

```
Format: PNG (Portable Network Graphics)
  Mime type: image/png
  Class: DirectClass
  Geometry: 600x400+0+0
  Units: Undefined
  Type: TrueColor
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
```

这里可以看出区别，那就是原图的Type为Palette，处理之后的图片为TrueColor，这就是最大的差别，因为原图为索引类型，处理之后的图片为RGB真彩色，因此需要编码的像素点个数是索引类型的三倍，因此导致图片变大。

这种类型是最常见的图片变大的原因，造成一种原因一个很重要的因素是使用了PNG优化工具如ImageAlpha等，这些工具会将真彩色的PNG图片降采样到256种颜色然后压缩成索引类型以减少图片大小，这是一个有损的过程。但是在缩放过程中，缩略的过程会根据原像素计算出新的像素值，因此缩放算法会导致结果图片生成的颜色数目超过256种从而无法压缩成索引图像。

为了确定这一点，我们也可以使用identify工具来查看颜色数目：

```
$ identify -verbose -unique http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png?x-oss-process=image/resize,w_600  |grep Colors
  Colorspace: sRGB
  Colors: 27080
$ identify -verbose -unique http://batchtest.oss-cn-hangzhou.aliyuncs.com/example1.png  |grep Colors
  Colorspace: sRGB
  Colors: 256
```

可见颜色数目由256增长到了27080个。

## 例子2

原图：<http://batchtest.oss-cn-hangzhou.aliyuncs.com/example2.png>

处理之后：<http://batchtest.oss-cn-hangzhou.aliyuncs.com/example2.png?x-oss-process=image/resize,w_600>

原图大小为283K，处理之后大小为335K。

首先还是按照上面一个例子的方法，检查一下图片格式信息。

这是原图的：

```
  Format: PNG (Portable Network Graphics)
  Mime type: image/png
  Class: DirectClass
  Geometry: 640x427+0+0
  Units: Undefined
  Type: Palette
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
```

这是处理之后的结果：

```
  Format: PNG (Portable Network Graphics)
  Mime type: image/png
  Class: DirectClass
  Geometry: 600x400+0+0
  Units: Undefined
  Type: TrueColor
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
```

可见，两者的压缩类型是完全一致的，那是什么原因导致的问题呢？

这里需要使用另外一个工具，pngthermal，链接见<https://encode.ru/threads/1725-pngthermal-pseudo-thermal-view-of-PNG-compression-efficiency>。

这个工具主要的功能是将PNG图片不同部分的压缩率使用可视化的方式展现出来，亮度越高的地方压缩率越低，我们针对两张图片用该工具分析的结果如下，上为原图，下为处理后图片：
![99106583A707EB41B11ECCC125C4F8C6](https://yqfile.alicdn.com/7577b006cbeab47cb39f849bbcb62ca03198f3e0.png)

![BF0DD7BABC83E65DD239A01286BB665B](https://yqfile.alicdn.com/547724df82c033c5469e58295152949022a4a5f9.png)

可见同样视觉效果的图片，压缩率差别很大，说明处理前的图片细节上重复较多，一般来说都是由于颜色数目导致的。为了确定这一点，使用上个例子的工具检查颜色数目，可以同样确定原图为256种颜色，处理后变成了27080种颜色。

其实本示例中的原图就是上一个例子的原图强制转换为RGB真彩色空间之后生成的。因为颜色只有256种，因此可供压缩的重复性信息较多，导致了处理后的图片比原图还大。

实际应用中，有很多美工生成的素材图片使用的颜色并不多，因此导致压缩率比较高，缩放之后就会出现类似的情况。

## 例子3

原图：<http://batchtest.oss-cn-hangzhou.aliyuncs.com/example3.png>

处理之后：<http://batchtest.oss-cn-hangzhou.aliyuncs.com/example3.png?x-oss-process=image/format,webp>

这里的示例是一个PNG转WEBP，原图大小为760字节，处理之后变成了1.3K。

首先还是使用identify工具查看两者的差别。

这是原图的：

```
  Format: PNG (Portable Network Graphics)
  Mime type: image/png
  Class: DirectClass
  Geometry: 92x34+0+0
  Units: Undefined
  Type: GrayscaleAlpha
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    gray: 8-bit
    alpha: 8-bit
```

这是处理之后的：

```
  Format: PAM (Common 2-dimensional bitmap format)
  Mime type: image/x-portable-pixmap
  Class: DirectClass
  Geometry: 92x34+0+0
  Units: Undefined
  Type: PaletteAlpha
  Base type: TrueColor
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
    alpha: 8-bit
```

这里可以看出一个非常明显的区别，原图是灰度图带上了透明通道，而处理之后的图片是RGB再带上透明通道。

原因是Webp并不支持灰度图带上透明通道这种类型，带上透明通道就将格式固定成了RGBA格式。因此导致了要保存的数据变大。

# 总结

从上文的例子可以看出，影响图片文件大小的因素很多，这里仅简单的列举了几个在应用中经常遇到的因素。

- 例子1：索引类型PNG缩略之后变成RGB类型PNG，像素增多导致文件变大。
- 例子2：颜色数目较小的PNG图片缩略之后颜色数目变多，压缩率下降导致文件变大。
- 例子3：带透明通道的灰度PNG转换成WEBP之后变成RGBA格式，像素增多导致文件变大。

可以看到大部分和预期结果不符合的都是PNG格式的图片。

一般来说，同样视觉效果的图片文件大小，webp<jpeg<png。但是PNG支持的编码类型非常的灵活，可以根据实际应用的需求自由调整，网上也有很多优化PNG图片大小的工具，因此往往出现图片处理之后破坏了优化效果，导致处理之后反而文件变大的情况。因此这里建议使用PNG图片上有体积大小要求的场景把体积优化放在最后一步，最好保存多份不同分辨率的PNG素材以供调用，不要对图片处理之后的图片文件大小有强预期。如果碰到了图片文件大小上的问题，也可以使用本文介绍的工具自己手动进行分析。