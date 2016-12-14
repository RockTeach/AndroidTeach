# Shader #
<p>
   　　Shader是在View绘制过程中的提供横向颜色变换的对象的一个基类，它通过Paint的setShader方法设置上去，在绘制过程中可以从我们的Shader中获取颜色（对Bitmap无效）<br/>
   　　官网是上面那样介绍的，其实翻译为我们自己话就是，Shader是渲染器的基类，渲染器可以在我们绘制的时候提供颜色变换，在绘制Bitmap的时候无效。
</p>

## Shader的子类 ##
>* LinearGradient
>* RadialGradient
>* SweepGradient
>* BitmapShader
>* ComposeShader

## LinearGradient ##
- Linear 线性的，Gradient 倾斜的，有斜度的
- 组合起来就是线性变换的渲染器，实际就是我们常说的线性渐变色
- 有两种构造方式
  - <code>
  LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1,TileMode tile)
  </code>
  <br/>
  参数挺多，但是不要紧张，我们一个一个来看<br/>
   - x0 x的起始坐标
   - y0 y的起始坐标
   - x1 x的结束坐标
   - x2 y的结束坐标
   - color0 渐变开始的颜色
   - color1 渐变结束的颜色
   - TileMode 下面统一介绍

  - <code>
  LinearGradient(float x0, float y0, float x1, float y1, int colors[], float positions[],TileMode tile)
  </code>
  <br/>
  一样的参数就不介绍了<br/>
    - colors[] 渐变的颜色数组，按数组顺序渐变
    - positions[] 可能为null，null的话颜色均分，如果设置的话，数组长度需要和颜色数组长度一致，数组中的值 0-1的float
- 其实辐射方向是start(x0,y0)--end(x1,y1)连起来的方向

## RadialGradient ##
- Radial 放射状的
- 圆环扩散，类似于水波纹
- 有两种构造方式
  - <code>
  RadialGradient(float centerX, float centerY, float radius,int centerColor, int edgeColor, TileMode tileMode)
  </code>
    - centerX 扩散圆心X
    - centerY 扩散圆心Y
    - radius  扩散圆的半径
    - centerColor 从圆心向外扩散，中心的颜色
    - edgeColor  扩散到边缘的时候颜色
    - TileMode 后面统一说
  - <code>
  RadialGradient(float centerX, float centerY, float radius,int colors[],float stops[], TileMode tileMode)
  </code>
    - colors 和上面的差不多，辐射的时候颜色数组，按数组的坐标排颜色
    - stops 可以设置为null，为null的时候均分，如果设置的话需要和颜色数组的长度保持一致，取值0-1的float



## SweepGradient ##
- Sweep 扫视，扫射
- 扫射的颜色扩散，类似于雷达扫描的样子，但默认不是圆的
- 同样有两种构造形式
  - <code>
      SweepGradient(float cx, float cy, int color0, int color1)
  </code>
    - cx 中心x的坐标
    - cy 中心y的坐标
    - color0 开始的颜色，以水平向右为0，顺时针为开始
    - color1 结束的颜色
  - <code>
      SweepGradient(float cx, float cy,int colors[], float positions[])
  </code>
    - colors 和上面的一样，多个颜色
    - positions 可以为null，为null的时候均分，不为null的时候，数组长度需要和颜色数组长度一致，0-1的float取值


## BitmapShader ##
- 使用一张图片作为绘制时的颜色抽取，可以用这个方便的实现圆形，圆角图片，只需使用bitmap构建这个Shader，然后用画笔去画你想要的形状就行了
- 构造
  - <code>
      BitmapShader(Bitmap bitmap, TileMode tileX, TileMode tileY)
  </code>
    - bitmap 被用作抽取颜色的位图
    - tileX  X方向的模式
    - tileY  Y方向的模式


## ComposeShader ##
- compose 组合的
- 当你的需求比较复杂的时候，一个渲染器已经不能满足你了，你可能需要组合一下特效
- 两种构造
  - <code>
      ComposeShader(Shader shaderA, Shader shaderB, Xfermode mode)
  </code>
    - shaderA PorterDuff.Mode中的dst
    - shaderB PorterDuff.Mode中的src
    - Xfermode 这个这里先简单的说一下，就是绘制两次，绘制的两个图样到底是如何处理的，是取交，取并，A覆盖B还是B覆盖A等等
  - <code>
      ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode)
  </code>  
    - PorterDuff.Mode 和Xfermode用起来一样,Xfermode也是调用的这个

## TileMode ##
>* 枚举一个，有三个可选值
  - CLAMP 如果我们绘制的容器比咱们声明的大，会复制结尾的边缘进行填充
  - REPEAT 单纯的重复123123123...这种
  - MIRROR 镜像的 上下左右都是镜像
